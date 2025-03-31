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

## Module Dependency Graph

```mermaid
graph LR;
    Prelude <--|depends on| RegTypes;
    Prelude <--|depends on| Regs;
    Prelude <--|depends on| PMP;
    Prelude <--|depends on| PrivilegeTransition;
    Prelude <--|depends on| PhysicalMemory;
    Prelude <--|depends on| VirtualMemory;
    Prelude <--|depends on| Misc;
    Prelude <--|depends on| Instructions;
    Prelude <--|depends on| Main;
    
    RegTypes <--|depends on| Regs;
    RegTypes <--|depends on| PMP;
    RegTypes <--|depends on| PrivilegeTransition;
    RegTypes <--|depends on| PhysicalMemory;
    RegTypes <--|depends on| VirtualMemory;
    RegTypes <--|depends on| Misc;
    RegTypes <--|depends on| Instructions;
    RegTypes <--|depends on| Main;

    Regs <--|depends on| PMP;
    Regs <--|depends on| PrivilegeTransition;
    Regs <--|depends on| PhysicalMemory;
    Regs <--|depends on| VirtualMemory;
    Regs <--|depends on| Misc;
    Regs <--|depends on| Instructions;
    Regs <--|depends on| Main;

    PMP <--|depends on| PrivilegeTransition;
    PMP <--|depends on| PhysicalMemory;
    
    PrivilegeTransition <--|depends on| PhysicalMemory;
    PrivilegeTransition <--|depends on| VirtualMemory;
    PrivilegeTransition <--|depends on| Misc;
    PrivilegeTransition <--|depends on| Instructions;
    PrivilegeTransition <--|depends on| Main;

    PhysicalMemory <--|depends on| VirtualMemory;
    PhysicalMemory <--|depends on| Misc;
    PhysicalMemory <--|depends on| Instructions;
    PhysicalMemory <--|depends on| Main;
    
    VirtualMemory <--|depends on| Instructions;
    VirtualMemory <--|depends on| Main;

    Misc <--|depends on| Instructions;
    Misc <--|depends on| Main;

    Instructions <--|depends on| Main;

```

 
## Usage
1. Install [Sail](https://github.com/rems-project/sail)
2. Download `sail-riscv-master` from [RISCV Sail Model repository](https://github.com/riscv/sail-riscv/tree/master) and build the RISC-V model.
3. Download the `.sail_project` file in this repository. Move it to the `sail-riscv-master/model` folder.
4. On the Command Line Interface, navigate to the `sail-riscv-master/model` folder and invoke `sail` as follows using your desired options: `sail <options> riscv.sail_project`
     For example, for type checking: `sail riscv.sail_project`
                  for C compilation: `sail -c riscv.sail_project`



