# InverterControl
This library allows to control inverters via CiA402 supported communications (ie. EtherCAT) in advanced applications eg. position control or torque control. Library recreates PLCOpen motion control blocks for inverters, that does not support AXIS_REFs elements in CODESYS or CODESYS based softwares. At all times durign creating this library, the goal was to keep those motion blocks as close to the originals as possible, so user will not have to learn new functionality. If any difference occurs - suitable note will be included near block description.

## Function Blocks
Below you may find a full list and description of all blocks supported by this library. As said above, all blocks function similarly as standard PLCOpen MC blocks, thus the IC prefix, which basically stands for Inverter Control.

### IC_Power
Administrative function block, that allows controlling power stage of a drive. The block is enabled if the input **Enable** is `TRUE`, then the body of block is executed. If **bRegulatorON** is `TRUE` - drive power stage is enabled (in other words Servo ON). If **bDriveStart** is `TRUE` - drive quick stop is disabled. Quick stop input allows to stop a drive with quick stop deceleration ramps (that can be changed via communication) without triggering stop command.

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

### IC_Reset
Administrative function block, that allows reseting drive errors if such occur. The block is executed once, when rising edge of input **Execute** is detected. 

When block is being executed output **Busy** is `TRUE`, when axis is reset succesfully output changes it's value to `FALSE` and output **Done** is set to `TRUE`. If error during execution of block occur output **Error** will be set to `TRUE`. Error code will be indicated in output **ErrorID**.

