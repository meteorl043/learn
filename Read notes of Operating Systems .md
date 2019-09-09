## Microsoft Windows Overview

### 1. Backgroud

**1985** First windows was published, for an operating environment
extension to the primitive MS-DOS operating system

**1993** Windows/MS-DOS was replaced by Windows NT, although the graphical user interface
is roughly as same as order system. there is completely new internal design on that.
The core of Windows NT consists of **the Kernel** and **the Executive**, including a number
of **object-oriented features**.

Windows 8 introducted major changes including **process management**, **virtual memory management** 
and  **touch-optimized shell** to platform and user interface

### 2. Architecture

> In total, windows separate application-oriented software from the core OS software
> 
> The core OS software includes **the Executive**, **the Kernel**, **device driver** 
> and **the hardware abstraction layer** 
> 
> attention: Kernel mode software has access to system data and to the hardware, but user mode software
> has limited access to system data

##### Operating System Organization
> Windows has a highly modular architecture, any module can be removed, upgraded or replaced without
> rewriting the entire system or its standard application program interfaces(APIs)

1. **The Kernel-Mode components** 
    
    + Executive: **the core OS sevices** such as memory management, process and thread management security, I/O
    and interprocess communication
    
    + Kernel: **control execution of the processors.** kernel manage thread scheduling, process switching, 
    exception and interrupt handing and multiprocessor synchronization. **Kernel's own code does not run in threads** 
    
    + Hardware abstraction layer(HAL): **Maps between generic hadware commands and responses and those unique to a specific 
    platform**. It isolates the OS from platform-specific hardware difference. The HAL makes each computer's system bus,
    direct memory access(DMA) controller, interrupt controller, system timer, and memory controller look the same to the Executive
    and the Kernel components
    
    + Device driver: Dynamic libraries that extend the functionality of the Executive. these includes 
    **hardware device driver translate user I/O function calls into specific hardware device I/O requests** and software
    components for implementing file systems, network protocol and any other system extension that run in kernel mode.
    
    + Windows and graphics system: **Implements the GUI fuctions**, such as dealing with windows, user interface, and drawing

2. **[Brief description of the Executive modules](#Executive)**

    + I/O managemer:       
        1. Provide a framework through which I/O devices are accessible to application
        2. response for dispatching to the approprivate device drivers for further processing
        3. implements all the Windows I/O APIs and enforces security and naming for devices, network protocols, and file systems(using the object manager)
    
    + Cache manager:
        1. imporve the performance of file-based I/O
        2. defer disk writes for a short time berfore sending them to the disk in more efficient batches

    + Object manager:
        1. Create, manage, and deletes Windows Executive objects.
        2. It enforces uniform rules for retaining, naming, and setting the security of objects
        3. Create the entries on each process's handle table, which consist of access control information and a pointer to the object
    
    + Plug-and-play manager: Determine which driver is required to support a particular device and loads these drivers
    + Power manager: coordinates power management among various devices and can be configured to reduce power consumption
    + Security reference monitor: **Enforce access-validation and audit-generation rules** 
    + Virtual meomory manager: 
        1. Manages virtual addresses, physical memory, and paging files on disk.
        2. Controls the memory management hardware and data structure which map virtual addresses in process's address space to
        physical pages in the computer's memory.
        
    + Process/thread manager: Create, manages, and deletes process and thread objects
    + Configuration manager: Responsible for implementing and managing the system registry, which is the repository for both
    system-wide and per-user setting of various parameters.
    + Advanced local procedure call(ALPC) facility: Implements an efficient cross-process procedure call mechanism for communication
    between local processes implementing services and sybsystems.
    
3. **User Mode Process**

    + Special System processes: User-mode services needed to manage the system such as the session manager, the authentication subsystem,
    the service maneger, and the logon process.
    
    + Service processes: Includes the printer spooler, the event logger, user-mode components that cooperate with device drivers, various
    network services, and many, many others. Services are used by both Microsft and external software developers to extend system funcionality
    as they are the only way to run background user-mode activity on a Windows system.
    
    + Environment subsystems: Provide different OS personalities(enviroments). The supported subsystems are WIN32 and POSIX. Each envirment 
    subsystem includes a subsystem process shared among all applications using the subsystem and dynamic link libraries(DLLs) that convert the 
    user application calls to ALPC calls on the subsystem process, and/or native Windows calls.
    
    + User application: Executables(EXEs) and DLLs that provide the functionality users to make use of the system.

### Client/Server Model


