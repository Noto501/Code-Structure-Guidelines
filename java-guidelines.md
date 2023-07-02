# Java/Robot Code Standards for Team 2637

Refrencing 3476 [Java Code best practices](https://github.com/FRC3476/style-guides/blob/master/java-style-guide.md) by @jackw01, @AraknosDe
and [2637 Code Standards Presentation](https://docs.google.com/presentation/d/1sKjgOOEfPU-1lpW2Q55ssY6NsyhUO9tG/edit#slide=id.p5) (Updated October 2022 version)

Author: [Kynam Lenghiem](https://github.com/Noto501)

## Introduction
---
These coding standards have been created to:
- Make software easier to understand
- Make software easier to update/maintain
- Reduce the frequency of software compile/runtime errors.


## Project Structure
---
Timed based Model:
- **Autonomous folder** contains files related to controling a robot during autonomous phase of a match.

- **DataLogger folder** contains files related to the datacollection of specfic variables for debuggging and tunning of the robot.

- **Mechanisms Folder** contains files related to the operation of each of the robot mechanisms. 

    Mechcanism: A mechanical subsystem used to perform an operation of a robot. (ie Drivetrain allows the robot to move on a gamefield)

- **Robot Folder** contains files related to controling the main robot code loop that will periodically call various robot methods to control the robot based on operator/code input

<font size = 4> Example project structure:</font>

![alt text](/resources/example-1.jpg)

    


## Editor settings
---

### Auto Save
Auto Save should be enabled/checked for VS code as shown in this example.

This is done as a convience when comitting and pushing code to a github repository.

Files --> autosave

![alt text](/resources/Screenshot%202023-07-01%20214108.png)

## Formatting
---


## Naming
---

### General Guidlines
- Names should be meaningful
- Long names > short names 
- Avoid giving classes, methods, constants, or variables that have similar meanings different names.
- 

```java
LIFT_ENCODER_DIOA			= 2;	//’A’ should go with Encoder
LIFT_ENCODER_CH_A_DIO_PORT	= 2;  //Better


run()								//What are we running?
runLiftMotor()


MAX_TEMPERATURE            = 85.0;	//Max Temp for what?
MAX_LIFT_MOTOR_TEMPERATURE = 85.0;
```
- Avoid variable names starting with possesives/articles (i.g. a, an, my)

```java
//bad
private static CatzElevator;

public void RobotInIt()
{
    CatzElevator myCatzElevator = new CatzElevator();
}

//better
private static CatzElevator;

public void RobotInIt()
{
    CatzElevator elevatorSubsystem = new CatzElevator();
}
```
- Names in code should match represented names on a User Interface(i.g. advantagescope, shuffleboard) 

This is done to correctly and quickly associate a screen name with a variable name in code.

```Java
//bad
public void smartDashboardElevator()
    {

        SmartDashboard.putBoolean("Low Limit Switch", lowSwitchState);
        SmartDashboard.putBoolean("High Limit Switch", highSwitchState);

    }
//better
public void smartDashboardElevator()
    {

        SmartDashboard.putBoolean("lowSwitchState", lowSwitchState);
        SmartDashboard.putBoolean("highSwitchState", highSwitchState);
    }
```


### 1. Class Naming Convention
- Format: noun
- Style: First letter of each word is ***uppercase***


```java
class States;
class GameHandler;
```


### 2. Method Naming Convention
- Format: verb/action + noun
- Style: `Camel Case` (First letter is all ***lowercase*** + first letter in each supsequent word is ***uppercase***)


```java
openForearm()
readLiftLimitSwitchTop()
```


### 3. Variable Naming Convention
- Format: verb/action + noun
- Style: `Camel Case` (First letter is all ***lowercase*** + first letter in each supsequent word is ***uppercase***)

```java
public static Encoder drvTrainEncoderL;
public static Encoder liftEncoder;
```

- Include ***units of measurment*** when applicable.

```java
public static double distanceInFt;
```

- Avoid reusing variable names in different scopes in the same method unless the variables will be set equal to each other using the "this." keyword
```java
private int cmdStateUpdate
void cmdProcElevator(int cmdStateUpdate)
{
    this.cmdStateUpdate = cmdstateUpdate
}
```




### 4. Constants Naming Convention
***"\*" = if applicable***
- Format: [Min/Max]*_[subsystem name]\_[noun]\_[noun]\*
- Style: `UPPER_SNAKE_CASE`


```java
public static final LIFT_ENCODER_CH_A_DIO_PORT = 2;
public static final MAX_LIFT_MOTOR_TEMP = 85.0;
```

### Standard Names & Abbreviations & Acronyms
- When picking abbreviations that have multiple values (e.g. left/right or left/middle/right), the abbreviation for each value should have the ***same number of letters.***

```java
private double turnSpeedLT = 0.5;
private double turnSpeedRT = -0.5
```
This helps keep variables aligned

- Acronyms should always be capitalized.
- Only use ***understood and standardized*** acronyms for variable names (i.g. HTTP, CAN, PID, I2C). 
- Do not abbreviate unless the abreviated word is ***commonly understood.*** (i.g. Id, Pos)


<font size = 4> Current Abreviation Standards: </font>

- ***Fwd, Rev***	= Forward, Reverse
- ***LT, RT***	= Left, Right
- ***MC***		= Motor Controller
- ***pd***		= PDP or PDH  
- ***Xxx***		= Encoder
- ***i*** = loop iterating in a for conditional statement or 1 dimensional array.
- ***i,j,k*** = for 2/3 dimensional arrays.
- ***x, y, z*** = for representing cordinates in a 3 dimensional space.


### Short Variable Names
Single letter variable names should not be used unless used in a conditional 
```java
int armNumCount = 0;
for (int i = 0, i <= 10, i++)
{
    armNumCount++;
}
```



## Documentation/Commenting
---