![obraz](https://user-images.githubusercontent.com/109360131/199761197-dbb923f2-c1f3-4f0e-a4df-dbb14e5e72ab.png)

| Scope  | Name                 | Type                | Comment                                                           |
| ------ | -------------------- | ------------------- | -------------                                                     |
| InOut  | Inverter             | `Inverter_Axis_Ref` | Reference to inverter - see description of type Inverter_Axis_Ref |
| Input  | Execute              | `BOOL`              | Rising edge: Starts the execution of the function block           |
| Output | Done                 | `BOOL`              | `TRUE`: Reset has been succesfully executed                       |
|        | Busy                 | `BOOL`              | `TRUE`: Function block is executing                               |
|        | Error                | `BOOL`              | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`          | Error identification - see description of type IC_Error           |

### IC_SetVelocityUnits
Configuration function block, that allows switching velocity units of Inverter_Axis_Ref. The block is executed once, when rising edge of input **Execute** is detected. Velocity units are set with input VelocityUnits.

When block is being executed output **Busy** is `TRUE`, when axis is reset succesfully output changes it's value to `FALSE` and output **Done** is set to `TRUE`. If error during execution of block occur output **Error** will be set to `TRUE`. Error code will be indicated in output **ErrorID**.

![obraz](https://user-images.githubusercontent.com/109360131/199768707-93224d2d-34a5-43f5-833e-aed756fed1f9.png)

| Scope  | Name                 | Type                | Comment                                                           |
| ------ | -------------------- | ------------------- | -------------                                                     |
| InOut  | Inverter             | `Inverter_Axis_Ref` | Reference to inverter - see description of type Inverter_Axis_Ref |
| Input  | Execute              | `BOOL`              | Rising edge: Starts the execution of the function block           |
|        | VelocityUnits        | `IMC_VelocityUnits` | Allows for selection of velocity units of axis ref. Available units are RPM, Pulse/s, mm/min. See `IMC_VelocityUnits` data type description for further details |
| Output | Done                 | `BOOL`              | `TRUE`: Velocity units has been succesfully set                   |
|        | Busy                 | `BOOL`              | `TRUE`: Function block is executing                               |
|        | Error                | `BOOL`              | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`          | Error identification - see description of type IC_Error           |

### IC_SetControllerMode
Configuration function block, that allows switching controller mode (velocity, position, torque) of Inverter_Axis_Ref. The block is executed once, when rising edge of input **Execute** is detected. Velocity units are set with input VelocityUnits.

When block is being executed output **Busy** is `TRUE`, when axis has set the controller mode succesfully, output changes it's value to `FALSE` and output **Done** is set to `TRUE`. If error during execution of block occur output **Error** will be set to `TRUE`. Error code will be indicated in output **ErrorID**.

![obraz](https://user-images.githubusercontent.com/109360131/199773244-5b092ff1-d91c-49e2-90fd-742ff5ce1ff6.png)
| Scope  | Name                 | Type                 | Comment                                                           |
| ------ | -------------------- | -------------------- | -------------                                                     |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref |
| Input  | Execute              | `BOOL`               | Rising edge: Starts the execution of the function block           |
|        | ControllerMode       | `IMC_ControllerMode` | Allows for selection of controller mode of axis. Available controller modes are velocity, position, torque and home control. For further details see `IMC_ControllerMode` description |
| Output | Done                 | `BOOL`               | `TRUE`: Controller mode has been succesfully set                  |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                               |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error           |

### IC_ReadActualPosition
Diagnostic function block, that allows reading actual position of inverter axis. Actual position is read in milimeters - distance per revolution and encoder pulses are used for scaling (see `Inverter_Axis_Ref` for further details).

Block is being executed when **Enable** input is `TRUE`. When block is executing **Busy** output is `TRUE`, whena position has actual value - **Valid** output is `TRUE`. Position is written to output **Position** in `LREAL` value. If any error occur during execution of this block **Error** output will be `TRUE` and error code could be checked in **ErrorID**

![obraz](https://user-images.githubusercontent.com/109360131/200296069-2c88bac0-3483-4ee1-9c92-e7cccbb9bd8b.png)
| Scope  | Name                 | Type                 | Comment                                                           |
| ------ | -------------------- | -------------------- | -------------                                                     |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref |
| Input  | Enable               | `BOOL`               | `TRUE`: Block is being executed                                   |
| Output | Valid                | `BOOL`               | `TRUE`: Values are up to date                                     |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                               |
|        | Position             | `LREAL`              | Actual posiiton read from a drive                                 |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error           |

### IC_ReadActualVelocity
Diagnostic function block, that allows reading actual velocity of inverter axis. Actual velocity may be read in different units - unit selection is defined by velocityUnit input of inverter axis ref data type. For more information see description of `Inverter_Axis_Ref` data type below.

Block is being executed when **Enable** input is `TRUE`. When block is executing **Busy** output is `TRUE`, whena velocity has actual value - **Valid** output is `TRUE`. Velocity is written to output **Velocity** in `LREAL` value. If any error occur during execution of this block **Error** output will be `TRUE` and error code could be checked in **ErrorID**

![obraz](https://user-images.githubusercontent.com/109360131/200335180-683b20e4-22eb-4e93-baf1-be754eace14f.png)
| Scope  | Name                 | Type                 | Comment                                                           |
| ------ | -------------------- | -------------------- | -------------                                                     |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref |
| Input  | Enable               | `BOOL`               | `TRUE`: Block is being executed                                   |
| Output | Valid                | `BOOL`               | `TRUE`: Values are up to date                                     |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                               |
|        | Velocity             | `LREAL`              | Actual velocity read from a drive                                 |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error           |

### IC_ReadActualTorque
Diagnostic function block, that allows reading actual torque of inverter axis. Actual torque is scaled in 0.1%.

Block is being executed when **Enable** input is `TRUE`. When block is executing **Busy** output is `TRUE`, when **Torque** has actual value - **Valid** output is `TRUE`. Torque is written to output **Torque** in `LREAL` value. If any error occur during execution of this block **Error** output will be `TRUE` and error code could be checked in **ErrorID**

![obraz](https://user-images.githubusercontent.com/109360131/200336624-e2a443de-bf85-4da8-9187-3741671bfffa.png)
| Scope  | Name                 | Type                 | Comment                                                           |
| ------ | -------------------- | -------------------- | -------------                                                     |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref |
| Input  | Enable               | `BOOL`               | `TRUE`: Block is being executed                                   |
| Output | Valid                | `BOOL`               | `TRUE`: Values are up to date                                     |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                               |
|        | Torque               | `LREAL`              | Actual torque read from a drive                                   |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error           |

### IC_ReadAxisError
Diagnostic function block, that allows reading inverter warning and error codes.

Block is being executed when **Enable** input is `TRUE`. When block is executing **Busy** output is `TRUE`, when outputs have actual values - **Valid** output is `TRUE`. Error code is written to output **AxisErrorID** in `BYTE` value. Warning code is written to output **AxisWarningID**. If any error occur during execution of this block **Error** output will be `TRUE` and error code could be checked in **ErrorID**

![obraz](https://user-images.githubusercontent.com/109360131/209980049-fc7f55d1-ba29-4615-a19a-8bab88241c2a.png)
| Scope  | Name                 | Type                 | Comment                                                           |
| ------ | -------------------- | -------------------- | -------------                                                     |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref |
| Input  | Enable               | `BOOL`               | `TRUE`: Block is being executed                                   |
| Output | Valid                | `BOOL`               | `TRUE`: Values are up to date                                     |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                               |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error           |
|        | AxisErrorID          | `BYTE`               | Error code of inverter axis                                       |
|        | AxisWarningI         | `BYTE`               | Warning code of inverter axis                                     |

### IC_ReadStatus
Diagnostic function block, returns detailed informations about status of the axis.

Block is being executed when **Enable** input is `TRUE`. When block is executing **Busy** output is `TRUE`, when outputs have actual values - **Valid** output is `TRUE`. If any error occur during execution of this block **Error** output will be `TRUE` and error code could be checked in **ErrorID**

![obraz](https://user-images.githubusercontent.com/109360131/210090406-766fd687-84bb-4b68-8561-60ce367f205a.png)
| Scope  | Name                 | Type                 | Comment                                                           |
| ------ | -------------------- | -------------------- | -------------                                                     |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref |
| Input  | Enable               | `BOOL`               | `TRUE`: Block is being executed                                   |
| Output | Valid                | `BOOL`               | `TRUE`: Values are up to date                                     |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                               |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error           |
|        | Disabled             | `BOOL`               | Power stage of an axis is disabled and no errors occur            |
|        | Errorstop            | `BOOL`               | Error occurs on the axis                                          |
|        | Standstill           | `BOOL`               | Power stage of an axis is enabled, axis is not moving             |
|        | DiscreteMotion       | `BOOL`               | Axis is moving in Position Mode                                   |
|        | ContinuousMotion     | `BOOL`               | Axis is moving in Velocity Mode                                   |
|        | Homing               | `BOOL`               | Axis is moving and is being homed                                 |
|        | ConstantVelocity     | `BOOL`               | Axis is moving with constant velocity                             |
|        | Accelerating         | `BOOL`               | Axis is accelerating                                              |
|        | Decelerating         | `BOOL`               | Axis is decelerating                                              |

###IC_MoveVelocity
This function block causes an endless motion at a specified velocity. Inverter has to be set in velocity mode to be controlled with this function block. **Velocity** units depend on velocity unit set in inverter. **Acceleration** and **deceleration** ramps are both scaled in seconds and entered as `LREAL` values.

It is possible to change velocity on the fly, block is executed based on **Enable** state. 

Timing execution of this function block is shown on a diagram below. When input **Enable** is `TRUE` inverter accelerates to set velocity. When it reaches the set velocity output **InVelocity** is set to `TRUE`. When velocity is changed inverter automatically changes it's velocity to new set velocity. If intput **Enable** changes to `FALSE` inverter continuous motion with last velocity.

![IC_MoveVelocityTimingDiagram drawio(1)](https://user-images.githubusercontent.com/109360131/210220906-b54c6a2b-c8b2-4a7e-ad5e-d8a60d584154.png)

![obraz](https://user-images.githubusercontent.com/109360131/210218220-97c6fba5-1a23-4a52-b0e6-9bd960cbf0fc.png)

| Scope  | Name                 | Type                 | Comment                                                             |
| ------ | -------------------- | -------------------- | -------------                                                       |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref   |
| Input  | Enable               | `BOOL`               | `TRUE`: Block is being executed                                     |
|        | Velocity             | `LREAL`              | Maximum velocity in units set in Inverter. Is always positive       |
|        | AccelerationTime     | `LREAL`              | Acceleration time in seconds in which motor will reach maximum velocity. Is always positive |
|        | DecelerationTime     | `LREAL`              | Deceleration time in seconds in which motor will reach full stop from  maximum velocity. Is always positive |
|        | Direction            | `IMC_Direction`      | Direction of the set motion - see description of type IMC_Direction |
| Output | InVelocity           | `BOOL`               | `TRUE`: Axis has reached set velocity                               |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                                 |
|        | Active               | `BOOL`               | `TRUE`: Function block is controlling axis motion                   |
|        | CommandAborted       | `BOOL`               | `TRUE`: Comand was aborted during execution                         |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution           |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error             |

### IC_Stop
This function block causes the axis to stop. As long as **Execute** input is `TRUE` no other motion blocks could be executed. If the velocity reaches zero, block output **Done** is set to `TRUE`. Deceleration values are scaled as seconds and enter as `LREAL` value.

Timing execution of this function block is shown in diagram below. When **Execute** changes to `TRUE` axis starts to decelerate. When actual velocity reaches value zero. Output **Done** is set to `TRUE`.

![IC_StopTimingDiagram drawio](https://user-images.githubusercontent.com/109360131/210226347-df44b41c-ea88-488e-ae2e-6d2ee6bb87ec.png)

![obraz](https://user-images.githubusercontent.com/109360131/210225565-cc9123dc-0b87-4af9-9b31-094bfaab269e.png)

| Scope  | Name                 | Type                 | Comment                                                             |
| ------ | -------------------- | -------------------- | -------------                                                       |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref   |
| Input  | Execute              | `BOOL`               | `TRUE`: Block is being executed                                     |
|        | DecelerationTime     | `LREAL`              | Deceleration time in seconds in which motor will reach full stop from  maximum velocity. Is always positive |
| Output | Done                 | `BOOL`               | `TRUE`: Axis has reached a stop                                     |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                                 |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution           |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error             |

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
| IMC_Wrong_Ramp_Times              | 9       | Could not write ramps to the drive. Wrong ramps ie. 0 or negative             |

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

### IMC_ControllerMode (Enum)
Enumeration data type, represents drive controller mode in text values. Controller mode is set and read, by Mode of Operation and Mode of Operation display respectively. Enum values correspond directyl to Mode of Operation values (ie. IMC_Velocity=2 and Mode of Opertaion=2). Further description below:

| Name            | Initial | Comment                                                                                                            |
| --------------- | ------- | ------------------------------------------------------------------------------------------------------------------ |
| IMC_Position    | 1       | Drive is set in position controller mode - positioning blocks are available to use                                 |
| IMC_Velocity    | 2       | Drive is set in velocity controller mode - velocity blocks are available to use                                    |
| IMC_Torque      | 3       | Drive is set in torque controller mode - torque control blocks are available to use                                |
| IMC_Home        | 6       | Automatically set only for triggering IC_Home. Can be set by the user, but only IC_Home could be used in this mode |

### IMC_Direction (Enum)
Enumeration data type, represents direction of set motion.

| Name            | Initial | Comment                                                                                                            |
| --------------- | ------- | ------------------------------------------------------------------------------------------------------------------ |
| IMC_Positive    | 1       | Drive is moving in positive direction                                                                              |
| IMC_Negative    | 3       | Drive is moving in negative direction                                                                              |

### IMC_MotionState (Enum)
Enumeration data type, represents direction of set motion.

| Name                    | Initial | Comment                                                                                                            |
| ----------------------- | ------- | ------------------------------------------------------------------------------------------------------------------ |
| IMC_ConstantVelocity    | 1       | Drive is moving with constant velocity                                                                             |
| IMC_Accelerating        | 2       | Drive is accelerating                                                                                              |
| IMC_Decelerating        | 3       | Drive is decelerating                                                                                              |
| IMC_Standstill          | 4       | Drive has zero velocity                                                                                            |

##TODO
[x] Testing velocity mode
[ ] Add reading ControllerMode and VelocityUnits
[ ] Switch to Inverter Function Block from structure
[ ] Add Positioning mode
[ ] Test position mode
[ ] Add jog
[ ] Add Torque mode
[ ] Test torque mode
[ ] Test mode switching on the fly
[ ] Testing on AX3 (different library for ETC_SDO)
