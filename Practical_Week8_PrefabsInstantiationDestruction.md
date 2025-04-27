# Week 7 -- Prefabs, Instantiation, Destruction --

Learning outcomes:

* How to create prefabs from objects in the scene & create instances from prefabs.
* How to change parameters on prefabs & instances, and the effects of each.
* How to instantiate prefabs in script (including position, rotation & parent)
* How to destroy gameobjects.
* How to use subgraphs to organise code.

## Foreword

Today we'll be adding the final bit of content to our little asteroid game we've made. At this point, everyone's game is going to be unique with all of the various options they've decided to put in, so by the end of the prac this is likely something you should feel ownership over. So if this is your first game, good job!

Regarding what we're doing today, we're going to be going back to the asteroids and making them a bit more fun and lively an object, as opposed to just being an entity that exists in your scene for a brief moment. The overall content today is quite light, but a tad more complex, so take your time with it (or if you finish early, enjoy the extra time to work on challenges)

## Setting up

As always, make sure you've completed the previous weeks work before continuing on today (a lot of students did not finish the final part of the state machine - don't feel bad if you're still up to that last task). You should have finished:

* A UFO with different states and transitions between them
* The aforementioned UFO is able to move between patrol points
* The aforementioned UFO can chase the player if it gets too close

Otherwise, export your previous weeks work into a package, and import it into this weeks repository. Once imported, verify that everything is there and functioning correctly.

Note: **You will likely need to create your Layers again (again), as well as redo your Layer Collision Matrix**. You will also likely need to regenerate your visual scripting nodes (`Edit > Project Settings`, navigate to the `Visual Scripting` tab, and then press the `Regenerate Nodes`).

## Expanding the Asteroid Functionality

To start todays prac, make two empty game objects. Give them the following names:

* `Spawner_Asteroid`
* `Destroyer_Asteroid`

After you've done this, make them into a prefab by dragging them from the **Hierarchy**, to your Project window (ideally in to the `Prefabs` folder).

To make the spawner function as intended, we have to change a bit of functionality to the Asteroid, and have it do the following:

1. Uses the `Find` node to find the player

* The `Name` in this node is whatever the player prefab name is (in the scene)
* Once we've found the player (game object), we can get it's transform

2. Uses a speed value, that is randomly generated (`0.5` for min, and `3` for max is recommended, but you can tweak these as you see fit)

* You should create a new variable for speed
* This value should be set in `OnStart`
* You should simply expand the movement implementation you already have

3. Using the player as a heading, moves in a straight line

* This heading should be set in `OnStart`, alongside the speed
* Using the same logic as the bouncing feature - where it can bounce and move to a different direction

This will likely be your first time using the `Find` node, here's what it looks like

<img src="PracResources\images\01_FindNode.png">

Find is a pretty useful function that simply goes through the **Hierarchy** and selects an object that matches our input. This can be handy when we don't actually have something that we want to interact with/use, but we don't have a reference for it. However, it is a good idea to use this node sparingly, as `Find` can be a fairly computationally expensive operation when there are a large amount of objects in a scene

Have a go at implementing the above by yourself. Once you're ready to test this, move your Asteroid to the top of your the game screen (ideally outside of the `Main Camera` view)

Of course, here's a solution is below if you get stuck

<img src="PracResources\images\02_AsteroidMoveToPlayer.png">

<img src="PracResources\images\03_AsteroidSpeedCalc.png">

Once you've checked that the Asteroid works as outlined above, make sure you have it saved as a prefab, then delete it from your scene -- we'll be spawning them in over time

## Asteroid Spawner

If you have not already, make a new script graph for `Spawner_Asteroid`, and call it something sensible (like `Spawner`), and save it in an appropriate place.

Before we get started, make the following variables for our `Spawner_Asteroid` object:

* `Asteroid` - Type: GameObject:-  Holds the prefab reference for our Asteroid
* `spawnDelay` - Type: Float:- Will be used to re**set** our timer, once we have spawned an asteroid. We will be setting this in advance
* `spawnCooldownRemaining` - Type: Float:- Will be used as our timer. Once this reaches 0, we spawn an asteroid and restart the timer. This value will change dynamically, so we don't need to give it a value/can leave it as 0

### Instantiating

This will likely be a new term to a lot of you, but simply put, in this context **Instantiation** is used to create clones of an object (but ideally, a prefab).

