# Oxide Computer Research Notes

This is a work in progress, to collect my research notes from trying to understand the work Oxide Computer is doing to create a new computer. This is collected entirely from my reading of publicly available materials, primarily their GitHub repositories. So keep in mind that it could be completely wrong.

Note: These are notes, not a final product. I have tried to quote as I go, but there is some copy-paste right now that I need to properly reference.


## Overview

- Very ethical and transparent. 
	- https://oxide.computer/about/
	- https://oxide.computer/blog/compensation-as-a-reflection-of-values/

- Create a rack-computer, that makes hyper-scale available to on-perm deployments
- Build an open source and verifiable hardware root of trust.
- Tightly integrate the hardware and software stack.


[Stanford Seminar - The Soul of a New Machine: Rethinking the Computer ](https://youtu.be/vvZA9n3e5pc)
> The openEDA movement has made FPGA design and implementation easier than ever, e.g. Yosys, Chisel, SpinalHDL, Bluespec


## Secure World
- Hardware Root of Trust
- Mostly working with commonly available FPGA development boards

### Hardware

#### LPC55
[LPC55S6X](https://www.nxp.com/products/processors-and-microcontrollers/arm-microcontrollers/general-purpose-mcus/lpc5500-cortex-m33/high-efficiency-arm-cortex-m33-based-microcontroller-family:LPC55S6x)
> leverages the new Armv8-M architecture to introduce new levels of performance and advanced security capabilities including TrustZone-M and co-processor extensions. The LPC55S6x family enables these co-processors extensions and leverages them to bring significant signal processing efficiency gains from a proprietary DSP accelerator offering a 10x clock cycle reduction. An optional second Cortex-M33 core offers flexibility to balance high performance and power efficiency.

[NXP LPC55 family of ARM Cortex-M33 processors](https://www.nxp.com/products/processors-and-microcontrollers/arm-microcontrollers/general-purpose-mcus/lpc5500-cortex-m33/high-efficiency-arm-cortex-m33-based-microcontroller-family:LPC55S6x) 
[LPCXpresso55S69](https://www.nxp.com/design/development-boards/lpcxpresso-boards/lpcxpresso55s69-development-board:LPC55S69-EVK)

![](https://www.nxp.com/assets/images/en/dev-board-image/LPC55S69-EVK.png)

- [Exploiting Undocumented Hardware Blocks in the LPC55S69](https://oxide.computer/blog/lpc55/) Perhaps indicating a change here.

![](https://www.nxp.com/assets/images/en/block-diagrams/LPC55S6x-BD.png)

#### LPC-Link2
[lpc-link2:OM13054](https://www.nxp.com/design/microcontrollers-developer-resources/lpc-microcontroller-utilities/lpc-link2:OM13054) 
> stand-alone debug probe that can be configured to support various development tools and IDEs
> It can also be used as an evaluation board in its own right for the NXP® LPC4370 triple core MCU.
> reports as several USB interfaces. Interface 1 is the CMSIS-DAP port; interface 4 is the SWO port.
> Interface name: LPC-LINK2 DATA PORT

![LPC-LINK2](https://www.nxp.com/assets/images/en/photography/lpc-link-2.jpg)

####  [probe-rs](https://github.com/oxidecomputer/probe-rs)
- forked from [probe-rs/probe-rs](https://github.com/probe-rs/probe-rs)
	- Fixes for CMSIS-DAP v2 and SWO/SWV
- provide a toolset to interact with a variety of embedded MCUs and debug probes.
- see [lpc-link2-re][lpc-link2-re]

### Other

[ULX3S](https://radiona.org/ulx3s/)
![](https://radiona.org/ulx3s/assets/img/legend.png)
[iCEstick](https://www.latticesemi.com/icestick)
> USB thumb drive form factor evaluation board

![](https://www.latticesemi.com/-/media/LatticeSemi/Images/ProductImages/DevelopmentKitsAndBoards/iCEstick/icestick-reva-side-2400.ashx)

[ECP5](https://www.latticesemi.com/products/developmentboardsandkits/ecp5evaluationboard)
> More I/O access – With 178 I/O, 20 differential pair I/O, four 5G SERDES channels and 85K LUTs available for user-defined applications, the ECP5 Evaluation Board allows designers to better investigate and experiment with the features of ECP5 FPGA family.

![](https://www.latticesemi.com/-/media/LatticeSemi/Images/ProductImages/DevelopmentKitsAndBoards/ECP5EvaluationBoard/LFE5UM5G-85F-EVN-revB-top.ashx)

### FPGA Toolchain
[MITHRO RUNS DOWN OPEN SOURCE FPGA TOOLCHAINS](https://hackaday.com/2020/03/06/mithro-runs-down-open-source-fpga-toolchains/)
> When you code for an FPGA, words you type get turned into a bitstream of ones and zeroes that flip perhaps a few million switches inside the chip. Going from a higher-level language to a bitstream is a lot like compiling normal programming languages, except with the twist that the resulting computational logic doesn’t map straight into a machine language, but rather into lower-level physical hardware on the FPGA. So “compilation” for FPGAs involves two steps: synthesis and place-and-routing. Synthesis takes the higher-level language that you write and turns it into a set of networks and timing requirements that represent the same logic, and can work across chip families. Yosys is the open-source synthesis tool of choice here.
> Place-and-route then maps the program logic into the individual FPGA’s architecture and feature set
> nextpnr takes the program logic and maps it into FPGA gates, and it can’t do this without detailed descriptions of the various chips’ internals.

- Bluespec Language 
	- functions
	-  modules 
- build (cobble)
	- "Compilation" 
		- Synthesis (yosys)
		- Place & Route (nextnpr)
	- Flash (target-gen)
		- Hardware
		-  Bluespec Simulator

#### [cobble](https://github.com/cbiffle/cobble-build/compare/master...oxidecomputer:main)
	- Fork of a simple build system for modular software
	- Used by Cobalt to build [Bluespec](https://github.com/B-Lang-org/bsc) interfaces and modules
	- Added support for parsing and creating [TOML](https://en.wikipedia.org/wiki/TOML)

#### Bluespec 
[Hardware Description Language](https://en.wikipedia.org/wiki/Hardware_description_language)
> hardware description language (HDL) is a specialized computer language used to describe the structure and behavior of electronic circuits, and most commonly, digital logic circuits.


#### [oxidecomputer/cobalt](https://github.com/oxidecomputer/cobalt)
> Cobalt is a collection of Bluespec interfaces and modules, used by Oxide Computer to implement custom logic for its new computer. Since we developed some of these pieces on readily available development boards using a fully open source synthesis toolchain, we figured this work and the implementation of its build system may serve as practical examples for others on how to use Bluespec in their own projects.

#### [yosys](https://github.com/YosysHQ/yosys)
	- Synthesis tool.
#### nextpnr
	- Portable FPGA place and route tool

![](https://camo.githubusercontent.com/3da7cc866a411e9f1552fae0ac2a455e9ac1dc7dfb6227b56a789ade0ff18e29/68747470733a2f2f692e696d6775722e636f6d2f3073706d6c42612e706e67)

#### target-gen
	- forked
	- allows for flashing ARM chips

####  [pyOCD](https://github.com/oxidecomputer/pyOCD)
	- Python package for programming and debugging Arm Cortex-M microcontrollers using multiple supported types of USB debug probes


### Rust
#### [oxidecomputer/lpc55-pac](https://github.com/oxidecomputer/lpc55-pac) 
> The purpose of this crate is to give embedded programs or libraries written in Rust access to the complete functionality of LPC55 microcontrollers.
> See also the higher-level companion library [LPC55 HAL][lpc55-hal]
> The API documentation is located at <https://docs.rs/lpc55-pac>.
> To gain an understanding of `svd2rust`-generated APIs, checkout documentation at <https://docs.rs/svd2rust>.

- forked from [lpc55/lpc55-pac](https://github.com/lpc55/lpc55-pac)
	- Derive USB0 from USB1

#### [oxidecomputer/lpc-link2-re](https://github.com/oxidecomputer/lpc-link2-re)
> reverse engineered minimal specification of the LPC-Link2's USB interface for capturing SWO trace data
> The LPC-Link reports as several USB interfaces. Interface 1 is the [CMSIS-DAP](https://os.mbed.com/handbook/CMSIS-DAP) port; interface 4 is the SWO port.
> CMSIS-DAP provides a standardized way to access the Coresight Debug Access Port (DAP) of an ARM Cortex microcontroller via USB.

[Single Wire Output (SWO)](https://software-dl.ti.com/ccs/esd/documents/xdsdebugprobes/emu_swo_trace.html) 
> supports profiling hardware events such as periodic sampling of program counter, data variable reads and writes, interrupt entry and exit, counters as well as application generated software messages.

[Tutorial: Using Single Wire Output SWO with ARM Cortex-M and Eclipse](https://mcuoneclipse.com/2016/10/17/tutorial-using-single-wire-output-swo-with-arm-cortex-m-and-eclipse/)

![CMSIS-DAP](https://os.mbed.com/media/uploads/samux/cmsis-dap-6.png)

### Boot
#### [cortex-m-rt ](https://github.com/oxidecomputer/cortex-m-rt)
- forked
> Startup code and minimal runtime for Cortex-M microcontrollers 

#### [tock](https://github.com/oxidecomputer/tock)
- forked
> embedded operating system designed for running multiple concurrent, mutually distrustful applications on Cortex-M and RISC-V based embedded platforms. Tock's design centers around protection, both from potentially malicious applications and from device drivers.
-  written in Rust

##### [tockilator](https://github.com/oxidecomputer/tockilator)

> Rust program that consumes the output from the tracer module of an Ibex RISC-V core running under Verilator along with an ELF file (or ELF files) that correspond to the running software (e.g., boot loader, operating system and application) and symbolically interprets the instruction trace to provide a view of execution flow.

#### BIOS
##### [oxidecomputer/coreboot](https://github.com/oxidecomputer/coreboot)
- forked from [rminnich/coreboot](https://github.com/rminnich/coreboot)

[coreboot](https://github.com/coreboot)
> Open Source Firmware for computers (x86, ARM)

##### [serialice](https://github.com/coreboot/serialice)
- forked from [coreboot/serialice](https://github.com/coreboot/serialice)
	- added AMD booting, AsRock EPYCD8, 500 baud serial


### debug
	- Possible some of these are used for communicating with the x86 main board?

#### [DAPlink](https://github.com/oxidecomputer/DAPLink)

> enables programming and debugging application software running on Arm Cortex CPUs. Commonly referred to as interface firmware, DAPLink runs on a secondary MCU that is attached to the SWD or JTAG port of the application MCU. This configuration is found on nearly all development boards. Enumerating as a USB composite device, it creates a bridge between your development computer and the CPU debug access port. DAPLink enables developers with:

> - MSC - drag-n-drop programming flash memory
> - CDC - virtual com port for log, trace and terminal emulation
> - HID - CMSIS-DAP compliant debug channel
> - WEBUSB HID - CMSIS-DAP compliant debug channel 
> DAPLink has superseded the mbed CMSIS-DAP interface firmware project 

#### cortex-m-semihosting
- forked
> Semihosting is a mechanism that enables code running on an ARM target to communicate and use the Input/Output facilities on a host computer that is running a debugger.

#### serial-rs
- forked
- Rust interfaces for working with serial ports

## Normal World

### Hardware
[This little server startup wants to take on a horde of tech giants](https://www.protocol.com/oxide-computer-cloud-server)
> Oxide's mission is to create a server using standard x86 processors from companies like Intel or AMD that strips away a lot of the extraneous software that ships with a mainstream server, giving customers a "clean sheet" designed for their operating system, infrastructure, and application requirements.

### [oxidecomputer/dropshot](https://github.com/oxidecomputer/dropshot)

- exposing REST APIs from a Rust program
- Used By:
	- propolis
	- cio

### OS (illumos)
### [illumos-gate](https://github.com/oxidecomputer/illumos-gate)
- forked
- core illumos source tree

#### VMs (bhyve)

#####  [ch-oxidase](https://github.com/oxidecomputer/ch-oxidase)
- port of [Cloud Hypervisor](https://github.com/cloud-hypervisor/cloud-hypervisor) to run on Illumos and the Bhyve kernel space, instead of Linux and KVM.

##### [oxidecomputer/propolis](https://github.com/oxidecomputer/propolis)
> rust-based userspace for illumos bhyve.
> While Propolis is intended to expose a REST API to drive all of its functionality, a CLI binary using a static toml configuration is the basis for the initial prototype.
> Propolis will create a unix domain socket, available at "./ttya", which acts as a serial port. (Console)

- Defines VM configs
- OVMF disk

##### [bhyve-api](https://github.com/oxidecomputer/bhyve-api)

- A Rust library interface to Bhyve's kernel ioctl interface

## [vmm-sys-util](https://github.com/rust-vmm/vmm-sys-util)
- forked from [rust-vmm/vmm-sys-util](https://github.com/rust-vmm/vmm-sys-util)
	- added some illumos support 
> build custom Virtual Machine Monitors (VMMs) and hypervisors.


#### dtrace
##### [oxidecomputer/usdt](https://github.com/oxidecomputer/usdt)

- exposes statically-defined DTrace probes to Rust code. Users write a provider definition as usual, in a D language script. The provider's probes may then be compiled into Rust code that fires the probes.
- Bridge to let Rust use DTrace probes
- D Lang -> .rs file builder
- [D Language](https://docs.oracle.com/en/operating-systems/oracle-linux/dtrace-guide/dt_dlang.html#dt_comp_dlang)

![D Trace](https://docs.oracle.com/en/operating-systems/oracle-linux/dtrace-guide/images/dtrace.png)

#### containers (zones)
##### [Zone](https://github.com/oxidecomputer/zone)
- library for creating and managing illumos zones.

#### services
#####  [SMF](https://github.com/oxidecomputer/smf)
- provides a Rust interface to the [Solaris service management facility (SMF)](https://en.wikipedia.org/wiki/Service_Management_Facility) system, the illumos model for services and service management
- commands such as "svcs", "svcadm", "svccfg", and "svcprop".

##### [libscf-sys](https://github.com/oxidecomputer/libscf-sys)
- Rust bindings for [libscf](https://www.illumos.org/man/3LIB/libscf) on illumos, interface for reading, writing, and manipulating service configurations

#### file system
##### [fs3-rs](https://github.com/oxidecomputer/fs3-rs)
- Extended utilities for working with files and filesystems in Rust


##### [gosigar](https://github.com/oxidecomputer/gosigar)
- Forked, added illumos file system list support.
- golang implementation of Sigar, "a free software library (under the Apache License) that provides a cross-platform, cross-language programming interface to low-level information on computer hardware and operating system activity."


### Orchestration
#### [oxidecomputer/steno](https://github.com/oxidecomputer/steno)

- [Distributed Sagas as described by Caitie McAffrey](https://www.youtube.com/watch?v=0UTOLRTwOX0)
- Prototype implementation of "Sagas", protocol for coordinating micro services.
- Defines an Action, Failed|Canceled, Undo

### database
#### [cockroach](https://github.com/oxidecomputer/cockroach)
- forked from [cockroachdb/cockroach](https://github.com/cockroachdb/cockroach)
- distributed SQL database
- [Cockroach](https://cockroachlabs.com/docs/stable/)

# Other
##  [interval_future](https://github.com/oxidecomputer/interval_future)
> Implements a wrapper around non-async functions, allowing them to be used within an async context.

## RustCrypto_signatures, elliptic-curves, RustCrypto_traits, group

## [serde_tokenstream](https://github.com/oxidecomputer/serde_tokenstream)
> "intended for use with macros that need bespoke configuration"
