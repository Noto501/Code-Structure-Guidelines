# Atlas Code structure



Author: [Kynam Lenghiem](https://github.com/Noto501)

## Introduction
---
It is highly recommended that you download the latest copy of [Timed Atlas Code](https://github.com/PhantomCatz/Timed_Based_Atlas_before_Socal) to follow along with the presentation.


Atlas Robot structure
- Robot.java
- Elevator
- arm
- intake
- drivetrain
- autonomous
- Led

## Code Overview (Teleop) (Robot.java)
### Teleop Periodic CMD Scheduler

This is a general overview of how commands are determined for each of the mecahnisms.
1. drivetrain values are collected and sent to the drivetrain class
2. xbox values are collected and assigned to variables.
3. Variables collected from xbox and from the currently held gamepiece(Cone, cube, none) determine which "state" the robot is in with the "determineCommandState" method.
4. A integer is assigned to each one of these states
(high node cube = 1, pickup ground = 3, etc.)
5. These variables, along with any manual control information is sent to each of mechanism(as denoted in **red**)(ie. "CommandStateUpdate" represents the newly commanded state as determined by the xbox controls)
6. Mechanisms use this information to set their motors/sensors/mechanical stuff...etc.
7. After all mechanisms recieve their integer denoting their state, the "robot state" is set to "null" until another xbox controller input is made.
![alttext](/AtlasResources/teleop%20cmd%20scheduler%20picture.png)

List of all states the robot could be in...and of all gamepiece configs

![alttext](/AtlasResources/constants.png)

## General code structure of mechanisms

Scoring mechansims(arm, intake, elevator) are gnerally divided into this code structure:

![alttext](/AtlasResources/arm%20code%20structure%20example.png)



## Elevator (CatzElevator.java)
The elevator code is based around a "cmdprocessing" method to use the information given by the teleopCmdSchdeuler to decide what position the elevator should be at. 

The elevator has these different states it can be at:
- high
- MidCone
- MidCube
- Low
- Pid Manual(Use of the Pid controller in tandem with a joystick to manually control the elevator)
- Full Manual(Full negation of Pid controller...elevator is fully controlled by joystick)

CmdprocessMethod example:


![alttext](/AtlasResources/elevator%20cmdprocelevator.png)

The mechanism uses a motor based pid controller to ease into the different position states when called
This is an example of a pid implementation configuration of the elevator motor before setting a value to the motor:

![alttext](/AtlasResources/configpidElevator.png)

The elevator mechanism also has a thread(continous while loop) that performs any elevator position recording and tracking the postion of the arm(See below for elaboration)

- Due to the high risk of smashing the intake into the mid node during scoring sequences, during any decent stages(ie COMMAND_UPDATE_STOW from COMMAND_UPDATE_SCORE_HIGH_CONE), the thread keeps track of the arm position...only moving the elevator down once the arm is in the retract position

![alttext](/AtlasResources/elevator%20thread.png)

## Arm  (CatzArm.java)
The arm code is based around a cmdprocessing method to use the information given by the telopCmdSchdeuler to decide what position the arm should be at.

the mechanism uses a pid controller within the motor to set the power applied to a motor.

Similarly to CatzElevator, a thread is used during the acsent stages to track the elevator position, only setting the arm to move after the elevator has reached a certain height(to avoid colliding with the mid nodes)

## Intake (CatzIntake.java)
The intake code is based around a cmdprocessing method to use the information given by the telopCmdSchdeuler to decide the "target Angle" for the intake and if the intake is in manual/state set mode.

Once a target angle has been defined, a wpilib pidcontroller is used in a thread to determine the right power to set the motor at. Do to the volitile nature of the intake at differing angles, a "Fine tunning" controller and a "aggresive" pid controller are used in conjunction to set the right power to the intake.

Picture below denotes the intake thread that contains the pid controllers:

![alttext](/AtlasResources/intake%20thread.png)

## other mechanism features
Within CatzArm and CatzElevator, there are methods for reseting the motor encoder used to determine the mechaisms respective positions. This is useful for recalibrating the accurate positions for the the resepctive mechanisms

![alttext](/AtlasResources/limitswitch%20example.png)

## Manual Control
For the arm motor, manual control is solely just translating a trigger value into a percent value range that can be applied to the motor.

For the intake/elevator, manual control is determined by creating a new target position or target angle from the inputted joystick values.


## Drivetrain (CatzDriveTrain.java)
The Catzdrivetrain also uses a cmd proccessing method to determine what state it should be in(Modified drive or regular drive) or what power to apply to each of the steering/driving motors.

The mechanism uses this information to calculate a steer angle and a drive power.(Using Calcjoystickpwr and CalcTurnpwr methods)

The calculated power, steering angle, and a gyro angle is sent to the method translate turn/drive/rotateinplace(mehod used is determined by if the robot is turning in place/ driving straight/ or making a diagonal "translating" movment) to determine what information to sent to each of the 4 swerve modules. 

Below is the method for "translateturn"

![alttext](/AtlasResources/transate%20turn%20example.png)

In the individual swerve modules, the modules determine the optimal wheel angle to set the motors at. Along with using a mag enc to determine the current angle of the turn wheel, the pid controller within the swerve module uses the error between the two to set power to the turn motor.

The modules also collect the calculated drive power from the Catzdrivetrain and just apply it to the drive motors.

Example of catzSwerveModule
![alttext](/AtlasResources/closets%20angle%20swerve%20module.png)


## Autonomous

![alttext](/AtlasResources/auton%20mapping.png)

- "CatzAutonomous" house any resusable functions such as "drivestraight", distance recording, "turninplace"

Example from CatzAutonomous "Turninplace"
![alttext](/AtlasResources/drivestraighauton.png)

- "CatzAutnomousPaths" house all paths that atlas will use during autonmous. Paths are chosen through shuffleboard through "senablechoser"
Excerpt from catzAutonomous paths:
![alttext](/AtlasResources/senable%20choser%20example.png)
- "Balance"
This is the class that autobalances the robot during autonomous.
Taking in a navX gyro angle...the robot determines the best power to apply to the swerve modules using a clamped pid controller.

Atlas autonomous is sequential(step based). In any given autonomous path, One action must be completed before another one can start.
Example autonomous path:
```java
   public void SidePickup(){
        double direction;

        if(chosenAllianceColor.getSelected() == Robot.constants.RED_ALLIANCE)
        {
            direction = RIGHT;
        }
        else
        {
            direction = LEFT;
        }

        Robot.auton.DriveStraight( 30, FWD_OR_BWD, 2.0);     //From Grid to area to do 180 deg turn

        Robot.auton.TurnInPlace(180, 2.0);

        pickUpCube();
        
        Robot.auton.DriveStraight(176, FWD_OR_BWD, 5.0);  
        Timer.delay(0.1); 
        // stow();

        if(direction == 90.0)
        {
            Robot.auton.TurnInPlace(-180, 2.0);
        }
        else
        {
            Robot.auton.TurnInPlace(180, 2.0);
        }
        stow();

        Robot.auton.DriveStraight(-212, FWD_OR_BWD, 5.0);
        Robot.auton.DriveStraight( 36,  direction, 2.0);
    }
```
