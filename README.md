# Module Structure for RISCV ISA Sail Models
This repository provides a dependency structure for [Sail models](https://github.com/riscv/sail-riscv) specifying the RISC-V ISA.

## Why?
While compiling `sail` files, the order matters. If `file2.sail` depends on definitions from `file1.sail`, compiling them in the wrong order will result in an error: <br />

```
sail file2.sail file1.sail
Type error: Unknown type level operator or function
```

To avoid this, dependencies must be compiled first:<br />

```sail file1.sail file2.sail```

In large projects like Sail-RISCV, determining a correct order manually is challenging. The module structure in this repository ensures each `sail` file is compiled only after its dependencies, maintaining a valid compilation sequence. 

## Sail Project
This `.sail_project` file organises `sail` files into modules, ensuring properdependency resolution based on the [Reading Guide](https://github.com/riscv/sail-riscv/blob/master/doc/ReadingGuide.md) and [Modular Sail Specifications Guide](https://github.com/rems-project/sail/blob/sail2/doc/asciidoc/modules.adoc).

Each module consists of a sequence of `sail` files treated as a logical unit. If a module requires definitions from another module, it declares a dependency using the `requires` statement. 

### Exluded Files
The following `sail` files are excluded:

- **riscv_flen_D.sail** (double-precision floating-point extension)  
  - **Reason:** Defines `type flen_bytes` which clashes with `riscv_flen_F.sail` (single-precision floating-point extension)

- **riscv_step_rvfi.sail**  
  - **Reason:** Defines `function ext_fetch_hook` which clashes with `riscv_step.sail`

- **riscv_fetch_rvfi.sail**  
  - **Reason:** Defines `function fetch` which clashes with `riscv_fetch.sail` 

- **rvfi_dii.sail**
  - **Reason:** Other RVFI (RISC-V Formal Interface) files above were excluded

## Module Dependencies
- **Prelude** (Core types and definitions)
- **RegTypes** (Core register types)
  - Requires: Prelude
- **Regs** (General-purpose and system registers)
  - Requires: Prelude, RegTypes
- **PMP** (Physical Memory Protection)
  - Requires: Prelude, RegTypes, Regs
- **PrivilegeTransition** (Privilege transitions and exceptions)
  - Requires: Prelude, RegTypes, Regs, PMP
- **PhysicalMemory** (Handling physical memory)
  - Requires: Prelude, RegTypes, Regs, PMP, PrivilegeTransition
- **VirtualMemory** (Page tables and virtual memory translation)
  - Requires: Prelude, RegTypes, Regs, PrivilegeTransition, PhysicalMemory
- **Misc** (Miscellaneous extensions and interfaces)
  - Requires: Prelude, RegTypes, Regs, PrivilegeTransition, PhysicalMemory
- **Instructions** (Instruction set definitions)
  - Requires: Prelude, RegTypes, Regs, PrivilegeTransition, PhysicalMemory, VirtualMemory, Misc
- **Main** (Core execution logic)
  - Requires: Prelude, RegTypes, Regs, PrivilegeTransition, PhysicalMemory, VirtualMemory, Instructions

### Module Dependency Graph 
```mermaid
graph LR;
    RegTypes -->|depends on| Prelude;
    Regs -->|depends on| Prelude;
    PMP -->|depends on| Prelude;
    PrivilegeTransition -->|depends on| Prelude;
    PhysicalMemory -->|depends on| Prelude;
    VirtualMemory -->|depends on| Prelude;
    Misc -->|depends on| Prelude;
    Instructions -->|depends on| Prelude;
    Main -->|depends on| Prelude;
    
    Regs -->|depends on| RegTypes;
    PMP -->|depends on| RegTypes;
    PrivilegeTransition -->|depends on| RegTypes;
    PhysicalMemory -->|depends on| RegTypes;
    VirtualMemory -->|depends on| RegTypes;
    Misc -->|depends on| RegTypes;
    Instructions -->|depends on| RegTypes;
    Main -->|depends on| RegTypes;

    PMP -->|depends on| Regs;
    PrivilegeTransition -->|depends on| Regs;
    PhysicalMemory -->|depends on| Regs;
    VirtualMemory -->|depends on| Regs;
    Misc -->|depends on| Regs;
    Instructions -->|depends on| Regs;
    Main -->|depends on| Regs;

    PrivilegeTransition -->|depends on| PMP;
    PhysicalMemory -->|depends on| PMP;

    PhysicalMemory -->|depends on| PrivilegeTransition;
    VirtualMemory -->|depends on| PrivilegeTransition;
    Misc -->|depends on| PrivilegeTransition;
    Instructions -->|depends on| PrivilegeTransition;
    Main -->|depends on| PrivilegeTransition;

    VirtualMemory -->|depends on| PhysicalMemory;
    Misc -->|depends on| PhysicalMemory;
    Instructions -->|depends on| PhysicalMemory;
    Main -->|depends on| PhysicalMemory;

    Instructions -->|depends on| VirtualMemory;
    Main -->|depends on| VirtualMemory;

    Instructions -->|depends on| Misc;
    Main -->|depends on| Misc;

    Main -->|depends on| Instructions;

```

 
## Usage
1. Install [Sail](https://github.com/rems-project/sail)
2. Download `sail-riscv-master` from [RISCV Sail Model repository](https://github.com/riscv/sail-riscv/tree/master) and build the RISC-V model.
3. Download the `.sail_project` file in this repository. Move it to the `sail-riscv-master/model` folder.
4. On the Command Line Interface, navigate to the `sail-riscv-master/model` folder and invoke `sail` as follows using your desired options: `sail <options> riscv.sail_project`
     For example, for type checking: `sail riscv.sail_project`
                  for C compilation: `sail -c riscv.sail_project`



