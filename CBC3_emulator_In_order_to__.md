##CBC3 emulator

In order to test this FW without actually having a physical CBC3 device a basic emulator was written. The code can be found in FC7-Firmware/fw/src/usr/phy/CBC3Emulator/.

The fast cmd.vhd receives the 320MHz clock and the fast command from the phy layer. It recovers fast reset, trigger, test pulse trigger and orbit reset. The fast reset can be used to reset the CBC3 emulator and the trigger signal to make the CBC3 emulator output triggered data. The other 2 fast signals are not used in the emulator.

The CBC3 i2c slave state machine can be found in CBC3 i2c slave.vhd. The emulator does not implement all i2c registers as a real CBC3 has, but it has all functionalities to test i2c addressing for the cbc3 chip: paging, masking, cbc broadcast, .… The first page of the i2c on the CBC3 has 35 registers whereas the second page has only two registers. The page bit can be found on the MSB of the first register (register address = 00000000) on the first page. On the first page some useful registers were implemented that actually set the data output pattern of the CBC3 emulator. Register 1 (register address = 00000001) to 32 (register address = 000100000) represent the channel masking and can be used to generate patterns in the triggered data. Register 1 corresponds to (channel 8, …, channel 1) and register 32 corresponds to (unused, unused, channel 254, …, channel 249). The output of the channel triggered data is set to 1 by default for all the channels and the masking is by default set to on. This setting makes it easier to generate a minimal pattern by unmasking some channels doing only a few i2c writes.

Register 33 and 34 of the first page set the VTH value. The VTH value is a 10 bit number (as in real CBC3) so is spread over 2 registers. The mapping is as follows (comparable with the mapping as in the real CBC3): register33 = (VTH7, VTH6, VTH5, VTH4, VTH3, VTH2, VTH1, VTH0) = 0x00 by default and register34 = (unused, unused, unused, unused, unused, unused, VTH9, VTH8) = 0x02 by default. The VTH register can be used to program the format of the stub data.  The table below gives the setting for the stub data output as it is programmed now (but it can easily be changed to whatever):

The stub data is put on the 5 lines in the stub data output.vhd file (modify this file if you want a different stub data output VTH dependence). This entity also receives the content of the VTH register. And the data output is as described in the table below.

Triggered data is put on the triggered data line on reception of a l1 trigger fast signal. The CBC3 emulator implements a bram and a fifo for the triggered data. The pattern is as defined in the CBC3 specs: (2 start bits = 11,  2 error bits = 00, pipe address, l1 counter = 9 bit counter, 254 channel data). The channel data can be (un)masked using i2c transactions as described before. 
The stub data and triggered data generation is different for the emulator and the real CBC3 case in the sense that the real CBC3 puts the triggered and stub data on 6 lines at 320MHz. Implementing a similar 320MHz logic on the fc7 FPGA seemed to result in timing closure problems. As a result we opted for a triggered data and stub data output at 40MHz where the data is outputted at 40MHz on 8 bit wide busses. This mimics the real processing of the data. In the real CBC3 case a first deserialisation will be done on ISERDES of the Kintex7. When using this emulator this first deserialisation is skipped and we produce already the deserialised data in the emulator. In this way we have a design that has timing closure and that, except for the first deserialisation in the ISERDES, allows to test the complete data flow. 

**How to use the CBC3 emulator for testing the data flow:**

1) Go to user package basic and set it as follows:

        --===================================--
        -- FMC Config
        --===================================--
        -- which implementation? electrical or optical? that is the question!
        constant IMPLEMENTATION         : implementation_type := EMULATION_CBC3;
        -- specifies, what's connected to the FMC1 (l12)
        constant FMC1                   : fmc_hardware_type := FMC_NONE;
        -- specifies, what's connected to the FMC2 (l8)
        constant FMC2                   : fmc_hardware_type := FMC_NONE;
        —===================================—
        --===================================--
        -- Important configs (have to correspond the FMC's configs)
        --===================================--
        -- 2 for CBC2, 3 for CBC3 (be careful!!!)
        constant CBC_VERSION            : integer := 3;     
    
        constant NUM_HYBRIDS            : integer := 1;
        constant NUM_CHIPS              : integer := 1;    
        --===================================--

   **For now it is only possible to generate 1 CBC.  We will fix this soon. So please leave the NUM_CHIPS to 1 for now.**
   
2) The default settings (and without issuing triggers) of the i2c registers give no output for the triggered data and for the stub data: as described for the case VTH <700 in the above table.

*in simulation: look at the stub to hb signal in user core.vhd. These are the 40 bit wide busses for each CBC going to the hybrid block.

*on board: have to connect with hybrid block and back end to see data output

3) To issue triggers:

*in simulation: use the trigger commands provided in Mykyta’s sim usr general file. You should see trigger data to the hybrid block (trig data to hb in user core) in the format as agreed upon (276 wide busses for each hybrid). The error bits are programmed to be zero, the l1 counter to increment and the channel data to be zero.

*on board: have to connect with hybrid block and back end to see data output

4) Write/Reading registers over i2c. 

*in simulation: use the IPBUS like transactions defined in Mykyta’s sim usr general file (e.g. the procedure write i2c command1). To read/write the registers and check the i2c reply (from the user core) signal in the simulation.

*on board: use the python scripts provided by Mykyta to read from all the registers of the CBC3 emulator and see that writing and reading works. See e.g. the I2CTester() command in send command.py (link) where you read default values on all pages, write to all registers and read the written value.

5) change data format through i2c:

*in simulation: write to an i2c register e.g. the VTH/mask registers and see the stub/channel data changing after the i2c write has changed the register values (see step 2 and 3 on how to see stub/triggered data).

*on board: have to connect with hybrid block and back end to see data output

