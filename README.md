# Module Structure for RISCV ISA Sail Models
This repository provides a module structure for [`sail` models](https://github.com/riscv/sail-riscv) specifying the RISC-V ISA.

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
The `.sail_project` file in this repository provides a dependency structure based on [the Reading Guide](https://github.com/riscv/sail-riscv/blob/master/doc/ReadingGuide.md).  

Each module consists of a sequence of `sail` files treated as a logical unit. If a module requires definitions from another module, it declares a dependency using the `requires` statement. 

The following `sail` files are excluded due to conflicts:

- **riscv_flen_D.sail** (double-precision floating-point extension)  
  - **Reason:** Conflicts with `riscv_flen_F.sail` (single-precision floating-point extension)  
  - **Conflict:** Both declare `type flen_bytes`

- **riscv_step_rvfi.sail**  
  - **Reason:** Conflicts with `riscv_step.sail`  
  - **Conflict:** Both declare `function ext_fetch_hook`

- **riscv_fetch_rvfi.sail**  
  - **Reason:** Conflicts with `riscv_fetch.sail`  
  - **Conflict:** Both declare `function fetch`
