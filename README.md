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

### IC_HomingConfig
This function block, allows to configure search home sequence. To configure the sequence function block inputs are used. Input **HomingMode** allows to choose homing sequence. Homing sequence is generally defined by manufacturers. You may see the description of `IMC_Homing_Mode` for general sequence introduced by CoE402 standard. **FirstHomingSpeed** allows to set homing speed in `IMC_VelocityUnits` when looking for reference signal. **SecondHomingSpeed** allows to set homing speed in `IMC_VelocityUnits` when looking for Z phase. Input **Acceleration** allows to set acceleration times when homing.

![obraz](https://user-images.githubusercontent.com/109360131/210771882-f95705ce-45ad-4032-b87b-86a41b4fcda3.png)

| Scope  | Name                 | Type                 | Comment                                                                                       |
| ------ | -------------------- | -------------------- | -------------                                                                                 |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref                             |
| Input  | Execute              | `BOOL`               | `TRUE`: Block is being executed                                                               |
|        | HomingMode           | `IMC_Homing_Mode`    | Sequence of homing procedure. See description of IMC_Homin_Mode.                              |
|        | FirstHomingSpeed     | `LREAL`              | Speed while looking for reference signal. Scaled in IMC_VelocityUnits actually set on drive.  |
|        | SecondHomingSpeed    | `LREAL`              | Speed while looking for Z-phase signal. Scaled in IMC_VelocityUnits actually set on drive.    |
|        | AccelerationTime     | `LREAL`              | Acceleration time in which drive will reach `FirstHomingSpeed` in seconds                     |
| Output | Done                 | `BOOL`               | `TRUE`: Finished homing sequence                                                              |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                                                           |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution                                     |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error                                       |

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
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error           |
|        | Position             | `LREAL`              | Actual posiiton read from a drive                                 |

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
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error           |
|        | Velocity             | `LREAL`              | Actual velocity read from a drive                                 |
|        | VelocityUnits        | `IMC_VelocityUnits`  | Information in which units velocity was read from drive (actual set velocity units) |

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
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error           |
|        | Torque               | `LREAL`              | Actual torque read from a drive                                   |

### IC_ReadControllerMode
Diagnostic function block, that allows reading actual controller mode set on inverter axis.

Block is being executed when **Enable** input is `TRUE`. When block is executing **Busy** output is `TRUE`, when **ControllerMode** has actual value - **Valid** output is `TRUE`. Actual controller mode is written to **ControllerMode** output. If any error occur during execution of this block **Error** output will be `TRUE` and error code could be checked in **ErrorID**

![obraz](https://user-images.githubusercontent.com/109360131/210572333-03028a3a-0e8a-4b6d-a697-31431059e206.png)
| Scope  | Name                 | Type                 | Comment                                                           |
| ------ | -------------------- | -------------------- | -------------                                                     |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref |
| Input  | Enable               | `BOOL`               | `TRUE`: Block is being executed                                   |
| Output | Valid                | `BOOL`               | `TRUE`: Values are up to date                                     |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                               |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error           |
|        | ControllerMode       | `IC_ControllerMode`  | Actual controller mode read form device                           |


### IC_ReadVelocityUnits
Diagnostic function block, that allows reading actual velocity units set on inverter axis.

Block is being executed when **Enable** input is `TRUE`. When block is executing **Busy** output is `TRUE`, when **VelocityUnits** has actual value - **Valid** output is `TRUE`. Actual controller mode is written to **VelocityUnits** output. If any error occur during execution of this block **Error** output will be `TRUE` and error code could be checked in **ErrorID**

![obraz](https://user-images.githubusercontent.com/109360131/210572619-084e2247-56d2-4ff6-9c98-66ad1c114c43.png)
| Scope  | Name                 | Type                 | Comment                                                           |
| ------ | -------------------- | -------------------- | -------------                                                     |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref |
| Input  | Enable               | `BOOL`               | `TRUE`: Block is being executed                                   |
| Output | Valid                | `BOOL`               | `TRUE`: Values are up to date                                     |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                               |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution         |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error           |
|        | VelocityUnits        | `IC_VelocityUnits `  | Actual velocity units read form device                            |

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

### IC_MoveVelocity
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

### IC_Home
This function block causes the axis to perform the "search home" sequence. Details of this sequence might be changed with `IC_HomingConfig` function block and generally are defined by manufacturer. The **Position** input is used to set the absolute position offset when reference signal is detected.

![obraz](https://user-images.githubusercontent.com/109360131/210769433-09ddcea4-d518-4eae-b2d8-a9f670cf3e17.png)


| Scope  | Name                 | Type                 | Comment                                                             |
| ------ | -------------------- | -------------------- | -------------                                                       |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref   |
| Input  | Execute              | `BOOL`               | `TRUE`: Block is being executed                                     |
|        | Position             | `LREAL`              | Absolute position offset when the refernce signal is detected       |
| Output | Done                 | `BOOL`               | `TRUE`: Search home procedure is finished                           |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                                 |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution           |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error             |

### IC_MoveRelative
The function block commands a motion of a specified distance. The motion ends in standstill. 

The motion starts when **Execute** changes from `False` to true. Distance moved is set with input **Distance** in engineering units. Finished position will be sum of actual position of an axis and **Distance**. Motion profile could be set with inputs **Velocity**, **Acceleration** and **Deceleration**. When motion is finished output **Done** will be set to `TRUE`. When block is operating output **Busy** is `TRUE` and if block is controlling the motion output **Active** is `TRUE` If error occurs during execution of this block output **Error** is set to `TRUE`, error ID can be checked with output **ErrorID**. If motion of an axis is interrupted output **CommandAborted** is set to `TRUE`.

![obraz](https://user-images.githubusercontent.com/109360131/210819616-29da858e-b5be-4dae-88b9-6ed3e5e2e89e.png)


| Scope  | Name                 | Type                 | Comment                                                                            |
| ------ | -------------------- | -------------------- | -------------                                                                      |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref                  |
| Input  | Execute              | `BOOL`               | `TRUE`: Block is being executed                                                    |
|        | Distance             | `LREAL`              | Offset distance to which axis will be moved                                        |
|        | Velocity             | `LREAL`              | Maximum velocity to which axis will accelerate                                     |
|        | Acceleration         | `LREAL`              | Acceleration time in second in which axis will reach `Velocity`                    |
|        | Deceleration         | `LREAL`              | Deceleration time in second in which axis will reach full stop from `Velocity`     |
| Output | Done                 | `BOOL`               | `TRUE`: Motion is finished                                                         |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                                                |
|        | Active               | `BOOL`               | `TRUE`: Block is controlling motion of an axis                                     |
|        | CommandAborted       | `BOOL`               | `TRUE`: Execution of function block was interrupted                                |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution                          |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error                            |

### IC_MoveAbsolute
The function block commands a motion to the specifed location. The motion ends in standstill. 

The motion starts when **Execute** changes from `False` to true. Distance moved is set with input **Position** in engineering units. Finished position will be position set with input **Position**. Motion profile could be set with inputs **Velocity**, **Acceleration** and **Deceleration**. When motion is finished output **Done** will be set to `TRUE`. When block is operating output **Busy** is `TRUE` and if block is controlling the motion output **Active** is `TRUE` If error occurs during execution of this block output **Error** is set to `TRUE`, error ID can be checked with output **ErrorID**. If motion of an axis is interrupted output **CommandAborted** is set to `TRUE`.

![obraz](https://user-images.githubusercontent.com/109360131/210819616-29da858e-b5be-4dae-88b9-6ed3e5e2e89e.png)


| Scope  | Name                 | Type                 | Comment                                                                            |
| ------ | -------------------- | -------------------- | -------------                                                                      |
| InOut  | Inverter             | `Inverter_Axis_Ref`  | Reference to inverter - see description of type Inverter_Axis_Ref                  |
| Input  | Execute              | `BOOL`               | `TRUE`: Block is being executed                                                    |
|        | Distance             | `LREAL`              | Target position of the motion                                                      |
|        | Velocity             | `LREAL`              | Maximum velocity to which axis will accelerate                                     |
|        | Acceleration         | `LREAL`              | Acceleration time in second in which axis will reach `Velocity`                    |
|        | Deceleration         | `LREAL`              | Deceleration time in second in which axis will reach full stop from `Velocity`     |
| Output | Done                 | `BOOL`               | `TRUE`: Motion is finished                                                         |
|        | Busy                 | `BOOL`               | `TRUE`: Function block is executing                                                |
|        | Active               | `BOOL`               | `TRUE`: Block is controlling motion of an axis                                     |
|        | CommandAborted       | `BOOL`               | `TRUE`: Execution of function block was interrupted                                |
|        | Error                | `BOOL`               | `TRUE`: Error has occured during function block execution                          |
|        | ErrorID              | `IC_Error`           | Error identification - see description of type IC_Error                            |

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

### Inverter_Axis_Ref (Class)
The function block serves as drives interface, containing routines as controlling inverter drives. Every drive should be extension of this function block. Function block have to be declared and called in task synchronized with communication (ie. EtherCAT Task). All InOut variables are mandatory for proper inverter control - other input/outputs are not necessary, but certain functionality might not be fully working.

| Scope  | Name                              | Type                  | Comment                                                                         |
| ------ | --------------------------------- | --------------------- | ------------------------------------------------------------------------------- |
| InOut  | ETCSlave                          | `ETCSlave`            | See description https://content.helpme-codesys.com/en/libs/IODrvEtherCATDriver/Current/ETCSlave.html      |
|        | modeOfOperation                   | `SINT`                | Mode of Operation setting, mapped to `6060h`                                    |
|        | modeOfOperationDisplay            | `SINT`                | Mode of Operation display, mapped to `6061h`                                    |
|        | controlWord                       | `UINT`                | Register used for sending control to the drive, mapped to `6040h`               |
|        | statusWord                        | `UINT`                | Register used for recieving status from the drive, mapped to `6041h`            |
| Input  | actVelocity                       | `INT`                 | Feedback from drive with its' actual rotating speed, mapped to `6043h`          |
|        | actPosition                       | `DINT`                | Feedback from drive with its' actual position, mapped too `6064h`               |
| Output | targetVelocity                    | `INT`                 | Target velocity sent to the drive in velocity mode, mapped to `6042h`           |
| Inter  | velocityUnits                     | `IMC_VelocityUnits`   | Variable allows switching velocity units of an axis. Default: `IMC_RPM`. See description of `IMC_VelocityUnits` data type       |
|        | distancePerRevolution             | `LREAL`               | Variable allows to change distance per revolution of an axis, thus user can automatically scale from PUU to engineering units (ie. mm)   |
|        | encoderPulses                     | `DINT`                | Variable allows to change encoder pulses per revolution of a motor (if it has one), and thus scale from PUU to engineering units (ie. mm)                    |
|        | controllerMode                    | `IMC_ControllerMode`  | Controller mode set by the class. See description on IMC_ControllerMode         |
|        | realControllerMode                | `IMC_ControllerMode`  | Real controller mode feedback by inverter drive. See description of IMC_ControllerMode            |
|        | xActDirection                     | `IMC Direction`       | Real direction feedback by inverter drive. See description of IMC_Direction     |
|        | xSetDirection                     | `IMC Direction`       | Set dircetion by the function block to inverter drive. See description of IMC_Direction            |
|        | motionStatus                      | `IMC_Motion_State`    | Actual motion status of the inverter drive. See description of IMC_Motion_State |
|        | axisStatus                        | `IMC_Axis_State`      | Axis state with regard to PLCOpen axis state - with some exclusion, that are not possible to execute on inverter drives (ie. syncrhonized motion). See description of IMC_Axis_State               |
|        | FBError                           | `BOOL`                | Error happend during execution of function block that used this Axis            |
|        | FBErrorID                         | `IMC_Error`           | Error code, that happend during execution of function block that used this Axis |
|        | fActVelocity                      | `LREAL`               | Actual velocity feedback by inverter drive. Scaled regarding variable `velocityUnits`              |
|        | fActPosition                      | `LREAL`               | Actual position feedback by inverter drive.                                     |
|        | fActTorque                        | `LREAL`               | Actual torque feedback by inverter drive. Scale in %                            |
|        | fSetAcceleration                  | `LREAL`               | Set acceleration by the function block to inverter drive                        |
|        | fSetAcceleration                  | `LREAL`               | Set acceleration by the function block to inverter drive                        |
|        | fSetVelocity                      | `LREAL`               | Set velocity bu the block to inverter drive. Scaled regarding variable `velocityUnits`            |
|        | fPrevVelocity                     | `LREAL`               | Velocity feedback in previous iteration of the function block. Scaled regarding variable `velocityUnits`                              |
|        | iActTorque                        | `INT`                 | Actual torque feedback by inverter drive. Scaled in 0.1% integer value          |
|        | diSetAcceleration                 | `UDINT`               | Set acceleration by the function block to inverter drive. Scaled in unsigned integer value    |
|        | diSetDeceleration                 | `UDINT`               | Set deceleration by the function block to inverter drive. Scaled in unsigned integer value    |
|        | byAxisError                       | `BYTE`                | Error code read from inverter axis                                              |
|        | byAxisWarning                     | `BYTE`                | Warning code read from inverter axis                                            |



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

### IMC_Motion_State (Enum)
Enumeration data type, represents direction of set motion.

| Name                    | Initial | Comment                                                                                                            |
| ----------------------- | ------- | ------------------------------------------------------------------------------------------------------------------ |
| IMC_ConstantVelocity    | 1       | Drive is moving with constant velocity                                                                             |
| IMC_Accelerating        | 2       | Drive is accelerating                                                                                              |
| IMC_Decelerating        | 3       | Drive is decelerating                                                                                              |
| IMC_Standstill          | 4       | Drive has zero velocity                                                                                            |

### IMC_Homing_Mode (Enum)
Enumeration data type, represents homing sequences introduced by CoE402 standard. For more information seek description of CoE402 or DS402 standard or manual for device (ie. Delta C2000).

| Name                                                                          | Initial | Comment                                                                                                                                                                            |
| ----------------------------------------------------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| NEGATIVE_DIRECTION_LIMIT_AND_Z_PHASE                                          | 1       | Execute homing in the negative direction until encountering negative limit switch. Then, direction is reversed to locate Z-phase                                                                           |
| POSITIVE_DIRECTION_LIMIT_AND_Z_PHASE                                          | 2       | Execute homing in the positive direction until encountering positive limit switch. Then, direction is reversed to locate Z-phase                                                                           |
| POSITIVE_DIRECTION_ORIGIN_AND_Z_PHASE_IN_NEGATIVE_DIRECTION                   | 3       | Execute homing in the positive direction until encountering reference signal. Then, direction is reversed to locate Z-phase. Stops when encountering positive limit switch                        |
| POSITIVE_DIRECTION_ORIGIN_AND_Z_PHASE_IN_POSITIVE_DIRECTION                   | 4       | Execute homing in the positive direction until encountering reference signal. Then continue to locate Z-phase. Stops when encountering positive limit switch                                      |
| NEGATIVE_DIRECTION_ORIGIN_AND_Z_PHASE_IN_POSITIVE_DIRECTION                   | 5       | Execute homing in the negative direction until encountering reference signal. Then, direction is reversed to locate Z-phase. Stops when encountering negative limit switch                        |
| NEGATIVE_DIRECTION_ORIGIN_AND_Z_PHASE_IN_NEGATIVE_DIRECTION                   | 6       | Execute homing in the negative direction until encountering reference signal. Then continue to locate Z-phase. Stops when encountering negative limit switch                                      |
| POSITIVE_DIRECTION_ORIGIN_AND_Z_PHASE_IN_NEGATIVE_DIRECTION_LIMIT_REVERSING   | 7       | Execute homing in the positive direction until encountering reference signal. Then, direction is reversed to locate Z-phase. Direction is reversed when positive limit is encountered             |
| POSITIVE_DIRECTION_ORIGIN_AND_Z_PHASE_IN_POSITIVE_DIRECTION_LIMIT_REVERSING   | 8       | Execute homing in the positive direction until encountering reference signal. Then continue to locate Z-phase. Direction is reversed when positive limit is encountered                           |
| POSITIVE_DIRECTION_ORIGIN_AND_Z_PHASE_IN_NEGATIVE_DIRECTION_LIMIT_REVERSING_2 | 9       | Execute homing in the positive direction until encountering reference signal. Then, direction is reversed to locate Z-phase. Direction is reversed when positive limit is encountered             |
| POSITIVE_DIRECTION_ORIGIN_AND_Z_PHASE_IN_POSITIVE_DIRECTION_LIMIT_REVERSING_2 | 10       | Execute homing in the positive direction until encountering reference signal. Then continue to locate Z-phase. Direction is reversed when positive limit is encountered                           |
| NEGATIVE_DIRECTION_ORIGIN_AND_Z_PHASE_IN_POSITVE_DIRECTION_LIMIT_REVERSING    | 11       | Execute homing in the negative direction until encountering reference signal. Then, direction is reversed to locate Z-phase. Direction is reversed when negative limit is encountered             |
| NEGATIVE_DIRECTION_ORIGIN_AND_Z_PHASE_IN_NEGATIVE_DIRECTION_LIMIT_REVERSING   | 12       | Execute homing in the negative direction until encountering reference signal. Then continue to locate Z-phase. Direction is reversed when negative limit is encountered                           |
| NEGATIVE_DIRECTION_ORIGIN_AND_Z_PHASE_IN_POSITIVE_DIRECTION_LIMIT_REVERSING_2 | 13       | Execute homing in the negative direction until encountering reference signal. Then, direction is reversed to locate Z-phase. Direction is reversed when negative limit is encountered             |
| NEGATIVE_DIRECTION_ORIGIN_AND_Z_PHASE_IN_NEGATIVE_DIRECTION_LIMIT_REVERSING_2 | 14       | xecute homing in the negative direction until encountering reference signal. Then continue to locate Z-phase. Direction is reversed when negative limit is encountered                           |
| NEGATIVE_DIRECTION_LIMIT                                                      | 17       | Execute homing in the negative direction until encountering negative limit switch. Use negative limit swtich as the origin                                                                        |
| POSITIVE_DIRECTION_LIMIT                                                      | 18       | Execute homing in the positive direction until encountering positive limit switch. Use positive limit swtich as the origin                                                                        |
| POSITIVE_DIRECTION_ORIGIN                                                     | 19       | Execute homing in the positive direction until encountering reference signal. Use reference signal swtich as the origin after reversing. Stops when encountering positive limit switch            |
| POSITIVE_DIRECTION_ORIGIN_2                                                   | 20       | Execute homing in the positive direction until encountering reference signal. Use reference signal swtich as the origin after reversing. Stops when encountering positive limit switch            |
| NEGATIVE_DIRECTION_ORIGIN                                                     | 21       | Execute homing in the negative direction until encountering reference signal. Use reference signal swtich as the origin after reversing. Stops when encountering negative limit switch            |
| NEGATIVE_DIRECTION_ORIGIN_2                                                   | 22       | Execute homing in the negative direction until encountering reference signal. Use reference signal swtich as the origin after reversing. Stops when encountering negative limit switch            |
| POSITIVE_DIRECTION_ORIGIN_REVERS_LIMIT_REVERSING                              | 23       | Execute homing in the positive direction until encountering reference signal. Use reference signal swtich as the origin after reversing. Direction is reversed when positive limit is encountered |
| POSITIVE_DIRECTION_ORIGIN_REVERSE_LIMIT_REVERSING_2                           | 24       | Execute homing in the positive direction until encountering reference signal. Use reference signal swtich as the origin after reversing. Direction is reversed when positive limit is encountered |
| POSITIVE_DIRECTION_ORIGIN_CONTINUE_LIMIT_REVERSING                            | 25       | Execute homing in the positive direction until encountering reference signal. Use reference signal swtich as the origin after continuing. Direction is reversed when positve limit is encountered |
| POSITIVE_DIRECTION_ORIGIN_CONTINUE_LIMIT_REVERSING_2                          | 26       | Execute homing in the positive direction until encountering reference signal. Use reference signal swtich as the origin after continuing. Direction is reversed when positve limit is encountered |
| NEGATIVE_DIRECTION_ORIGIN_REVERS_LIMIT_REVERSING                              | 27       | Execute homing in the negative direction until encountering reference signal. Use reference signal swtich as the origin after reversing. Direction is reversed when negative limit is encountered |
| NEGATIVE_DIRECTION_ORIGIN_REVERSE_LIMIT_REVERSING_2                           | 28       | Execute homing in the negative direction until encountering reference signal. Use reference signal swtich as the origin after reversing. Direction is reversed when negative limit is encountered |
| NEGATIVE_DIRECTION_ORIGIN_CONTINUE_LIMIT_REVERSING                            | 29       | Execute homing in the negative direction until encountering reference signal. Use reference signal swtich as the origin after continuing. Direction is reversed when negative limit is encountered|
| NEGATIVE_DIRECTION_ORIGIN_CONTINUE_LIMIT_REVERSING_2                          | 30       | Execute homing in the negative direction until encountering reference signal. Use reference signal swtich as the origin after continuing. Direction is reversed when negative limit is encountered|
| Z_PHASE_IN_NEGATIVE_DIRECTION                                                 | 33       | Execute homing in the negative direction unitl Z-phase is detected and used as the origin. Stops when encountering negative limit                                                                        |
| Z_PHASE_IN_POSITIVE_DIRECTION                                                 | 34       | Execute homing in the negative direction unitl Z-phase is detected and used as the origin. Stops when encountering negative limit                                                                        |
| CURRENT_POSITION_AS_ORIGIN                                                    | 35       | Actual position is set as the origin     |


##TODO
- [x] Testing velocity mode
- [x] Add reading ControllerMode and VelocityUnits
- [x] Switch to Inverter Function Block from structure
- [x] Test Inverter class
- [x] Add Homing mode
- [x] Tested Homing mode
- [ ] Add Positioning mode
- [ ] Test position mode
- [ ] Add jog
- [ ] Add Torque mode
- [ ] Test torque mode
- [ ] Test mode switching on the fly
- [ ] Testing on AX3 (different library for ETC_SDO)
