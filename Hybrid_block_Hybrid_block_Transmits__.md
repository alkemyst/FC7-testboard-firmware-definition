##Hybrid block
Hybrid block

* Transmits slow and fast commands
* Receives and unpacks data
* Syncs trigger and stub data
* Can forward stub detection to fast command block
* Sends data to the readout block
* Can pre-process hits data for calibration (hit or stub counting)

The hybrid block is split in the following blocks:
* CIC unpacker: (if needed) splits stub & hits streams, otherwise ~ passthrough
* Chip data align
* Hit processing:
 * Hit count can be activated to count hits (or clusters or stub) per strip locally
 * Always sends stub to trigger with fixed latency
 * When requested, queues data to readout block