When we clone objects, we can specify a few things like its position and rotation, but otherwise default to the objects original position and rotation, among other variables. This means that we have to be certain that our prefab is set up correctly before we start instantiating things during runtime. There are a few other quirks about instantiation that you'll likely pick up over time, but this is enough to have a solid idea of how to use it, especially when it concerns making asteroids.

Unity has a few different types of Instantiation nodes, and we'll be looking at a few today:

<img src="PracResources\images\04_Insantiators.png">

Let's start off simple. Create a new node using `Component: Instantiate (Original)` and set it up like the following

<img src="PracResources\images\05_InsantiateOriginal.png">
<img src="PracResources\images\06_InsantiateOriginalSetUp.png">

Double-check your `Asteroid` variable in your spawner contains a reference to the prefab (use the object picker button if you're not sure, or simply re-drag the prefab back in there), and play your game. You'll notice that we've run into a fairly unintuitive problem -- The Asteroid spawns wherever it wants, not in the center of the spawner. However, reading above, it is correct that the game object would spawn at the prefabs position and rotation if not given a new one.

Delete the instantiate nodes. Instead we are going to need the `Component: Instantiate (Original, Parent)` node

<img src="PracResources\images\07_InsantiateAtParent.png">

Assign a reference to the parent using a `This` node. It should look like the following:

<img src="PracResources\images\08_InsantiateAtParentInitial.png">

Test your game, and hopefully it should work as expected - if Asteroids are still spawning at random points, check that the Asteroid prefab itself has its `Position` set to `0,0,0`.

As an aside, if you check your **Hierarchy** there is one caveat to this solution, the asteroid is a child of the spawner. Grouping similar objects makes sense, but  this might not be something we want to do, especialy when the game goes up in complexity and scale. Thankfully, resolving this is easy, we can simply detatch the asteroid using a `Detach Children` node - you could easily guess what this does based on its name alone

<img src="PracResources\images\09_InsantiateAtParentFinal.png">

At this point, it is recommended to save your prefab. If you have been editing directly from the prefab, great! You won't need to make any changes, and your spawner should be retroactively changing based on your changes. Otherwise, select the `Spawner_Asteroid` in the **Hierarchy**, click the `Overrides` dropdown, and select **Apply All** - this will apply all changes to objects in the scene **and** save the changes directly to the prefab.

<img src="PracResources\images\09_InsantiateAtParentFinal.png">

There are a few other ways you can save changes (for example, you might only want to save the changes for one component), ask your TA about this

<img src="PracResources\images\bonus_SavePrefab.png">

At this point our spawner functions, but we still want it continuously spawn Asteroid prefabs. We want to use the variables we made before to occasionally spawn another copy of the asteroid. Lets break this problem down into smaller chunks to help wrap our head around the logic for this.

### Making a timer

As you can imagine, having some sort of measure to count down is quite important when making video games generally speaking -- pretty much every game you can think of would use an implementation of a timer. Thankfully, implementing a timer is quite straightforward when you break down the logic:

For us, our timer will have three intrinsic components:

1. The first part should subtract the `spawnCooldownRemaining` from the time between the last frame (delta time). This can be done by getting the result of the two (`spawnCooldownRemaining` - DeltaTime) and simply setting `spawnCooldownRemaining` as the new output
2. The next part should check if `spawnCooldownRemaining` is currently less than (or equal to) zero. Using both a `Less or Equal` node and a `If` node seems sensible here, as  `Less or Equal` outputs a boolean that `If` can use
3. If the result of the If is true, reset our `spawnCooldownRemaining` back to whatever `spawnDelay` is set to
4. Spawn an asteroid (ideally using the same function as before, or using a copy/paste of the nodes)

Have a go at implementing this yourself.  Play your game and check your results.

If you get stuck implementing this, check the above logic and check the following:

* Your nodes are correctly using get (or set) when appropriate
* Check your timer is actually counting down (and resetting back to zero)

Of course, you can check the below solution if you're *really* stuck

<img src="PracResources\images\10_TimerSolution_p1.png">
<img src="PracResources\images\11_TimerSolution_p2.png">

## Asteroid Destroyer

Add a `BoxCollider2D` to your `Destroyer_Asteroid` object, and move it to the bottom of your screen. Drag it to the bottom of the screen (again, ideally outside the view of the player) and change the `Size` of the collider to something like `X:50 Y:2`. We want it to be extremely wide, so it can catch the asteroid from essentially any angle it spawns at.

Like before, if you have not already made a  script graph for `Destroyer_Asteroid` do so now.

Before starting, you may want to disable our UFO enemy , as it will definitely get in the way (or move to the QA scene)

You will be implementing this without much direction, as this is simply doing a different implementation/variation of things you've already done. Still, here's the logic to help you think about how to implement this:


|   |                                                                                                                                  Destroyer logic                                                                                                                                  |   |
| --- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | --- |
|   | Similar to our how our player checks for layers, we want to check if the incoming object is on the**Asteroid** layer. If it is, we want to destroy the object. You will not need a Rigidbody. You should put the Destroyer on its own Layer, as well as use the `IsOnLayer` node. |   |
|   |                                                                                                                                                                                                                                                                                   |   |

There will be several steps to this process, so don't feel afraid to ask for advice from the TA, however this is nothing you have not done before. If you're stuck, check the following:

* Your Layer Matrix has been set up correctly
  * Check everything is on the correct layer
* Look at how player collision was implemented
* Verify that the `IsTrigger` box on both your Asteroid and your Destroyer is correctly set up.

If you're still stuck, here's a script graph solution. However, **you will still need to check the above mentioned points**, simply copying this won't be the entire solution

<img src="PracResources\images\12_DestroyerSolution.png">

Now, we can't have a space game with a ship without shooting. Let's think about everything we've learnt so far this semester and add in shooting.

## Implement shooting

First things first, you'll need to manually create a shooting input for the player (using the `PlayerInput` Input Action Asset we previously made). Something like this will work fine (keep in mind that this might look different for you, depending on which challenges you've completed) - just note that `Spacebar` is already being used for boosting:

