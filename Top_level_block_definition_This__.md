#Top-level block definition
This should be configurable so that we can read-out either one or more modules (in case of optical interface).
It relies on the standard FC7 system firmware for: IPbus, Memory access, etc.

For the purpose of this document we define **modes** as working modes that can be switched at run time and **implementations** as working modes that can be switched at compilation time. not all of the working modes or implementations described here are needed for the initial version of the firmware to be functional. in particular: the **standard mode** is required (but not the **hit counting**) and only the implementations dealing with electrical, non-CIC, 2×CBC2 hybrid is needed first.

Hybrid and readout blocks should implement two modes of operation: **standard mode** and **hit counting mode** and they should be configured via the same register to avoid mismatches.

##Physical access block
This block has two (main) **implementations**. **Optical implementation**: wrapping around N×GBT-FPGA blocks connecting to a COTS FMC with a number of SFP+ cages and **Electrical implementation** connecting to the custom-made interface FMC. Each custom-made FMC interface will need a different implementation of the Physical access block.

The electrical access block implements also an I²C master and presents the same interface as the optical to the hybrid block. Since it must be independent of whether there is an optical or electrical connection, a CIC or not, the data sent to the hybrid module has to be slightly processed.

**Suggestion:** sending 16 times 127 bits to each hybrid block ? The interface (concerning the triggered data) to the hybrid block would therefore simply be a 127-bit wide bus on which we would send all the 8 CBCs 2 x 127 channels in 16 clock cycles. In the case of a full, complete hybrid with 8 CBC3, we could get all the data. For an hybrid with 2 CBC2, only the first 4 clock cycles would contain meaning full data, the others being all zero (for the 6 non-present CBCs). The format of the data transfer could be something like this:

Clk Cycle - Data transferred
1. 127 channels of 1st CBC top layer 
2. 127 channels of 1st CBC bottom layer 
3. 127 channels of 2nd CBC top layer 
4. 127 channels of 2nd CBC bottom layer 
5. 127 channels of 3rd CBC top layer 
6. 127 channels of 3rd CBC bottom layer 
7. 127 channels of 4th CBC top layer 
8. 127 channels of 4th CBC bottom layer 
9. 127 channels of 5th CBC top layer 
10. 127 channels of 5th CBC bottom layer 
11. 127 channels of 6th CBC top layer 
12. 127 channels of 6th CBC bottom layer
13. 127 channels of 7th CBC top layer 
14. 127 channels of 7th CBC bottom layer 
15. 127 channels of 8th CBC top layer 
16. 127 channels of 8th CBC bottom layer 

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
