# Enablement Guid

Integrating advanced logger depends on the platform capabilities. Integration will differ depending
on how memory is handled in the system, the capabilities of the hardware and the libraries that
and provided by the silicon.

## Integration step overview for X86/X64 where memory is not initially available

1. Determine what boot phases will be supported by advanced logger and include the DebugLib instances
for those phases in the platform.

2. Find a suitable SerialPortLib instance for the platform serial hardware. This can be BaseSerialPortLibNull
if serial will not be supported on the platform. An ideal SerialPortLib instance will contain
a minimum number of dependenct libraries.

3. If Sec will be supported, and the system will use Cache As Ram:

    1; Determine the Cache As Ram base address that the system will use for memory and set the PCD
    `gAdvLoggerPkgTokenSpaceGuid.PcdAdvancedLoggerBase` to that value. This can usualy be determined
    by examining the SecCore code value passed in `TempRamBase` to the `SecStartup` function.

    Advanced logger will place a pointer at this address that points to the logger internal
    information, so be aware that other silicon code may trample this pointer, and require
    an adjustment to not use the first 16 bytes of this memory.

    2; Advanced logger will find an unused variable range mtrr and configure it to use
    as the log buffer until memory becomes available. Determine an unused range of memory
    that can be configured as the log buffer address and set it to
    `gAdvLoggerPkgTokenSpaceGuid.PcdAdvancedLoggerCarBase`

    3; Configure the number of pages of "memory" that can be used by for the advanced
    logger for cache as ram. This requires investigation in the processor capabilities.
    Once determined, set `gAdvLoggerPkgTokenSpaceGuid.PcdAdvancedLoggerPreMemPages` to
    that number of pages. Please ensure that `gAdvLoggerPkgTokenSpaceGuid.PcdAdvancedLoggerCarBase`
    plus the number of pages of memory does infringe on other resouces.

4. Configure the number of pages that the advanced logger will consume after memory becomes
available. Set this value to `gAdvLoggerPkgTokenSpaceGuid.PcdAdvancedLoggerPages`

## Integration step overview for memory available at the start of PEI

1. Determine what boot phases will be supported by advanced logger and include the DebugLib instances
for those phases in the platform.

2. Find a suitable SerialPortLib instance for the platform serial hardware. This can be BaseSerialPortLibNull
if serial will not be supported on the platform. An ideal SerialPortLib instance will contain
a minimum number of dependenct libraries.

3. Set PCD `gAdvLoggerPkgTokenSpaceGuid.PcdAdvancedLoggerPeiInRAM|TRUE` to enable the PEI_CORE
instance of advanced logger to allocate the full log space at the start of PEI.

## Copyright

Copyright (C) Microsoft Corporation. All rights reserved.  
SPDX-License-Identifier: BSD-2-Clause-Patent