<img src="PracResources\images\13_ShootingInputAsset.png">

We will also need to make a prefab for our bullet. We could make a 2D Sphere (right-click in the **Hierarchy**, and instead of `Create Empty` , select `2D > Sprites > Circle`). Alternatively, there are a few sprites you could use in the Kenny.NL package.

Once you have your bullet object, add a collider to it (keep it simple and use a `CircleCollider2D`), add a script, and save it as a prefab. Then, delete if from your scene, as we don't want a bullet sitting in the middle of our scene. Before moving on, make sure the prefab has an appropriate name.

Once you've set up the bullet prefab, create a variable for the bullet on your player object, and reference the newly created bullet prefab (**not** the bullet that was in the middle of your scene that you should have just deleted).

Go into your player script and implement functionality for the player to Instantiate bullets when they press the `Shoot` key (or whatever action you called it). Copy the implementation of your Asteroid spawner, but just change OnStart to instead be triggered by the Input Action

<img src="PracResources\images\14_ShootingInput.png">

As you might have guessed, our bullets could work a lot like asteroids. To keep things simple, you can essentially copy most of the logic over from the asteroids to implement our shooting. Change the following:

For the prefab:

* The bullet should be smaller than the Asteroid (change the `Transform` - `Scale` of the prefab)
* The bullet should have the same variables as the Asteroid - `speed` and `direction`
  * `speed` should be a number that is *much* faster than the ship
  * `direction` should be a `Vector 2`: `X:0 Y:1`
