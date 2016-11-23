###Slow Control
The physical block receives slow control commands from the Supervisor Block and sends them to the front-end. The bus used for command request and command reply is given below:

    --=== slow control records ===--
    -- The signals going from supervisor to physical blocks
    type cmd_wbus is
    record
       cmd_strobe            : std_logic;
       -- hybrid_id
       cmd_hybrid_id         : std_logic_vector(4 downto 0);
       -- cbc on hybrid id
       cmd_chip_id           : std_logic_vector(3 downto 0);
       -- page in CBC
       cmd_page              : std_logic;
       -- read or write setting
       cmd_read              : std_logic;
       -- register_address
       cmd_register          : std_logic_vector(7 downto 0);
       -- data to cbc
       cmd_data              : std_logic_vector(7 downto 0);
    end record;

    type cmd_wbus_array is array(natural range <>) of cmd_wbus;
        
    -- The signals going from physical to supervisor blocks      
    type cmd_rbus is
    record
       cmd_strobe            : std_logic;
       cmd_data              : std_logic_vector(7 downto 0);
       cmd_err               : std_logic;
    end record;

    type cmd_rbus_array is array(natural range <>) of cmd_rbus;
    
If an error occurs during the transaction, the reply will have the `cmd_err` bit high as well as the `cmd_strobe` bit high, and the `cmd_data` will contain the error code.

###Fast Command

    
