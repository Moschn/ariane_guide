# Ariane

Ariane is an open source 64bit in-order RISC-V core written in SystemVerilog that implements the `RV64IMC` instuction set. `RV64IMC` stands for the RISC-V 64bit **I**nteger **M**ultiplication **C**ompressed instruction set. The **A**tomic and (**D**ouble precision) **F**loating point extension are currently underway. The goal is to fully support the complete RV64G (RV64IMAFD) instruction set. 

For people interested in running linux ontop of ariane: The major roadblock is the atomic extension. Currently ariane is unable to run linux. A full implementation of the atomic extension is underway and will hopefully allow for ariane-based systems to boot linux.

## The Core itself (based on the `ariane_next` branch)
The `ariane_next` branch has a lot less small nits. The `AXI_BUS` module has been moved to the `src/` directory, and the `ariane_wrapped.sv` has been renamed to `ariane_testharness.sv` and moved to the `tb/` directory. 

Also the core now incooperates more peripherals:
  - The `CLINT` (Core-local Interrupt Controller) is a timer that supports the RISC-V priviledge sped v1.11. It requires a real time clock input (`rtc_i`) and it keeps state of the current time for the core.
  - The debug interface is directly integrated and connected to the core itself 

## The Core itself (based on the master branch)

Most of the required source files reside in `src/`. The only exception is the `AXI_BUS` interface that is in `tb/agents/axi_if/axi_if.sv`. The `src/` directory also contains `ariane_wrapped.sv` which is part of the testbench and not the core itself. 

The toplevel module of the core resides in `ariane.sv` and is called `ariane`. 

Type | Name | Description
--- | --- | ---  
input logic | clk_i | Clock
input logic | rst_ni | Reset
input logic | test_en_i | enable all clock gates for testing
input logic | flush_dcache_i | external request to flush data cache
output logic | flush_dcache_ack_o | finished data cache flush
|| *CPU Control* ||
input logic | fetch_enable_i | start fetching data
|| *CPU Configuration* ||
input logic [63:0] | boot_addr_i | reset boot address
input logic [ 3:0] | core_id_i | core id in a multicore environment
input logic [ 5:0] | cluster_id_i | PULP specific if core is used in a clustered environment
|| *Memory Interfaces* ||
AXI_BUS.Master | instr_if | Instruction cache interface
AXI_BUS.Master | data_if | Data cache interface
AXI_BUS.Master | bypass_if | Bypass interface __(?)__
|| *Interrupt inputs* ||
input logic [1:0] | irq_i | level sensitive IR lines, mip & sip
input logic | ipi_i | inter-processor interrupts
output logic | sec_lvl_o | current privilege level out
|| *Timer facilities* ||
input  logic [63:0] | time_i | global time (most probably coming from an RTC)
input  logic | time_irq_i | timer interrupt in
|| *Debug Interface* ||
input  logic | debug_req_i | 
output logic | debug_gnt_o |
output logic | debug_rvalid_o |
input  logic [15:0] | debug_addr_i |
input  logic | debug_we_i |
input  logic [63:0] | debug_wdata_i |
output logic [63:0] | debug_rdata_o |
output logic | debug_halted_o |
input  logic | debug_halt_i |
input  logic | debug_resume_i |

## IPs 

The core relies on two external IPs for its internal L1 caches: 
  - data/instruction cache (Currently `256x128`)
  
    This cache is used to store the actual data/instructions.
  - tag cache (Currently `256x46`)

    The tag cache store metadata about the current cachelines

The sizes of the caches can easily be changed in `include/nbdcache_pkg.sv` for the data cache and in __?__ for the instruction cache.

The ariane repository does __not__ contain any IP for these blocks (besides a minimal example FPGA designflow). Instead it emulates the caches in the simulation. For an ASIC/FPGA flow one needs to generate the SRAM/blockram instances. 

## Verilator Testbench
The verilator testbench resides in `tb/`. 
