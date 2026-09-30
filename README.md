# EnergyPlus Agent

This agent allows a user to run building model simulations 
with EnergyPlus and to send/receive messages back and forth between VOLTTRON and the
simulated building. For more information about EnergyPlus, please refer to https://www.energyplus.net/sites/default/files/docs/site_v8.4.0/GettingStarted/GettingStarted/index.html. 
Technical documentation about the simulation framework can be found at https://volttron.readthedocs.io/en/develop/developing-volttron/integrating-simulations/index.html

## EnergyPlus installation
For installing setup in Ubuntu based systems, 

1. Install java JDK package

   ```shell
   sudo apt update
   sudo apt install default-jdk
   ```

1. Download and install EnergyPlus (ver 8.4.0)

   ```shell
   wget https://github.com/NatLabRockies/EnergyPlus/releases/download/v8.4.0-Update1/EnergyPlus-8.4.0-09f5359d8a-Linux-x86_64.sh
   chmod +x EnergyPlus-8.4.0-09f5359d8a-Linux-x86_64.sh
   sudo ./EnergyPlus-8.4.0-09f5359d8a-Linux-x86_64.sh 
   ```
1. You can verify the installation with. 
   ```shell
   energyplus --version
   ```
1. You should see an output with a version number of 8.4.0

   ```
   EnergyPlus, Version 8.4.0-c87e61b44b
   ```

## EnergyPlus Agent Configuration

Simulating a building with the EnergyPlus Agent requires three files:

* Energy Plus Input Data File (IDF): an ASCII file containing the data describing the building and HVAC system to be simulated.
* Energy Plus Weather File (EPW): a standardized container for TMY (typical meteorological year) weather data. 
* A configuration file that defines the available points from the IDF and maps these to VOLTTRON topics.

The EnergyPlus agent can be run with any IDF file, so long as it is compatible with the installed version of EnergyPlus.
Using a different EPW file allows the building to be simulated in a new location.

Several models, from the [DOE Prototype Building Models](https://www.energycodes.gov/prototype-building-models)
are included in the agent with pre-defined configurations. These models may be used by specifying the name of the model
in an abreviated configuration file:

```json
{
    "cosimulation_advance": "applications/ilc/advance",
    "properties": {
      "model": "small_office",
      "size": 40960,
      "startmonth": 7,
      "startday": 3,
      "endmonth": 8,
      "endday": 31,
      "timestep": 60,
      "cosimulation_sync": false,
      "real_time_periodic": false,
      "co_sim_timestep": 5,
      "real_time_flag": false,
      "base_topic": "Campus/SMALL_OFFICE"
    }
}

```
The following model names are included:

* building1
* large_office
* medium_office
* small_office
* warehouse


Other IDF files may be utilized by specifying the path to IDF file in the agent configuration's "model" key
in place of a short name. It should be noted, however, that building a configuration file for the IDF may require
good knowledge of Energy Plus as the agent configuration file will need to additionally inputs and outputs dictionaries.
Examples of complete configuration files includeing input and output dictionaries can be found in the models directory
of source of this package. For instance: [Building1 Config](src/energyplus/models/building1/building1.config).


1. Install volttron-energyplus package. The VIP identity can mimic that of the platform driver (as shown below)
   or can be a unique identity for the agent. Importantly, though agents which interact with it must use the same
   VIP identity. If a real platform driver is not used on this platform, it is recommended to use the "platform.driver"
   identity as this avoids needing to modify other agents which are designed to interact with a real driver.

      ````
      vctl install volttron-energyplus --vip-identity platform.driver --tag eplus
      ````
1. Store the configuration file in the VOLTTRON configuration store:
    ```shell
    vctl config store platform.actuator volttron-energyplus/ep_building1.yml volttron-energyplus/ep_building1.yml
    ```
1. Start the agent:
   ```shell
   vctl start --tag eplus
    ```


## Development

Please see the following for contributing guidelines [contributing](https://github.com/eclipse-volttron/volttron-core/blob/develop/CONTRIBUTING.md).

Please see the following helpful guide about [developing modular VOLTTRON agents](https://github.com/eclipse-volttron/volttron-core/blob/develop/DEVELOPING_ON_MODULAR.md)