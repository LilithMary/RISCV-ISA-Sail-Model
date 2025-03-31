# Module Structure for RISCV ISA Sail Models

Module structure for [Sail models](https://github.com/riscv/sail-riscv) specifying the RISC-V ISA.

This project ensures that each Sail file is compiled only after its dependencies have been compiled. 
In other words, we determine a compilation order that respects the dependencies among Sail files.

Each module consists of a sequence of Sail files treated as a logical unit. 
If a module requires definitions from another module, it declares a dependency using the "requires" statement.

The dependency structure is loosely based on [the Reading Guide](https://github.com/riscv/sail-riscv/blob/master/doc/ReadingGuide.md).

The following Sail files are excluded due to conflicts:

- **riscv_flen_D.sail** (Double-precision floating-point extension)  
  - **Reason:** Conflicts with `riscv_flen_F.sail` (Single-precision floating-point extension)  
  - **Conflict:** Both declare `type flen_bytes`

- **riscv_step_rvfi.sail**  
  - **Reason:** Conflicts with `riscv_step.sail`  
  - **Conflict:** Both declare `function ext_fetch_hook`

- **riscv_fetch_rvfi.sail**  
  - **Reason:** Conflicts with `riscv_fetch.sail`  
  - **Conflict:** Both declare `function fetch`
