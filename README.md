# Unity Scripting Tutorial

 Welcome to Part 2 of GDG's Unity 2D-Tutorial! This little README is an overview of what is being taught here during the session.

# Creating a script
1. Right click in your project window.
2. `Create > C# Script`
3. A script should appear with its name already highlighted, rename it to what you want. For example, `MyScript`.

**NOTE**:
- This will create `MyScript.cs` and `MyScript.cs.meta`
- `MyScript.cs.meta` cannot be seen in Unity or your code editor. However, do not delete it! It is very important
- If you wish to rename your script, do it in the Unity editor by single-clicking on its name. It will update the name of the `.meta` file as well.
- Do **not** edit it through file explorer or your code editor

# Writing scripts
**From this point forward, we will be explaining the code in laymen terms to help new developers.**

### Intro
When you open the file (by double clicking the script) it should like this:

```cs
using  System.Collections;
using  System.Collections.Generic;
using  UnityEngine;

public class MyScript : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {

    }
    // Update is called once per frame
    void Update()
    {

    }
}
```
The important things to note are:
1. `: MonoBehaviour` part is important in order for this script to be attached to `GameObject`s in the Unity editor.
2. `Start()` is something that will run on the first frame of the game. Simply put, the code between the `{` and `}` will run only once - usually when the game starts.
3. The code in `Update()`will run once every frame. Imagine your game is running at 60 frames per second. This `Update` code will run 60 times in one second. An [ELI5 ](https://www.reddit.com/r/explainlikeimfive/comments/752b1f/eli5_what_exactly_are_frames_in_video_games_and/)  (explain like im 5 thread) about what frames are.
4. Don't delete the 3 statements starting with `using` unless you know what you are doing

To learn more: [https://docs.unity3d.com/Manual/ExecutionOrder.html](https://docs.unity3d.com/Manual/ExecutionOrder.html)

Once created, you can attach it to a `GameObject` in the `Scene` or a `Prefab` in the project window.

## Writing a movement script
 Before continuing, we would like you to do the following
1. Create a `Scripts` folder in the `Assets` folder
2. Create a script called `Movement`
3. Attach the `Movement` script to the `Player` prefab found in `Assets/Prefabs`

**NOTE**: notice how the `Player` prefab has a `Rigidbody2D` component. We will use this to move the player.

### Trying things out
Now open the `Movement` script and type the following in `Update` like so:
```cs
void Update()
{
    Rigidbody2D rigidbody = GetComponent<Rigidbody2D>();
    rigidbody.velocity = Vector2.right;
}
```
If you run the sample scene provided, our `Player` will be constantly moving to the right. A basic explanation would be we are causing the Player to move right every frame. Specifically, we are setting the velocity of our player to the right every frame.

**NOTE**: Try moving the copied line into the `Start` instead. You should see the `Player` move to the right before coming to a halt when he hits the ground. Can you explain why?

By adjusting the velocity of the `Rigidbody2d` we are able to move the player. Now we just have to make him move when we want to - when the human player presses the appropriate buttons.

### Handling Input
Change `Update` to this:
```cs
void Update()
{
    Rigidbody2D rigidbody = GetComponent<Rigidbody2D>();
    if (Input.GetKey(KeyCode.D))
    {
        rigidbody.velocity = Vector2.right;
    }
}
```
The code is explained like so: If the `D` button is pressed, set the velocity of the player to the right.
When you run the scene now, the `Player` will start moving only when you press `D`.

### Setting x velocity
Currently there are two problems with this implementation.
1. The player's Y velocity is reset when when he presses the `D` button.
2. If the player touches a surface, it will naturally slow down. However, try setting the `Gravity Scale` value to 0. The `Player` will float and not touch any surface. If we press `D` now, he will move indefinitely.

These behaviours are unwanted. To remedy this we should change the x velocity of his `Rigidbody2D` like so:

```cs
void Update()
{
    Rigidbody2D rigidbody = GetComponent<Rigidbody2D>();
    Vector2 velocity = rigidbody.velocity; // get the original velocity
    if (Input.GetKey(KeyCode.D))
    {
        velocity.x = 1;
    }
    else
    {
        velocity.x = 0;
    }
    rigidbody.velocity = velocity;
}
```

So now the code reads like this: Every frame, if `D` is pressed, the player velocity x will be one, the positive x direction, and thus to the right, otherwise, reset it to 0;

**NOTE**: For the advanced or more experienced developers. One would wonder why we aren't setting the velocity directly like `rigidbody.velocity.x = 1`. The reason is because `velocity` is a [property](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/properties) and we cant edit each component of the vector individually.

### Left and Right

Now as an exercise, implement the left direction. Specifically:
1. if `D` key is pressed, move the `Player` to the right
1. otherwise if `A` key is pressed move the `Player` to the left

**HINT**: For new programmers, to do a otherwise if, you can use `else if`. The structure should be like this:
```cs
if (something)
{
}
else if (something different)
{
}
else
{
}
```

### Jumping
If you made it this far, good job! For those who couldn't get it here's something you can replace your original code with:
```cs
void Update()
{
    Rigidbody2D rigidbody = GetComponent<Rigidbody2D>();
    Vector2 velocity = rigidbody.velocity;
    if (Input.GetKey(KeyCode.D))
    {
        velocity.x = 1;
    }
    else if (Input.GetKey(KeyCode.A))
    {
        velocity.x = -1;
    }
    else
    {
        velocity.x = 0;
    }
    rigidbody.velocity = velocity;
}
```
**NOTE** There are many other ways to do this (for example, using `Transform`), left as an exercise. Also, note that we have not implemented movement speed. If you want to know more, feel free to find us on our discord!

Now back to jumping. To jump we can set the y velocity to be a positive value when the `Space` key is pressed:

```cs
void Update()
{
    Rigidbody2D rigidbody = GetComponent<Rigidbody2D>();
    Vector2 velocity = rigidbody.velocity;
    if (Input.GetKey(KeyCode.Space))
        velocity.y = 10;
    if (Input.GetKey(KeyCode.D))
    // additional code left out for space
}
```

**NOTE**: Without putting an `else if` before `(Input.GetKey(KeyCode.D))`. It means that the `A` and `D` keys can be pressed together with the `Space` key.

## Public Fields

For our current implementation, we are manually setting a value for the y velocity (10) and x velocities (1 and -1). Based on your level design you may need to readjust this values so that he can reach various platforms and have faster movement. Readjusting the values of the `Player` can get very tedious for developers as it requires updating the code, compiling, then checking the game. What if we can manually edit this values in real time in the Unity editor?

We can do so by using `public float`s which will be exposed in the inspector of the Unity editor like so:

```cs
public float JumpSpeed;
void Update()
{
    Rigidbody2D rigidbody = GetComponent<Rigidbody2D>();
    Vector2 velocity = rigidbody.velocity;
    if (Input.GetKey(KeyCode.Space))
    {
        velocity.y = JumpSpeed;
    }
    // Left and Right Code omitted
}
```

Now in the `Movement` component of your `Player` you should see `Jump Speed` with an input box. You can adjust this value to whatever you want and it will update the values used for that particular `Player`! For example if I change it to 20, the `Player` in the the scene will now jump higher! To learn more click [here](https://docs.unity3d.com/Manual/VariablesAndTheInspector.html).

As a quick exercise, try to implement this for our velocities by creating a `public float RunSpeed`!

---

If you made it here, or are lost, here's some coe you can refer to!

```cs
public float JumpSpeed;
public float RunSpeed;
void Update()
{
    Rigidbody2D rigidbody = GetComponent<Rigidbody2D>();
    Vector2 velocity = rigidbody.velocity;
    if (Input.GetKey(KeyCode.Space))
    {
        velocity.y = JumpSpeed;
    }

    if (Input.GetKey(KeyCode.D))
    {
        velocity.x = RunSpeed;
    }
    else if (Input.GetKey(KeyCode.A))
    {
        velocity.x = -RunSpeed;
    }
    else
    {
        velocity.x = 0;
    }
    rigidbody.velocity = velocity;
}
```

### Grounded Check
There's a huge problem with jumping logic. If you play around for a bit, you will soon realize that if hold down `Space`, the `Player` can fly indefinitely. Unless you are making a super hero game, this clearly is unwanted. If we try to think about this logically, a simple condition we can check before jumping would be to check if the `Player` is grounded (ie two feet are on the ground). This makes sense, only if the `Player` is on the ground would he be able to propel his body upward (jump).

To do this check we will make use of `Collider`s to detect a `Player` is touching the ground. Just below your `Update` code, put:
```cs
void Update()
{
 // code removed for space
}

void OnCollisionEnter2D(Collision2D collision)
{

}
```
`OnCollisionEnter2D` will be executed if the `Player` 's `Collider2D` begins to touch another `Collider2D`. `Collision2D collision` will be information about what the `Player` is touching. We can use that to detect if the `Player` is grounded.

Update you code as such:

```cs
public float JumpSpeed;
public float RunSpeed;
private bool Grounded = false;
void Update()
{
    Rigidbody2D rigidbody = GetComponent<Rigidbody2D>();
    Vector2 velocity = rigidbody.velocity;
    if (Input.GetKey(KeyCode.Space) && Grounded)
    {
        velocity.y = JumpSpeed;
        Grounded = false;
    }
    // Left and Right Code omitted
}

void OnCollisionEnter2D(Collision2D collision)
{
    Grounded = true;
}
```
The code works this way: When the `Player` 's `Collider2D` begins to touch another `Collider2D`, it will assume that it touches the ground and thus set `Grounded` to `true`. The next time the player presses `Space`, he will also be grounded and have his y velocity updated.

Important points:
1. Yes the `private bool Grounded = false` is outside of all the function. However it should be within the curly braces of `public class Movement : MonoBehaviour`
2. `bool` is a boolean, a true or false value. It can't be anything else.
3. The `&&` operator is a logical `AND` meaning that `Grounded` must be `true` and the player must have pressed `Space
4. `Grounded = false` is done after space has been pressed

**NOTE** This implementation is far from perfect. Consider the `Player` hitting a ceiling. `OnCollisionEnter2D` will be executed and consequently set `Grounded = true` incorrectly. This is not simple because the ceiling we hit from the bottom, could be a platform on top - in some cases `Grounded = true` is correct. A possible solution is to create another `Collider2D` and place it at the feet of the `Player` and detect collisions only for the "feet collider" to check for grounded, similar to how we feel the ground with our feet.

## Flipping the sprite
**NOTE** You can think of a sprite as a 2D image. In this case, we are referring to the 2D image of the `Player`

We have a functional `Movement` script now. However, notice when we move left, our `Player` faces the wrong direction. Given that we have set up the `Movement` script in the way mentioned above, a simple way to remedy is to flip the `Sprite` direction when move left. If you notice the `SpriteRenderer` component in the Unity editor, it has a flip option for x and y. If we toggle it, it will cause the sprite to change direction. We can update this value in our `Movement` script:

```cs
void Update()
{
    // jump code
    if (Input.GetKey(KeyCode.D))
    {
        velocity.x = RunSpeed;
        GetComponent<SpriteRenderer>().flipX = false;
    }
    else if (Input.GetKey(KeyCode.A))
    {
        velocity.x = -RunSpeed;
        GetComponent<SpriteRenderer>().flipX = true;
    }
    else
    // remaining code
}
```
**NOTE**: In the previous frame, the `Sprite` could have been already facing left. When we move right, we must unflip the sprite direction by setting `flipX = false`. If the `Sprite` was originally facing right, no harm is done by resetting it to `false`.

## Collecting Coins

### Trigger collision
In our `Assets/Prefabs` folder,  we have a `Coin` prefab. Drag it in onto the scene. Its components contain a `CircleCollider2D` and if you notice carefully, its `Is Trigger` is ticked, meaning it is a trigger collider. This was explained in previous tutorials.

Instead of `OnCollisionEnter2D`, trigger colliders will execute a different method `OnTriggerEnter2D`. We can use this to detect the coin. Let's detect the coin collision and destroy the coin `GameObject`, we can do it in `Movement` for convenience:
```cs
void OnTriggerEnter2D(Collider2D collider)
{
    Destroy(collider.gameObject);
}
```
**NOTE**:
1. `OnTriggerEnter2D` takes in a `Collider2D ` instead of `Collision2D`
2. We can `Destroy` the coin's `GameObject` calling `Destroy(collider.gameObject);`
    - a very common mistake would be to destroy the `Collider2D `. This will only destroy the component but not the actual `GameObject` in the scene.
3. This will destroy any `GameObject` with a trigger collider. In our sample game this is fine. However, if there are other triggers, you may not want to destroy it. We can use [tags or layers](https://docs.unity3d.com/560/Documentation/Manual/class-TagManager.html) to control the collision behaviour.

### Increasing Score
Destroying coins seem pretty meaningless. We can add some value to collecting coins by reward the player with a score. Every time the `Player` collects a coin, we can add to a score like this:
```cs
public int Score = 0;
void OnTriggerEnter2D(Collider2D collider)
{
    Score = Score + 1;
    Destroy(collider.gameObject);
}
```
Thanks to [serialized public variables](https://docs.unity3d.com/Manual/VariablesAndTheInspector.html) we are able to see the score appear on the component. We can also log our this event to our console like so:

```cs
public int Score = 0;
void OnTriggerEnter2D(Collider2D collider)
{
    Score = Score + 1;
    Debug.Log("My Score: " + Score);
    Destroy(collider.gameObject);
}
```
If you open the console window under `Window > General > Console` (window is found on top), you can see that when you hit a coin, you will have a statement logged `My Score: 1`.

## Displaying Score

A very important thing to note: the logging by `Debug.Log` will not appear in the final game, it is meant for debugging your code! It is best to present this score on the screen by adding Text on the Screen. In Unity, we can use `Canvas`es and `Text`s to display text on the screen.

In your scene hierarchy, right click and create a text: `UI > Text`. This will create a `Canvas` object and a `Text` object that is a child of it. Now do the following:

1. Adjust the `Text` object such that it is within the white border (which is your canvas). You should be able to see the text when you switch to the game window. If you move it to the top left hand corner of the white rectangle, it will appear on the top left of your game window.
1. If you select the `Text` game object, it will have a `Text` component that has a `Text` input field (too many texts...). Currently in the input field, it should say `New Text`. You can change it to whatever you want.
1. You can also change the font size of the text and the Color using the same component.


Now lets hook up this `Text` to our game logic.
At the very top of your add the `using UnityEngine.UI;` statement and create a `public Text ScoreText` field like so:

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI; // IMPORTANT!

public class Movement : MonoBehaviour
{
    public float JumpSpeed = 10;
    public float RunSpeed = 5;
    public int Score = 0;
    public Text ScoreText; // Reference to the Text!
    void Update()
    {
        //...
    }
    //...
}
```
This `public Text ScoreText` will appear in your `Movement` component of your `Player`. You can drag the `Text` game object directly into your slot that says `Score Text`.

Lastly, we should update the `Text` whenever we get a coin and update our score! Simply replace the `Debug.Log` statement with:

```cs
void OnTriggerEnter2D(Collider2D collider)
{
    Score = Score + 1;
    Destroy(collider.gameObject);
    ScoreText.text = "Score: " + Score;
}
```
Now when you collider with the a coin, it will be destroyed, and your text will be updated to reflect your score.

# End

If you made it this far, thanks for reading through the tutorial. Of course more can be done to improve the game. For example we can kill the player when he hits the spikes. We can also separate our code to different scripts for modularity. If you have any questions, don't hesitate to look for us on discord. Below will be references to the final code.

## Reference for Final Code
```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class Movement : MonoBehaviour
{
    public float JumpSpeed = 10;
    public float RunSpeed = 5;
    public int Score = 0;
    public Text ScoreText;
    void Update()
    {
        Rigidbody2D rigidbody = GetComponent<Rigidbody2D>();
        SpriteRenderer spriteRenderer = GetComponent<SpriteRenderer>();
        Vector2 velocity = rigidbody.velocity;

        if (Input.GetKey(KeyCode.Space) && Grounded)
        {
            velocity.y = JumpSpeed;
            Grounded = false;
        }

        if (Input.GetKey(KeyCode.D))
        {
            velocity.x = 1;
            spriteRenderer.flipX = false;
        }
        else if (Input.GetKey(KeyCode.A))
        {
            velocity.x = -1;
            spriteRenderer.flipX = true;
        }
        else
        {
            velocity.x = 0;
        }
        rigidbody.velocity = velocity;
    }

    void OnCollisionEnter2D(Collision2D collision)
    {
        Grounded = true;
    }

    void OnTriggerEnter2D(Collider2D collider)
    {
        Score = Score + 1;
        Destroy(collider.gameObject);
        ScoreText.text = "Score: " + Score;
    }
}
```

## Credits
This tutorial was done by the National University of Singapore Games Development Group
