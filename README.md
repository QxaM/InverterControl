# InverterControl
This library allows to control inverters via CiA402 supported communications (ie. EtherCAT) in advanced applications eg. position control or torque control. Library recreates PLCOpen motion control blocks for inverters, that does not support AXIS_REFs elements in CODESYS or CODESYS based softwares. At all times durign creating this library, the goal was to keep those motion blocks as close to the originals as possible, so user will not have to learn new functionality. If any difference occurs - suitable note will be included near block description.

## Function Blocks
Below you may find a full list and description of all blocks supported by this library. As said above, all blocks function similarly as standard PLCOpen MC blocks, thus the IC prefix, which basically stand for Inverter Control.

### IC_Power
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

## Library defined data types
Library defines several data types. Their descripition you may find below.

### IMC_Error (Enum)
Enumeration data type, allows indentification of function block error codes. Description of IC_Error values you may find below. IC_Error has attribute `to_string`, user can directly convert IC_Error message to type `STRING`.

| Name                              | Initial | Comment                                                                       |
| --------------------------------- | ------- | ----------------------------------------------------------------------------  |
| IMC_No_Error                      | 0       | No Error                                                                      |
| IMC_Inverter_No_Error             | 1       | Trying to reset error, but axis is not in error                               |
| IMC_Could_Not_Read_SDO            | 2       | Error reading axis parameter via SDO                                          |
| IMC_Axis_Not_Startup              | 3       | Axis was not started when trying to run                                       |
| IMC_Axis_Wrong_ControllerMode     | 4       | Axis is in wrong controller mode when trying to run                           |
| IMC_Axis_Not_In_Motion            | 5       | Tried to stop axis, that is already in stop mode                              |
| IMC_Drive_NotReady_For_Switch_ON  | 6       | Drive reports "Not ready for switch ON" state when trying to set power ON     |
| IMC_Operation_Not_Enabled         | 7       | Operation was not enabled when trying to set power ON                         |
| IMC_Voltage_Disabled              | 8       | Drives voltage is disabled whetn trygin to set power ON                       |

### Inverter_Axis_Ref (Struct)
Structured data type, that serves as a representation of inverter axis. Every function block, described aobve, need this kind of variable declared and mapped to the real equipements' data mapping. Ususally, this needs to be passed as pass by reference VAR_IN_OUT variable. Below you may find description of every element of this data type.

| Name                              | Type                  | Comment                                                                       |
| --------------------------------- | --------------------- | ----------------------------------------------------------------------------  |
| velocityUnits                     | `IMC_VelocityUnits`   | Variable allows switching velocity units of an axis. Default: `IMC_RPM`. See description of `IMC_VelocityUnits` data type       |
| distancePerRevolution             | `LREAL`               | Variable allows to change distance per revolution of an axis, thus user can automatically scale from PUU to engineering units (ie. mm)   |
| modeOfOperation                   | `SINT`                | Mode of Operation setting, mapped to `6060h`                                    |
| modeOfOperationDisplay            | `SINT`                | Mode of Operation display, mapped to `6061h`                                    |
| controlWord                       | `UINT`                | Register used for sending control to the drive, mapped to `6040h`               |
| statusWord                        | `UINT`                | Register used for recieving status from the drive, mapped to `6041h`            |
| actVelocity                       | `INT`                 | Feedback from drive with its' actual rotating speed, mapped to `6043h`          |
| actPosition                       | `DINT`                | Feedback from drive with its' actual position, mapped too `6064h`               |
| targetVelocity                    | `INT`                 | Target velocity sent to the drive in velocity mode, mapped to `6042h`           |


### IMC_VelocityUnits (Enum)
Enumeration data type, allows to represent actual velocity units used in application by a drive. Further description of all entries below

| Name            | Initial | Comment                                                                                                            |
| --------------- | ------- | ------------------------------------------------------------------------------------------------------------------ |
| IMC_RPM         | 0       | Velocity is scaled in rounds per minute                                                                            |
| IMC_PUU_PER_SEC | 1       | Velocity is scaled in pulses per second. Pulses mean actual pulses recieved by a drive from encoder                |
| IMC_MM_PER_MIN  | 2       | Velocity is scaled in milimiters per minute. Velocity is calculated from distance per revolution entered by a user |
