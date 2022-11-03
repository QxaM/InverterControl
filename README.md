# InverterControl
This library allows to control inverters via CiA402 supported communications (ie. EtherCAT) in advanced applications eg. position control or torque control. Library recreates PLCOpen motion control blocks for inverters, that does not support AXIS_REFs elements in CODESYS or CODESYS based softwares. At all times durign creating this library, the goal was to keep those motion blocks as close to the originals as possible, so user will not have to learn new functionality. If any difference occurs - suitable note will be included near block description.

# Function Blocks
Below you may find a full list and description of all blocks supported by this library. As said above, all blocks function similarly as standard PLCOpen MC blocks, thus the IC prefix, which basically stand for Inverter Control.

## IC_Power
Administrative function block, that allows for controlling power stage of a drive. The block is enabled if the input **Enable** is `TRUE`, then the body of block is executed. If **bRegulatorON** is `TRUE` - drive power stage is enabled (in other words Servo ON). If **bDriveStart** is `TRUE` - drive quick stop is disabled. Quick stop input allows to stop a drive with quick stop deceleration ramps (that can be changed via communication) without triggering stop command.

When the block is executing outputs **Busy** will be enabled (`TRUE`), if axis is ready to move **Status** will be `TRUE`. Outputs **bRegulatorRealState** and **bDriveStartRealState** indicate real states of servo ON and quick stop functions. If any error occur output **Error** will be set to `TRUE`, error code will be indicated with **ErrorID** output.

![obraz](https://user-images.githubusercontent.com/109360131/199725715-90566919-d5e8-4ef9-a0a1-251f775f67d2.png)

| Scope  | Name                 | Type                | Comment                                                           |
| ------ | -------------------- | ------------------- | -------------                                                     |
| InOut  | Inverter             | `Inverter_Axis_Ref` | Reference to inverter - see description of type Inverter_Axis_Ref |
| Input  | Enable               | `BOOL`              | `TRUE`: Enables execution of the function block                   |
|        | bRegulatorON         | `BOOL`              | `TRUE`: Enables the power stage                                   |
|        | bDriveStart          | `BOOL`              | `TRUE`: Disables quick stop                                       |
| Output | Status               | `BOOL`              | `TRUE`: Axis ready to move                                        |
|        | bRegulatorRealState  | `BOOL`              | `TRUE`: The power stage has been enabled by the drive             |
|        | bDriveStartRealState | `BOOL`              | `TRUE`: Drive is not blocked by QuickStop function                |
|        | Busy                 | `BOOL`              | `TRUE`: Function block is executing                               |
|        | Error                | `BOOL`              | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`          | Error identification - see description of type IC_Error           |