* As we want to spawn the bullet **in front** of our ship. The simplest way to do that is to change the starting location of our bullet prefab -- one of the challenges involves not taking this approach, but please do this for now.
* Movement for the bullet would be the same as the Asteroid, but obviously the speed value would be different (you'd expect the bullet to be smaller, but a lot faster than the Asteroid)

In our script:

* Our movement/`OnUpdate` would be the exact same
* Our Initial starting parameters/`OnStart` should be slightly different, as we want to move towards away from the player (look at how the Normalized vector is being calculated - we want the inverse of this)

This should not take long to implement, again this is nothing you have not done before & we encourage you to copy/paste here to save time:

<img src="PracResources\images\15_InitialBulletOnStart.png">
<img src="PracResources\images\16_InitialBulletUpdate.png">

Play your game - fly around and shoot. Ideally you should have the following behaviour:

* Your bullets change direction based on which way your ship is rotated
* Your bullets come out out fast!
* Your bullets fly out in front of your ship, and keep going forward relative to the rotation of your ship (when the bullet spawned)

## Rotating the bullet on spawn

You might notice one minor issue with our bullets though, they seem to be randomly rotated. You could choose to keep this, especially if you're using one of the sprites. Regardless, to fix this the most obvious change would be setting our bullets rotation to match our ships rotation (at the time the bullet spawned). This is a fine solution for now, but as you can imagine, this isn't a good solution for more complex games. Still, sometimes the best solution is the simplest.

* Using `Find`, get the rotation of the player, and drag the output to a `Get Rotation` node.
* Make another `Get Rotation` node - this will be used for this objects rotation
* Create a `Quaternion: Equals` - we'll be using more **Quaternion** nodes in the near future.

Quaternion: Equals simply sets the rotation of an object to match another object. There is another simple way to accomplish the same thing (using Transform: Rotate & Euler Angles), but Quaternions are usually a better option if you can use them, and in this case, it wins out in terms of simplicity.

<img src="PracResources\images\17_QuatEquals.png">

Have a go at implementing that. Minor note, as alluded to above, using `Find` is computationally expensive. Where possible try and reuse the same node/result (this also arguably makes a script graph look more elegant).

When you finish that, your OnStart function should look something like this

<img src="PracResources\images\18_BulletOnStart.png">

## Finishing touches

### Destroying Asteroids

While we can shoot, it doesn't make sense that it simply passes through objects. You could expand the functionality to hit other enemy types, but for now implement the following:

* Asteroids should be destroyed upon contact with a bullet, similar to how the destroyer functions (consider where this functionality should exist - the bullet or the asteroid)
* When colliding with an Asteroid, the asteroid should be destroyed *first*, then the bullet
  * If the bullet is destroyed first, the code that tells the asteroid to be destroyed (likely) cannot run

Have a go at implementing this yourself. If you get stuck check the following:

* Your Layer Matrix has been set up correctly
  * Check everything is on the correct layer
* Look at how player collision & asteroid destruction was implemented
* Verify that the `IsTrigger` box on both your Asteroid and your bullet is correctly set up.


|   |                                             |   |
| --- | :-------------------------------------------: | --- |
|   | There is no provided solution for this step |   |
|   |                                             |   |

### Despawning after Time Period

While shooting these bullets is fun, over time the **Hierarchy** would become cluttered with objects that aren't even on screen (and our game would inevitably lag).

Implement a timer system. Again, most of the logic can be copied over from our `Spawner_Asteroid`, the only difference would be that you're destroying an object at the end, not Instantiating


|   |                                             |   |
| --- | :-------------------------------------------: | --- |
|   | There is no provided solution for this step |   |
|   |                                             |   |

Congratulations, you've finished the first practical game. As mentioned in the Foreward, this is the final prac we will be spending on this game -- we will be making something entirely new next lesson. If you would like, you can post this to your itch.io page you made earlier in the semester as your first portfolio piece.

As always, here are some challenges from you to pick from. Please complete at least **TWO** for today.

## Recommended challenge: Use a bullet spawner

We somewhat cheated in this prac, and simply changed the starting position of our bullet prefab in order to spawn it in front of our ship. Ideally though, we would have a set point in front of the ship -- this is typically done using an empty game object, that is a child of our object. This empty object should probably be a variable for the player, and the bullets position should be set to the position of the spawner.

Note that a correct solution would have the prefab `Transform` - `Position` be `0,0,0`. Make sure this is changed (as the direction vector will also need to be revised).


|   |                                                                                                                                                                                          Caution                                                                                                                                                                                          |   |
| --- | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | --- |
|   | This is tricker than you might first think. The`Detach Children` node detaches **ALL** children from an object, including important children, like a spawner. You will likely need to move some things around (and perhaps make new script graphs) in order to navigate around this issue. Also, note that a final solution may not need the spawner as a variable for the Player script. |   |
|   |                                                                                                                                                                                                                                                                                                                                                                                           |   |

## Recommended challenge: Spawning Objects within a Zone/Rect

Change the spawner to instead use the `Rect` component (that you should place on the `Spawner_Asteroid prefab). This should be a variable of your spawner

To have the asteroid spawn inside the rect, it is recommended you use the `GetMin` and `GetMax` nodes. These will output a Vector2. From here, you can obtain the X or Y value independently from these min and max vectors, which you can then use as inputs for a Random node, which would to pick a point between the min and max of the rect for each axis.

Note: Unfortunately you cannot visualise the rect by default, which can be annoying. You can circumvent this by using **Gizmos**.

Gizmo's are not something we will go in to depth this unit. However, simply copy the following if you wish to be able to see your rect component in your Scene view (Note that `spawnZone` is the variable for our Rect)

<img src="PracResources\images\bonus_RectVisual.png">

## Recommended challenge: Add a Cooldown to Firing

Quite straightforward, add a timer using the Spawner_Asteroid implementation to implement a cooldown for shooting from our ship. You will likely need similar variables (e.g. `shootDelay` and `shootCooldownRemaining`)

Again, timers are used everywhere in games

## Easy/Moderate challenge: Destroy Asteroids around the play space using coordinates (or a Rect)

It is quite annoying that the asteroids fly out into the middle of nowhere  if they get knocked around, and move away from the bottom of the screen/our destroyer. Expand the functionality of the bottom destroyer to cover a wider area (from all sides of the play field).

This does not mean simply making four destroyers. Simply doing this will not count

There are a few ways you could approach this, such as:

* You could simply use the existing trigger, but tweak some of the nodes (this is intentionally vague, as this is the easiest solution, but not the best)
* Using co-ordinates. It is recommended to use a Rect for this approach, and check whether a position exists within the Rect

Note: Unfortunately you cannot visualise the rect by default, which can be annoying. You can circumvent this by using **Gizmos**.

Gizmo's are not something we will go in to depth this unit. However, simply copy the following if you wish to be able to see your rect component in your Scene view (Note that `spawnZone` is the variable for our Rect)

<img src="PracResources\images\bonus_RectVisual.png">

## Moderate challenge: Asteroids Break Apart into Chunks when Destroyed

While shooting Asteroids is fun, so are particles. We won't be covering particles in this unit, but we can mimic the "juiciness" of particles by simply spawning more asteroids. Try and implement this logic:


|   |                                                                                                                                                                                                                                        Asteroid chunk logic                                                                                                                                                                                                                                        |   |
| --- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | --- |
|   | Upon shooting an asteroid it should break apart into smaller chunks. These asteroid chunks should be roughly the same speed of the original, but smaller than the original Asteroid. This could be done using**Scale** in the **Transform** component, or simply using another prefab. It would be cool if you could break the chunks into even smaller pieces, but this is not necessary. The Destroyer deletes the asteroid as normal, but should not cause the Asteroid to explode into chunks. |   |
|   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |   |

## Moderate challenge: Create a Shooting State for UFO's

Going back to last weeks enemy, add a new state that lets the enemy shoot back!

* This state should be possible to enter from only the `Chase` state.
* You should create a different bullet prefab (and maybe make it act differently to the player bullet, with different speed, size, colour, etc)
* The bullet that spawns should be able to kill the player, and it is up to you if it destroys asteroids or not.
* Make sure you consider the scenario of a player bullet and an enemy bullet colliding.

## Hard challenge: Make a completely new feature

By now you should have a good grasp on how to use Visual scripting. At this point you probably have enough knowledge to implement some new features in from scratch.

Have a think about what would suit a game like this, and have a go at implementing it.

You can implement anything you want, but here are some examples of ideas you could implement (some of these might require a lot of time though/would have to be done out of class):

* Power ups that change how shooting functions
* Make the enemy UFO shoot back (or the enemy ship, if you did that optional task)
* Make the game "scroll" in a direction, e.g. vertically
* Turn the game into a multiplayer experience, where players try to navigate asteroids and shoot each others in order to be the last person standing
* Turn the game into a "bullet hell" style game

## Alternative: Go back and complete past challenges

There are a few challenges from earlier weeks that are quite difficult, or you may not have done. You may go back and complete one of these instead if you wish.

# Completed Tasks:

To verify that you've completed all of the content in this prac, check you have done the following:

* Created an asteroid spawner at the top of screen
* Created an asteroid destroyer at bottom screen
* Added shooting for the player
* Ensured the bullets despawn after a period of time
* You have completed **TWO** challenges - these don't have to both be from this week

### Show your demonstrator

To show your understanding of this weeks content to your prac demonstrator, you may wish to show:

* How neat and tidy your State Graphs are, especially your complex ones
* How to make (and apply changes to) prefabs
* Your Asteroid spawner
* Your ship zooming around and shooting
