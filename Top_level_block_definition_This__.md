#Top-level block definition
This should be configurable so that we can read-out either one or more modules (in case of optical interface).
It relies on the standard FC7 system firmware for: IPbus, Memory access, etc.

For the purpose of this document we define **modes** as working modes that can be switched at run time and **implementations** as working modes that can be switched at compilation time. not all of the working modes or implementations described here are needed for the initial version of the firmware to be functional. in particular: the **standard mode** is required (but not the **hit counting**) and only the implementations dealing with electrical, non-CIC, 2×CBC2 hybrid is needed first.

Module and readout blocks should implement two modes of operation: **standard mode** and **hit counting mode** and they should be configured via the same register to avoid mismatches.

##Physical access block
This block has two (main) **implementations**. **Optical implementation**: wrapping around N×GBT-FPGA blocks connecting to a COTS FMC with a number of SFP+ cages and **Electrical implementation** connecting to the custom-made interface FMC. Each custom-made FMC interface will need a different implementation of the Physical access block.

The electrical access block implements also an I²C master and presents the same interface as the optical to the module block.

Interface to the module block: (t.b.d.)

##Module block
Module block
Transmits slow and fast commands
Receives and unpacks data
Syncs trigger and stub data
Can forward stub detection to fast command block
Sends data to the readout block
Can pre-process hits data for calibration (hit or stub counting)


CIC unpacker: (if needed) splits stub & hits streams, otherwise ~ passthrough
Chip data align
Hit processing:
Hit count can be activated to count hits (or clusters or stub) per strip locally
Always sends stub to trigger with fixed latency
When requested, queues data to readout block

(more details further on)
##Readout block
Data mode:
Manages memory readout and back-pressure
Optionally could stream data to SFP+ connectors on the FMC
(iif optical readout is present, one connector can be dedicated to fast streams)
Data formatting for the DAQ, according to payload specs
Performs post-scaling (to exercise fast trigger rates with limited bandwidth)
Count mode:
Used for commissioning: holds hit/cluster/stub counts per strip
Takes care to add counters properly – as many counters as conditions (e.g. thresholds)

##Fast command block
Recovers clock and trigger from backplane
Can generate clock and periodic triggers
Holds a trigger counter: it can be programmed to accept next N triggers and then hold the next incoming triggers
Can issue a trigger upon reception of a stub from modules

##Supervisor block
Holds a local stack of operations to be executed sequentially:
I²C transactions for F.E. (via module)
Generate (or accept next) N triggers
Push hit counters to appropriate memory location
Significant reduction of IPBus transactions needed for a calibration operation (à la pixel DTB)