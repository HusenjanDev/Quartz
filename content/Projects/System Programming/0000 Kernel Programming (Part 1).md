---
title: "Kernel Programming Fundamentals"
created: 2026-02-30
modified: 2026-02-30
tags: ["C++", "Driver"]
draft: false
---

## Process vs. Threads



## DriverEntry

```cpp title="driver.cpp"
#include "ntifs.h"
#include "ntddk.h"

// IOCTL
#define DEVICE_CKERNELDRIVER 0x80070
#define IOCTL_CKERNELDRIVER_EXAMPLE_ONE CTL_CODE(DEVICE_CKERNELDRIVER, 0x8001, METHOD_BUFFERED, FILE_ANY_ACCESS)
#define IOCTL_CKERNELDRIVER_EXAMPLE_TWO CTL_CODE(DEVICE_CKERNELDRIVER, 0x8002, METHOD_BUFFERED, FILE_ANY_ACCESS)
#define IOCTL_CKERNELDRIVER_EXAMPLE_THREE CTL_CODE(DEVICE_CKERNELDRIVER, 0x8002, METHOD_BUFFERED, FILE_ANY_ACCESS)

// Total execution counter
ULONG totalExecution = 0;

// Custom kernel driver structure
struct CKernelDriverStruct {
	WCHAR username[128];
	INT age;
};

// Complete request function (helper function)
NTSTATUS CompleteRequest(PIRP Irp, NTSTATUS status = STATUS_SUCCESS, ULONG_PTR info = 0) {
	// Preventing warnings
	UNREFERENCED_PARAMETER(Irp);
	UNREFERENCED_PARAMETER(status);
	UNREFERENCED_PARAMETER(info);

	// Stack information
	Irp->IoStatus.Status = status;
	Irp->IoStatus.Information = info;
	IoCompleteRequest(Irp, IO_NO_INCREMENT);
	return status;
}

// Unloading Kernel Driver
VOID CKernelDriverUnload(PDRIVER_OBJECT DriverObject) {
	// Prevent warnings
	UNREFERENCED_PARAMETER(DriverObject);

	// Deleting Symbolic Link Object and Device Object
	UNICODE_STRING SymbolicLinkName = RTL_CONSTANT_STRING(L"\\??\\CKernelDriver");
	IoDeleteSymbolicLink(&SymbolicLinkName);
	IoDeleteDevice(DriverObject->DeviceObject);
	KdPrint(("[+] Successfully unloaded the Symbolic Link Object and Device Object\n"));
}

// Create and Close...
NTSTATUS CKernelDriverCreateClose(PDEVICE_OBJECT DeviceObject, PIRP Irp) {
	// Prevent warnings
	UNREFERENCED_PARAMETER(DeviceObject);
	UNREFERENCED_PARAMETER(Irp);
	return CompleteRequest(Irp);
}

NTSTATUS CKernelDrvierDeviceControl(PDEVICE_OBJECT DeviceObject, PIRP Irp) {
	// Prevent warnings
	UNREFERENCED_PARAMETER(DeviceObject);
	UNREFERENCED_PARAMETER(Irp);

	// Getting stack
	auto irpSp = IoGetCurrentIrpStackLocation(Irp);
	auto& dic = irpSp->Parameters.DeviceIoControl;
	auto status = STATUS_INVALID_DEVICE_REQUEST;
	ULONG_PTR len = 0;

	switch (dic.IoControlCode) {
	case IOCTL_CKERNELDRIVER_EXAMPLE_ONE:
	{
		if (dic.OutputBufferLength < sizeof(CKernelDriverStruct)) {
			KdPrint(("[!] OutputBuffer is too small.\n"));
			status = STATUS_BUFFER_TOO_SMALL;
			break;
		}

		auto user_one = (CKernelDriverStruct*)Irp->AssociatedIrp.SystemBuffer;
		KdPrint(("[I] System Buffer (user_one->username) : %s\n", user_one->username));
		KdPrint(("[I] System Buffer (user_one->age) : %i\n", user_one->age));

		if (user_one == nullptr) {
			status = STATUS_INVALID_PARAMETER;
			break;
		}

		wcscpy_s(user_one->username, L"Ellie Lilly");
		user_one->age = 21;
		len = sizeof(CKernelDriverStruct);
		status = STATUS_SUCCESS;
		break;
	}
	case IOCTL_CKERNELDRIVER_EXAMPLE_TWO:
	{
		if (dic.OutputBufferLength < sizeof(ULONG)) {
			KdPrint(("[!] OutputBuffer is too small.\n"));
			status = STATUS_BUFFER_TOO_SMALL;
			break;
		}

		auto tExecution = (ULONG*)Irp->AssociatedIrp.SystemBuffer;
		totalExecution += 1;
		*tExecution = totalExecution;
		len = sizeof(totalExecution);
		KdPrint(("[+] System Buffer: %i\n", *tExecution));
		status = STATUS_SUCCESS;
		break;
	}
	}
	return CompleteRequest(Irp, status, len);
}

// Main function for Kernel Drvier
extern "C" NTSTATUS DriverEntry(PDRIVER_OBJECT DriverObject, PUNICODE_STRING RegistryPath) {
	// Prevent warnings from occurring because of no use
	UNREFERENCED_PARAMETER(DriverObject);
	UNREFERENCED_PARAMETER(RegistryPath);

	// Dispatch Routiness
	DriverObject->DriverUnload = CKernelDriverUnload;
	DriverObject->MajorFunction[IRP_MJ_CLOSE] = CKernelDriverCreateClose;
	DriverObject->MajorFunction[IRP_MJ_DEVICE_CONTROL] = CKernelDrvierDeviceControl;
	DriverObject->MajorFunction[IRP_MJ_READ] = CKernelDriverCreateClose;
	DriverObject->MajorFunction[IRP_MJ_WRITE] = CKernelDriverCreateClose;
	DriverObject->MajorFunction[IRP_MJ_CREATE] = CKernelDriverCreateClose;

	// Creating Symbolic Link and Device Object
	UNICODE_STRING DeviceName = RTL_CONSTANT_STRING(L"\\Device\\CKernelDriver");
	UNICODE_STRING SymbolicLinkName = RTL_CONSTANT_STRING(L"\\??\\CKernelDriver"); // \\\\.\\CKernelDriver
	PDEVICE_OBJECT DeviceObject = NULL;
	NTSTATUS status = STATUS_SUCCESS;
	bool sLinkCreated = false;

	do {
		status = IoCreateDevice(DriverObject, 0, &DeviceName, FILE_DEVICE_UNKNOWN, 0, FALSE, &DeviceObject);

		if (!NT_SUCCESS(status)) {
			KdPrint(("[!] Failed to create device\n"));
			break;
		}

		status = IoCreateSymbolicLink(&SymbolicLinkName, &DeviceName);

		if (!NT_SUCCESS(status)) {
			KdPrint(("[!] Failed to create symbolic link\n"));
			break;
		}

		sLinkCreated = true;
	} while (false);

	if (!NT_SUCCESS(status)) {
		if (sLinkCreated) {
			KdPrint(("[!] Deleting symbolic link\n"));
			IoDeleteSymbolicLink(&SymbolicLinkName);
		}
		if (DeviceObject) {
			KdPrint(("[!] Deleting device object\n"));
			IoDeleteDevice(DeviceObject);
		}
	}

	KdPrint(("[+] Successfully created Symbolic Link Object and Device Object\n"));
	return STATUS_SUCCESS;
}
```


