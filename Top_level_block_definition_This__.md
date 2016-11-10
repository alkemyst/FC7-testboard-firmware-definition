#Top-level block definition
This should be configurable so that we can read-out either one or more modules (in case of optical interface).
It relies on the standard FC7 system firmware for: IPbus, Memory access, etc.

For the purpose of this document we define **modes** as working modes that can be switched at run time and **implementations** as working modes that can be switched at compilation time. not all of the working modes or implementations described here are needed for the initial version of the firmware to be functional. in particular: the **standard mode** is required (but not the **hit counting**) and only the implementations dealing with electrical, non-CIC, 2×CBC2 hybrid is needed first.

Hybrid and readout blocks should implement two modes of operation: **standard mode** and **hit counting mode** and they should be configured via the same register to avoid mismatches.

##Physical access block
This block has two (main) **implementations**. **Optical implementation**: wrapping around N×GBT-FPGA blocks connecting to a COTS FMC with a number of SFP+ cages and **Electrical implementation** connecting to the custom-made interface FMC. Each custom-made FMC interface will need a different implementation of the Physical access block.

The electrical access block implements also an I²C master and presents the same interface as the optical to the hybrid block.

Interface to the hybrid block: (t.b.d.)