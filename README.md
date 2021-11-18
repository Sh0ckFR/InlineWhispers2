# InlineWhispers2
Tool for working with Direct System Calls in Cobalt Strike's Beacon Object Files (BOF) via [Syswhispers2](https://github.com/jthuraisamy/SysWhispers2)

Based on https://github.com/outflanknl/InlineWhispers and https://github.com/helpsystems/nanodump work

### Note ###

This project is not a fork, the first version of InlineWhispers is based on SysWhispers, this version is for the second version of SysWhispers, to understand what it changes, take a look at it:

[https://github.com/jthuraisamy/SysWhispers2#difference-between-syswhispers-1-and-2](https://github.com/jthuraisamy/SysWhispers2#difference-between-syswhispers-1-and-2)

## How do I set this up?

```bash
git clone https://github.com/Sh0ckFR/InlineWhispers2 && cd InlineWhispers2
git clone https://github.com/jthuraisamy/SysWhispers2
cd SysWhispers2/ && python3 syswhispers.py --preset all -o syscalls_all && cd ..
python3 InlineWhispers2.py
```

## How to use syscalls in your Cobalt-Strike BOF?

Import syscalls.c syscalls.h, syscalls-asm.h in your project and include syscalls.c to start to use syscalls

Now you can use all syscalls that you need:

```c
#include <windows.h>
#include <stdio.h>
#include <tlhelp32.h>

#include "beacon.h"

#include "syscalls.c"

int go(char* args, int length) {
	datap  parser;
	BeaconDataParse(&parser, args, length);

	int pid = BeaconDataInt(&parser);

	BeaconPrintf(CALLBACK_OUTPUT, "	- Opening process: %d.", pid);

	HANDLE hProcess = NULL;
	OBJECT_ATTRIBUTES ObjectAttributes;
	InitializeObjectAttributes(&ObjectAttributes, NULL, 0, NULL, NULL);

	CLIENT_ID uPid = { 0 };
	uPid.UniqueProcess = (HANDLE)(DWORD_PTR)pid;
	uPid.UniqueThread = (HANDLE)0;

	NTSTATUS status = NtOpenProcess(&hProcess, PROCESS_ALL_ACCESS, &ObjectAttributes, &uPid);
	if (hProcess == NULL || status != 0) {
		BeaconPrintf(CALLBACK_OUTPUT, "	[ERROR] Failed to get processhandle, status: 0x%lx", status);
		return 0;
	}
	BeaconPrintf(CALLBACK_OUTPUT, "	- Handle: %x", hProcess);

	NtClose(hProcess);

	return 0;
}
```

## Limitations

Actually, you can't use `NtCallEnclave, NtGetCachedSigningLevel, NtSetCachedSigningLevel, NtCreateSectionEx` syscalls

## Credits

* [@jthuraisamy](https://github.com/jthuraisamy) for Syswhispers2
* [@outflanknl](https://github.com/outflanknl) for the first version of InlineWhispers
* [@helpsystems](https://github.com/helpsystems) for the nanodump exemple
* [@boku7](https://github.com/boku7) for his awesome work and his kindness
* [@HackingDave](https://github.com/HackingDave) because he's the owner of a great DeLorean *vroom vroom*
* The French Read The Fancy Manual community, the CyberThreatForce, and OsintFr ([@sigsegv_event](https://twitter.com/sigsegv_event) [@CTFofficielFR](https://twitter.com/CTFofficielFR) and [@OsintFr](https://twitter.com/OsintFr))
* All infosec enthusiasts who share their knowledge without looking down on other enthusiasts
