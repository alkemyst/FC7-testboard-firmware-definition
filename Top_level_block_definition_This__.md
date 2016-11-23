#Top-level block definition
This should be configurable so that we can read-out either one or more modules (in case of optical interface).
It relies on the standard FC7 system firmware for: IPbus, Memory access, etc.

For the purpose of this document we define **modes** as working modes that can be switched at run time and **implementations** as working modes that can be switched at compilation time. not all of the working modes or implementations described here are needed for the initial version of the firmware to be functional. in particular: the **standard mode** is required (but not the **hit counting**) and only the implementations dealing with electrical, non-CIC, 2×CBC2 hybrid is needed first.

Hybrid and readout blocks should implement two modes of operation: **standard mode** and **hit counting mode** and they should be configured via the same register to avoid mismatches.

##Physical access block
This block has two (main) **implementations**. **Optical implementation**: wrapping around N×GBT-FPGA blocks connecting to a COTS FMC with a number of SFP+ cages and **Electrical implementation** connecting to the custom-made interface FMC. Each custom-made FMC interface will need a different implementation of the Physical access block.

The electrical access block implements also an I²C master and presents the same interface as the optical to the hybrid block. Since it must be independent of whether there is an optical or electrical connection, a CIC or not, the data sent to the hybrid module has to be slightly processed.

###Triggered data readout
The physical access block sends 16 times 138 bits (127 for the channels + 9 for pipe address or L1 counter + 2 for Start bits or latency error) to each hybrid block ? The interface (concerning the triggered data) to the hybrid block is therefore simply be a 138-bit wide bus on which all the 8 CBCs 2 x 127 channels are sent, as well as additional information, in 16 clock cycles. In the case of a full, complete hybrid with 8 CBC3, we get all the data. For an hybrid with 2 CBC2, only the first 4 clock cycles contain meaning full data, the others being all zero (for the 6 non-present CBCs). The format of the data transfer is the following:

Clk Cycle - Data transferred
1. 2 start bits + 9 bits pipe address + 127 channels top layer of 1st CBC 
2. 2 bits Latency Error + 9 bits L1 counter + 127 channels bottom layer of 1st CBC 
3. 2 start bits + 9 bits pipe address + 127 channels top layer of 2nd CBC 
4. 2 bits Latency Error + 9 bits L1 counter + 127 channels bottom layer of 2nd CBC 
5. 2 start bits + 9 bits pipe address + 127 channels top layer of 3rd CBC 
6. 2 bits Latency Error + 9 bits L1 counter + 127 channels bottom layer of 3rd CBC
7. 2 start bits + 9 bits pipe address + 127 channels top layer of 4th CBC 
8. 2 bits Latency Error + 9 bits L1 counter + 127 channels bottom layer of 4th CBC 
9. 2 start bits + 9 bits pipe address + 127 channels top layer of 5th CBC 
10. 2 bits Latency Error + 9 bits L1 counter + 127 channels bottom layer of 5th CBC 
11. 2 start bits + 9 bits pipe address + 127 channels top layer of 6th CBC 
12. 2 bits Latency Error + 9 bits L1 counter + 127 channels bottom layer of 6th CBC
13. 2 start bits + 9 bits pipe address + 127 channels top layer of 7th CBC 
14. 2 bits Latency Error + 9 bits L1 counter + 127 channels bottom layer of 7th CBC 
15. 2 start bits + 9 bits pipe address + 127 channels top layer of 8th CBC 
16. 2 bits Latency Error + 9 bits L1 counter + 127 channels bottom layer of 8th CBC

This fits well for the 2S modules. For the case of the PS modules, since we have 2 times 8 MPA sending cluster's width and position (I don't know the details though, such as what is the maximum number of bits to be sent by one MPA), we could use the same bus but this time, the format of the data transferred would be something like this:

Clk Cycle - Data transferred
1. cluster's width and position on pixel layer of 1st MPA
2. cluster's width and position on strip layer of 1st MPA 
3. cluster's width and position on pixel layer of 2nd MPA 
4. cluster's width and position on strip layer of 2nd MPA 
5. cluster's width and position on pixel layer of 3rd MPA 
6. cluster's width and position on strip layer of 3rd MPA 
7. cluster's width and position on pixel layer of 4th MPA 
8. cluster's width and position on strip layer of 4th MPA 
9. cluster's width and position on pixel layer of 5th MPA 
10. cluster's width and position on strip layer of 5th MPA 
11. cluster's width and position on pixel layer of 6th MPA 
12. cluster's width and position on strip layer of 6th MPA
13. cluster's width and position on pixel layer of 7th MPA 
14. cluster's width and position on strip layer of 7th MPA 
15. cluster's width and position on pixel layer of 8th MPA 
16. cluster's width and position on strip layer of 8th MPA 

Interface to the hybrid block: (t.b.d.)
