---
title: "Kernel Drivers Handling I/O Data"
created: 2026-03-10
modified: 2026-03-10
tags: ["C++", "KERNEL"]
draft: true
---

## Kernel Mode

```cpp
#include "pch.h"

#define DRIVER 0x8010
#define IOCTL_ADD_PROCESS CTL_CODE(DRIVER, 0x8001, METHOD_BUFFERED, FILE_ANY_ACCESS)

LIST_ENTRY g_ProcessListHead;

struct ProcessList {
	LIST_ENTRY ListEntry;
	UNICODE_STRING ProcessName;
};

VOID DriverUnload(PDRIVER_OBJECT DriverObject);
NTSTATUS CompleteRequest(PIRP Irp, NTSTATUS status, NTSTATUS info);
NTSTATUS DriverCreateClose(PDEVICE_OBJECT DeviceObject, PIRP Irp);
NTSTATUS InsertProcess(UNICODE_STRING ProcessName);
NTSTATUS DriverControl(PDEVICE_OBJECT DeviceObject, PIRP Irp);

extern "C" NTSTATUS DriverEntry(PDRIVER_OBJECT DriverObject, PUNICODE_STRING RegistryKey) {
	UNREFERENCED_PARAMETER(DriverObject);
	UNREFERENCED_PARAMETER(RegistryKey);

	UNICODE_STRING symName		= RTL_CONSTANT_STRING(L"\\??\\LKernel");
	UNICODE_STRING devName		= RTL_CONSTANT_STRING(L"\\Device\\LKernel");
	PDEVICE_OBJECT deviceObject = NULL;
	NTSTATUS status				= NULL;

	status = IoCreateDevice(DriverObject, NULL, &devName, FILE_DEVICE_UNKNOWN, NULL, NULL, &deviceObject);

	if (!NT_SUCCESS(status)) {
		KdPrint(("[!] Failed to intialize deivce object.\n"));
	}

	status = IoCreateSymbolicLink(&symName, &devName);

	if (!NT_SUCCESS(status)) {
		KdPrint(("[!] Failed to initalize symbolic link.\n"));
	}

	DriverObject->DriverUnload							= DriverUnload;
	DriverObject->MajorFunction[IRP_MJ_DEVICE_CONTROL]	= DriverControl;
	DriverObject->MajorFunction[IRP_MJ_CREATE]			= DriverCreateClose;
	DriverObject->MajorFunction[IRP_MJ_READ]			= DriverCreateClose;
	DriverObject->MajorFunction[IRP_MJ_CLOSE]			= DriverCreateClose;

	InitializeListHead(&g_ProcessListHead);

	return STATUS_SUCCESS;
}

NTSTATUS CompleteRequest(PIRP Irp, NTSTATUS status = STATUS_SUCCESS, NTSTATUS info = 0) {
	Irp->IoStatus.Status = status;
	Irp->IoStatus.Information = info;
	IoCompleteRequest(Irp, IO_NO_INCREMENT);
	return status;
}

VOID DriverUnload(PDRIVER_OBJECT DriverObject) {
	UNREFERENCED_PARAMETER(DriverObject);

	while (!IsListEmpty(&g_ProcessListHead)) {
		LIST_ENTRY* e = RemoveHeadList(&g_ProcessListHead);
		ProcessList* entry = CONTAINING_RECORD(e, ProcessList, ListEntry);
		KdPrint(("[-] Removing - ProcessName: %ws\n", entry->ProcessName.Buffer));

		if (entry->ProcessName.Buffer) {
			ExFreePoolWithTag(entry->ProcessName.Buffer, 'c0rp');
		}

		ExFreePoolWithTag(entry, 'c0rp');
	}

	UNICODE_STRING symName = RTL_CONSTANT_STRING(L"\\.\\LKernel");
	IoDeleteSymbolicLink(&symName);
	IoDeleteDevice(DriverObject->DeviceObject);
}

NTSTATUS DriverCreateClose(PDEVICE_OBJECT DeviceObject, PIRP Irp) {
	UNREFERENCED_PARAMETER(DeviceObject);
	UNREFERENCED_PARAMETER(Irp);
	return CompleteRequest(Irp);
}

NTSTATUS InsertProcess(UNICODE_STRING ProcessName) {
	ProcessList* entry = (ProcessList*)ExAllocatePool2(POOL_FLAG_PAGED, sizeof(ProcessList), 'c0rp');

	if (!entry) {
		KdPrint(("[!] Failed to allocate ProcessList in InsertProcess()\n"));
		return STATUS_INSUFFICIENT_RESOURCES;
	}

	entry->ProcessName.Buffer = (PWCH)ExAllocatePool2(POOL_FLAG_PAGED, ProcessName.Length, 'c0rp');
	
	if (!entry->ProcessName.Buffer) {
		KdPrint(("[!] Failed to allocate entry->ProcessName.Buffer in InsertProcess()\n"));
		return STATUS_INSUFFICIENT_RESOURCES;
	}

	entry->ProcessName.MaximumLength = ProcessName.Length;
	RtlCopyUnicodeString(&entry->ProcessName, &ProcessName);
	
	KdPrint(("[+] Adding - ProcessName: %ws\n", entry->ProcessName.Buffer));

	InsertTailList(&g_ProcessListHead, &entry->ListEntry);

	return STATUS_SUCCESS;
}

NTSTATUS DriverControl(PDEVICE_OBJECT DeviceObject, PIRP Irp) {
	UNREFERENCED_PARAMETER(DeviceObject);
	UNREFERENCED_PARAMETER(Irp);

	auto irp = IoGetCurrentIrpStackLocation(Irp);
	auto& dic = irp->Parameters.DeviceIoControl;

	switch (dic.IoControlCode) {
		case IOCTL_ADD_PROCESS: {
			if (dic.InputBufferLength == 0) {
				KdPrint(("[!] DriverControl->IOCTL_ADD_PROCESS : Invalid input buffer length\n"));
				return STATUS_INVALID_BUFFER_SIZE;
			}

			auto input = (WCHAR*)Irp->AssociatedIrp.SystemBuffer;

			if (input == NULL) {
				KdPrint(("[!] DriverControl->IOCTL_ADD_PROCES - The input variable is invalid"));
				return STATUS_INSUFFICIENT_RESOURCES;
			}

			UNICODE_STRING ProcessName;
			RtlCopyMemory(&ProcessName.Buffer, &input, dic.InputBufferLength);
			ProcessName.Length = (USHORT)dic.InputBufferLength;
			KdPrint(("[#] Reading Process Name: %ws\n", ProcessName.Buffer));

			InsertProcess(ProcessName);
		}
	}

	return CompleteRequest(Irp);
}
```

## Client Mode

```cpp
#include <Windows.h>
#include <iostream>
#include <vector>

#define DRIVER 0x8010
#define IOCTL_ADD_PROCESS CTL_CODE(DRIVER, 0x8001, METHOD_BUFFERED, FILE_ANY_ACCESS)

int main() {
	HANDLE hDevice = CreateFile(L"\\\\.\\LKernel", GENERIC_READ | GENERIC_WRITE, NULL, NULL, OPEN_EXISTING, NULL, NULL);

	if (hDevice == INVALID_HANDLE_VALUE) {
		printf("[!] hDevice is a invalid handle value.\n");
		return -1;
	}

	std::vector<std::wstring> m_Processes;

	m_Processes.push_back(L"brave.exe");
	m_Processes.push_back(L"cmd.exe");

	for (int i = 0; i < m_Processes.size(); ++i) {
		if (DeviceIoControl(hDevice, IOCTL_ADD_PROCESS, (LPVOID)m_Processes[i].c_str(), sizeof(WCHAR) * m_Processes[i].size(), NULL, NULL, NULL, NULL)) {
			printf("[+] Successfully sent %ws to kernel driver.\n", m_Processes[i].c_str());
		}
	}

	return 0;
}
```