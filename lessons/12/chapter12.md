---
title: Working with Animated Models
---
To work with animated models, first select the "extract additional models" menu item.

Additional animated models are now available to be chosen in the Scene Creator.

# Video Overview

<iframe width="560" height="315" src="https://www.youtube.com/embed/PAJaedS4YaI?rel=0" frameborder="0" allowfullscreen></iframe>
 
# Generating animated models classes

Start the Scene Creator, select the appropriate background and ground texture, and put the Diablous model into your world.  The following class will be generated.

```java
import java.util.HashMap;
import java.util.ArrayList;

/**
 * This class is generated by the env3d plugin to make
 * it easier to work with models.  It scans a model
 * directory for all the obj files and organized them.
 */
public class Diablous extends GameObject
{
    // The various states of this model.  Each animation is a state
    public static final int IDLE = 0;
    public static final int HIT = 1;
    public static final int DIE = 2;
    public static final int ATTACK = 3;
    public static final int RUN = 4;
    public static final int WALK = 5;
    public static final int IDLE2 = 6;
    // The main data structure for animated models, a HashMap of ArrayList
    private HashMap
```

Look at the move() method carefully.  You can see that animation is achieved by changing the model field to a new obj model every time move() is called.  In the above generated code, all of the models are stored in the modelsMap data structure, which is a HashMap of ArrayList.  Each of the key of the HashMap is an integer representing the particular animated action.  By setting the state field to the correct integer, the appropriate animation will be played when the move method is called.

In the above case, the state variable is set to 0 (the default value), so the idle animation is called when the application runs.

 
# Changing the animation

Edit the state field initialization line so that the Diablous starts with the WALK animation instead of IDLE.

```java
 private int state = WALK;
```

Re-run your program and you'll see that the WALK animation now runs instead of IDLE.

 
# Adding 3rd person camera control

Since the move() method is called every frame, we can alter the behavior of our Diablous object by modifying the move() method. One of the built-in method is simpleFPSControl(), puts the camera behind the object as well as allow simple WASD and mouse control of our character. This method takes a number as its parameter, this parameter indicates the speed of the character's movement.  Modify the move() method as follows:

```java
    public void move() 
    {
        // allows the user to control an object of Diablous
        // (should only have one Diablous object in our game
        simpleFPSControl(0.1);
        
        setModel(modelsMap.get(state).get(frame));
        frame = (frame+1) % modelsMap.get(state).size();
    }
```

Run your program now and you will be able to control the Diablous character.

 
# Responding to user input

A different animation can be played by calling setState() with the correct parameter.  In our case, we want the ATTACK animation to play when the LEFT mouse button is clicked.  You can achieve this by adding the following code to the move() method:

```java
    public void move() 
    {
        // allows the user to control an object of Diablous
        // (should only have one Diablous object in our game
        simpleFPSControl(0.1);
        
        // change attack animation when the left mouse button is clicked
        if (getEnv().getMouseButtonClicked() == 0) {
            setState(ATTACK);
        }
        
        setModel(modelsMap.get(state).get(frame));
        frame = (frame+1) % modelsMap.get(state).size();        
    }
```

What happens when we run this program?  Does it work as expected?
 
# Resetting the animation

The above code looks fine, expect that the animation is "stuck" at attack after our mouse click.  We would like to return the animation back to IDLE after the ATTACK animation finishes.  To achieve this, we add the following piece of logic:

```java
    public void move() 
    {
        // allows the user to control an object of Diablous
        // (should only have one Diablous object in our game
        simpleFPSControl(0.1);
        
        // change attack animation when the left mouse button is clicked
        if (getEnv().getMouseButtonClicked() == 0) {
            setState(ATTACK);
        }
        
        setModel(modelsMap.get(state).get(frame));
        frame = (frame+1) % modelsMap.get(state).size();       
        
        // Reset animation to WALK after all other animations have finished
        // playing.  We can detect the end of any animation run by checking 
        // our frame counter.  Any time the frame variable is 0, we know 
        // our animation has looped.
        if (frame == 0 && getState() != WALK) {
            setState(WALK);
        }
    }
```
 
# Exercises

1. Add a Cerberus (a 3 headed dog) into your game.  Have the Cerberus chase after you by:
    1. Make the Cerberus play the WALK animation
    1. Have the Cerberus face and walk towards your character
1. Modify your Diablous character such that:
    1. Shoots a ball out from Diablous' location when the attack animation reaches the midpoint
1. When the ball hits the Cerberus, have the Cerberus' DIE animation played.  At the end of the DIE animation, do not animate the Cerberus anymore.

Your completed exercise should look similar to the following:

<iframe width="560" height="315" src="https://www.youtube.com/embed/nyhFBP3Qx88?rel=0" frameborder="0" allowfullscreen></iframe><br />