```cpp title="client.cpp"
#include <iostream>
#include <Windows.h>
#include <wchar.h>

// IOCTL
#define DEVICE_CKERNELDRIVER 0x80070
#define IOCTL_CKERNELDRIVER_EXAMPLE_ONE CTL_CODE(DEVICE_CKERNELDRIVER, 0x8001, METHOD_BUFFERED, FILE_ANY_ACCESS)
#define IOCTL_CKERNELDRIVER_EXAMPLE_TWO CTL_CODE(DEVICE_CKERNELDRIVER, 0x8002, METHOD_BUFFERED, FILE_ANY_ACCESS)
#define IOCTL_CKERNELDRIVER_EXAMPLE_THREE CTL_CODE(DEVICE_CKERNELDRIVER, 0x8002, METHOD_BUFFERED, FILE_ANY_ACCESS)

// Data structure
struct CKernelDriverStruct {
	WCHAR username[128];
	INT age;
};

// Total Execution
static ULONG totalExecution = 0;

int main() {
	CKernelDriverStruct user_one;
	wcscpy_s(user_one.username, L"Jacob Looker");
	user_one.age = 19;

	printf("[#] Details\n");
	wprintf(L"[+] Username: %s\n", user_one.username);
	printf("[+] Age : %i\n\n", user_one.age);

	HANDLE hDevice = ::CreateFile(L"\\\\.\\CKernelDriver", GENERIC_READ | GENERIC_WRITE, 0, NULL, OPEN_EXISTING, 0, NULL);

	if (hDevice == INVALID_HANDLE_VALUE) {
		printf("[!] Failed to create handle\n");
		return -1;
	}

	if (DeviceIoControl(hDevice, IOCTL_CKERNELDRIVER_EXAMPLE_ONE, &user_one, sizeof(user_one), &user_one, sizeof(user_one), nullptr, nullptr)) {
		printf("[#] Details\n");
		wprintf(L"[+] Username: %s\n", user_one.username);
		printf("[+] Age : %i\n\n", user_one.age);
	}

	if (DeviceIoControl(hDevice, IOCTL_CKERNELDRIVER_EXAMPLE_TWO, nullptr, 0, &totalExecution, sizeof(totalExecution), nullptr, nullptr)) {
		printf("[#] Technical Details\n");
		printf("[+] Total Execution : %i\n", totalExecution);
	}

	return 0;
}
```