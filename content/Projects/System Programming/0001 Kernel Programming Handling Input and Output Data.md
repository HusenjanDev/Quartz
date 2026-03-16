---
title: Kernel Drivers Handling I/O Data
created: 2026-03-20
modified: 2026-03-20
tags:
  - C++
  - KERNEL
draft: true
---
## Introduction

I recently have been learning about programming kernel drivers to dig further into the Windows operating system. I have learnt a lot about Windows while building kernel drivers and I decided to share that knowledge by building a simple kernel driver which handles input and outputs from the user-mode process. Let's get our hands dirty and dig into building a kernel driver.

## Symbolic Link, DeviceObject, and DriverObject

The **DeviceObject** and **Symbolic Link** will allow our user-mode process to communicate with the kernel driver while the **DriverObject** is responsible for handling requests and ensuring the correct operation is performed depending on the request.

A user-mode process must create a handle using the symbolic link and that will reference the device object which the user-mode process will use to perform operations using the driver object.

## Kernel Driver

I will be building a kernel driver which will allow the user-mode process to add and remove data structures from a linked list in the kernel driver. The data structure itself has a process name variable which we will use to remove processes from the linked list. In a future article I'll go through blocking process execution depending on the process name.

The source code for the kernel driver will be available at [Github](https://github.com/HusenjanDev/LKernel.git).

### IOCTL

The I/O Control Codes (IOCTL) is commonly used in kernel drivers to communicate with the kernel driver from the user-mode process. The kernel uses `IRP_MJ_DEVICE_CONTROL` to handle the different requests that come from the user-mode process.

```cpp title="IOCTL"
#define DRIVER 0x8010
#define IOCTL_ADD_PROCESS CTL_CODE(DRIVER, 0x8001, METHOD_BUFFERED, FILE_ANY_ACCESS)
#define IOCTL_REMOVE_PROCESS CTL_CODE(DRIVER, 0x8002, METHOD_BUFFERED, FILE_ANY_ACCESS)
```

The driver is assigned the device identifier number `0x8010` because Windows requires device type to be over `0x8000`. The IOCTLs are assigned the identifier number `0x8001` and `0x8002` but Windows only requires these to be over `0x800`. 

### Data Structures

The most common approach to handle data in kernel drivers is by using a linked list as it allows us to add and remove data from the memory which is crucial since if we fail to clean up the memory it could lead to failures in other components.

```cpp
LIST_ENTRY g_ProcessListHead;

struct ProcessList {
	LIST_ENTRY ListEntry;
	UNICODE_STRING ProcessName;
};
```

The `g_ProcessListHead` is the head of the linked list and the `ProcessList` is the data structure which will be stored inside of the linked list.

### Predefined Functions

The predefined functions will be used for different purposes such as unloading driver, completing requests, adding processes, removing processes, and iterating through these processes.

```cpp title="Predefined Functions"
VOID DriverUnload(PDRIVER_OBJECT DriverObject);
NTSTATUS CompleteRequest(PIRP Irp, NTSTATUS status, NTSTATUS info);
NTSTATUS DriverCreateClose(PDEVICE_OBJECT DeviceObject, PIRP Irp);
NTSTATUS InsertProcess(UNICODE_STRING ProcessName);
NTSTATUS DriverControl(PDEVICE_OBJECT DeviceObject, PIRP Irp);
NTSTATUS RemoveProcess(UNICODE_STRING ProcessName);
NTSTATUS IterateProcesses();
```

I'll be going deeper into these functions in the upcoming chapters so don't worry about their purposes right now. 

### Driver Entry

The `DriverEntry()` function is the entry point for the kernel driver and it's commonly used for initializing symbolic link, device object, and handling different requests using driver object. You can view the `DriverEntry()` as the `main()` entry point in user-mode applications.

```cpp title="DriverEntry"
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
		return STATUS_INVALID_ADDRESS;
	}

	status = IoCreateSymbolicLink(&symName, &devName);

	if (!NT_SUCCESS(status)) {
		IoDeleteDevice(deviceObject);
		KdPrint(("[!] Failed to initalize symbolic link.\n"));
		return STATUS_INVALID_ADDRESS;
	}

	DriverObject->DriverUnload							= DriverUnload;
	DriverObject->MajorFunction[IRP_MJ_DEVICE_CONTROL]	= DriverControl;
	DriverObject->MajorFunction[IRP_MJ_CREATE]			= DriverCreateClose;
	DriverObject->MajorFunction[IRP_MJ_READ]			= DriverCreateClose;
	DriverObject->MajorFunction[IRP_MJ_CLOSE]			= DriverCreateClose;

	InitializeListHead(&g_ProcessListHead);
	IterateProcesses();
	return STATUS_SUCCESS;
}
```

Inside the driver entry point the symbolic link and the device object is initialized and the driver object is used for handling IRP requests and when requests such as `IRP_MJ_CREATE`, `IRP_MJ_READ`, `IRP_MJ_CLOSE` and `IRP_MJ_DEVICE_CONTROL` occurs. The `IRP_MJ_CREATE` creates handle, `IRP_MJ_CLOSE` closes handle, `IRP_MJ_READ` reads data, and the `IRP_MJ_DEVICE_CONTROL` is used for IOCTL purposes. 

### Complete Request

The `CompleteRequest()` is a wrapper function which completes the I/O Request Packet (IRP). It simplifies the process by setting specific status and information fields and then it calls `IoCompleteRequest` to complete the request. 

```cpp title="Complete Request"
NTSTATUS CompleteRequest(PIRP Irp, NTSTATUS status = STATUS_SUCCESS, NTSTATUS info = 0) {
	Irp->IoStatus.Status = status;
	Irp->IoStatus.Information = info;
	IoCompleteRequest(Irp, IO_NO_INCREMENT);
	return status;
}

NTSTATUS DriverCreateClose(PDEVICE_OBJECT DeviceObject, PIRP Irp) {
	UNREFERENCED_PARAMETER(DeviceObject);
	UNREFERENCED_PARAMETER(Irp);
	return CompleteRequest(Irp);
}
```

The `DriverCreateClose` uses the `CompleteRequest()` function to complete IRP requests for `IRP_MJ_CREATE`, `IRP_MJ_READ`, and `IRP_MJ_CLOSE` which are commonly used by kernel drivers to perform specific operations such as establishing handle, releasing the handle, and reading data.

### Insert Process

The `InsertProcess()` function will create a new entry using the `ProcessList` data structure and then allocate memory for the `UNICODE_STRING` and set the maximum length and then push the data structure to the linked list.

```c++ title="InsertProcess()"
NTSTATUS InsertProcess(UNICODE_STRING ProcessName) {
	ProcessList* entry = (ProcessList*)ExAllocatePool2(POOL_FLAG_PAGED, sizeof(ProcessList), 'c0rp');

	if (!entry) {
		KdPrint(("[!] Failed to allocate ProcessList in InsertProcess()\n"));
		return STATUS_INSUFFICIENT_RESOURCES;
	}

	entry->ProcessName.Buffer = (PWCH)ExAllocatePool2(POOL_FLAG_PAGED, ProcessName.Length, 'c0rp');
	
	if (!entry->ProcessName.Buffer) {
		ExFreePoolWithTag(entry, 'c0rp');
		KdPrint(("[!] Failed to allocate entry->ProcessName.Buffer in InsertProcess()\n"));
		return STATUS_INSUFFICIENT_RESOURCES;
	}

	entry->ProcessName.MaximumLength = ProcessName.Length;
	RtlCopyUnicodeString(&entry->ProcessName, &ProcessName);
	
	KdPrint(("[+] Adding - ProcessName: %ws\n", entry->ProcessName.Buffer));

	InsertTailList(&g_ProcessListHead, &entry->ListEntry);

	return STATUS_SUCCESS;
}
```

### Remove Process

The `RemoveProcess()` function will remove a object from the linked list when it has the specific process name. An entry variable is made which is pointing to the head of the linked list and then it enumerates through the linked list to the process name we are searching after is found or to the loop is over.

```c++ title="RemoveProcess()"
NTSTATUS RemoveProcess(UNICODE_STRING ProcessName) {
	LIST_ENTRY* e = g_ProcessListHead.Flink;

	while (e != &g_ProcessListHead) {
		ProcessList* entry = CONTAINING_RECORD(e, ProcessList, ListEntry);
		LIST_ENTRY* next = e->Flink;

		if (RtlCompareUnicodeString(&entry->ProcessName, &ProcessName, TRUE) == 0) {
			RemoveEntryList(e);

			KdPrint(("[-] RemoveProcess - ProcessName : %ws\n", entry->ProcessName.Buffer));

			if (entry->ProcessName.Buffer) {
				ExFreePoolWithTag(entry->ProcessName.Buffer, 'c0rp');
			}

			ExFreePoolWithTag(entry, 'c0rp');
		}

		e = next;
	}

	return STATUS_SUCCESS;
}
```

### Iterate Processes

The `IterateProcesses()` function will be used to go through the linked list and print out the process name. I mainly made this for debugging purposes as it will allow us to view the data inside of our linked list.

```c++ title="IterateProcess()"
NTSTATUS IterateProcesses() {
	LIST_ENTRY* e = g_ProcessListHead.Flink;

	while (e != &g_ProcessListHead) {
		ProcessList* entry = CONTAINING_RECORD(e, ProcessList, ListEntry);
		KdPrint(("[#] IterateProcess - Process Name: %ws\n", entry->ProcessName.Buffer));
		e = e->Flink;
	}

	return STATUS_SUCCESS;
}
```

### DeviceControl

The `DeviceControl()` function is responsible for the I/O Control Codes (IOCTL) requests and this function will allow us to declare the way the kernel driver should respond to these IOCTL requests.

```cpp title="DeviceControl()"
NTSTATUS DriverControl(PDEVICE_OBJECT DeviceObject, PIRP Irp) {
	UNREFERENCED_PARAMETER(DeviceObject);
	UNREFERENCED_PARAMETER(Irp);

	auto irp = IoGetCurrentIrpStackLocation(Irp);
	auto& dic = irp->Parameters.DeviceIoControl;

	switch (dic.IoControlCode) {
		case IOCTL_ADD_PROCESS: {
			if (dic.InputBufferLength == 0) {
				KdPrint(("[!] DriverControl->IOCTL_ADD_PROCESS : Invalid input buffer length\n"));
				return CompleteRequest(Irp, STATUS_INVALID_BUFFER_SIZE);
			}

			auto input = (WCHAR*)Irp->AssociatedIrp.SystemBuffer;

			if (input == NULL) {
				KdPrint(("[!] DriverControl->IOCTL_ADD_PROCES: The input data is invalid"));
				return CompleteRequest(Irp, STATUS_INSUFFICIENT_RESOURCES);
			}

			UNICODE_STRING ProcessName;
			ProcessName.Buffer = input;
			ProcessName.Length = (USHORT)dic.InputBufferLength;
			ProcessName.MaximumLength = ProcessName.Length;
			KdPrint(("[#] Reading Process Name (ADDING): %ws\n", ProcessName.Buffer));

			InsertProcess(ProcessName);
			return CompleteRequest(Irp);
		}
		case IOCTL_REMOVE_PROCESS: {
			if (dic.InputBufferLength == 0) {
				KdPrint(("[!] Driver->IOCTL_REMOVE_PROCESS : Invalid input buffer\n"));
				return CompleteRequest(Irp, STATUS_INVALID_BUFFER_SIZE);
			}

			auto input = (WCHAR*)Irp->AssociatedIrp.SystemBuffer;

			if (input == NULL) {
				KdPrint(("[!] Driver->IOCTL_REMOVE_PROCESS : The input data is invalid"));
				return CompleteRequest(Irp, STATUS_INSUFFICIENT_RESOURCES);
			}

			UNICODE_STRING ProcessName;
			ProcessName.Buffer = input;
			ProcessName.Length = (USHORT)dic.InputBufferLength;
			ProcessName.MaximumLength = ProcessName.Length;
			KdPrint(("[#] Reading Process Name (REMOVAL): %ws\n", ProcessName.Buffer));

			RemoveProcess(ProcessName);
			return CompleteRequest(Irp);
		}
	}

	return CompleteRequest(Irp);
}
```

When the user-mode process uses the `IOCTL_ADD_PROCESS` a process is added into the linked list and when the user-mode process uses `IOCTL_REMOVE_PROCESS` the process is removed from the list.

### Driver Unloading

The driver unload function will be executed when the driver is going to be unloaded and this function is crucial to setup correctly since it will be responsible for deleting symbolic link, device object, and data that is on the memory pool. 

```cpp title="DriverUnload()"
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

	UNICODE_STRING symName = RTL_CONSTANT_STRING(L"\\??\\LKernel");
	IoDeleteDevice(DriverObject->DeviceObject);
	IoDeleteSymbolicLink(&symName);
}
```

All that is done inside of the driver unload function is the linked list data is cleaned up and the device object and symbolic object are deleted.

## User-Mode Process

The user-mode client will communicate with the kernel driver by creating a handle and then use that handle with the `DeviceIoControl` function using the IOCTL to call the operation that will allow it to add a process into the linked list inside of kernel driver. 

```cpp title="main.cpp"
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

The reason we are using a `std::vector` with `std::wstring` is because the kernel driver uses the UNICODE strings. Otherwise we would need to convert all the ASCII strings to UNICODE which requires additional code so I decided to simplify the process.

## Conclusion

Our simple kernel driver can now handle input and outputs and respond to IOCTL requests from the user-mode process. Additionally, the kernel driver also cleans up the memory before unloading itself. In future chapters I'll go through blocking processes from being executed using the process names from linked list.
