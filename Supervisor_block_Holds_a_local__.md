## Supervisor block
Holds a local stack of operations to be executed sequentially: 
* I²C transactions for F.E. (via module) 
* Generate (or accept next) N triggers 
* Push hit counters to appropriate memory location 
* Signifcant reduction of IPBus transactions needed for a calibration operation (à la pixel DTB)