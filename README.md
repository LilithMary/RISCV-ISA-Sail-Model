# Module Structure for RISCV ISA Sail Models
This repository provides a dependency structure for [Sail models](https://github.com/riscv/sail-riscv) specifying the RISC-V ISA.

## Why?
When compiling `sail` files, the order matters. For example, if `file1.sail` imports a library or contains a definition used by `file2.sail`, compiling them in the wrong order will cause an error: <br />

```
sail file2.sail file1.sail
Type error: Unknown type level operator or function
```

To avoid this, dependencies must be compiled first:<br />

```sail file1.sail file2.sail```

In large projects like Sail-RISCV, determining a correct order can be challenging. The module structure in this repository ensures each `sail` file is compiled only after its dependencies, maintaining a valid compilation sequence. 

## Sail Project
This `.sail_project` file provides a dependency structure based on [the Reading Guide](https://github.com/riscv/sail-riscv/blob/master/doc/ReadingGuide.md) by organising `sail` files into modules and declaring module dependencies according to [Modular Sail Specifications Guide](https://github.com/rems-project/sail/blob/sail2/doc/asciidoc/modules.adoc).

Each module consists of a sequence of `sail` files treated as a logical unit. If a module requires definitions from another module, it declares a dependency using the `requires` statement. 

The following `sail` files are excluded:

- **riscv_flen_D.sail** (double-precision floating-point extension)  
  - **Reason:** Conflicts with `riscv_flen_F.sail` (single-precision floating-point extension)  
  - **Conflict:** Both declare `type flen_bytes`

- **riscv_step_rvfi.sail**  
  - **Reason:** Conflicts with `riscv_step.sail`  
  - **Conflict:** Both declare `function ext_fetch_hook`

- **riscv_fetch_rvfi.sail**  
  - **Reason:** Conflicts with `riscv_fetch.sail`  
  - **Conflict:** Both declare `function fetch`

- **rvfi_dii.sail**
  - **Reason:** Other RVFI (RISC-V Formal Interface) files above were excluded
 
## Usage
1. Install [Sail](https://github.com/rems-project/sail)
2. Download `sail-riscv-master` from [RISCV Sail Model repository](https://github.com/riscv/sail-riscv/tree/master) and build a RISC-V simulator.
3. Download the `.sail_project` file in this repository and move it to the `sail-riscv-master/model` folder.
4. On the Command Line Interface, navigate to the `sail-riscv-master/model` folder and invoke `sail` as follows using your desired options: `sail <options> riscv.sail_project`

### Type Checking
`sail riscv.sail_project`

### C Compilation
`sail -c riscv.sail_project`



