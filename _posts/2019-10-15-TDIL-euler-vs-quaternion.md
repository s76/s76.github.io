---
layout: post
title: TDIL Euler angles and quaternions
snippet: a seemingly simple problem turns out to be not trivial at all with the math behind it
tags: [TDIL, math]
---

Starting with a script that has to rotate the camera to the direction where the cursor is pointing to
```csharp
float yaw = mouseSensitivity.x * Input.GetAxis("Mouse X");
float pitch = mouseSensitivity.y * -Input.GetAxis("Mouse Y");

Vector3 camRotDir = new Vector3(pitch, yaw, 0);

// this works (but dont do this)
Vector3 camRot = mPlayerCamera.transform.rotation.eulerAngles + camRotDir;
mPlayerCamera.transform.rotation = Quaternion.Euler(camRot);

// this doesn't work
var rotation = Quaternion.Euler(camRotDir);
mPlayerCamera.transform.rotation =  mPlayerCamera.transform.rotation * rotation;

// this is how it should be done (for the purpose of this script)
var yawRot = Quaternion.Euler(0, yaw, 0);
var pitchRot = Quaternion.Euler(pitch, 0, 0);
mPlayerCamera.transform.rotation = yawRot * mPlayerCamera.transform.rotation;
mPlayerCamera.transform.rotation = mPlayerCamera.transform.rotation * pitchRot;
```
I had the yaw rotation, pitch rotation and the first thing that came to mind was to combine rotations by multiplying the quaternions that represent them.
```
q_final = q_1 * q_2
``` 
but quickly I realized that the camera didnt behave as it should (as in the commented out code) and after quick consulting with my friend Marcin O. I still couldn't find the cause so I decided to ask it on stackoverflow - the holy land of programmers :D <br>
[Link to my post](https://gamedev.stackexchange.com/questions/176339/combining-rotation-using-quaternion-quaternion-doesnt-work-as-expected)<br>
It later led me to another excellent [post](https://gamedev.stackexchange.com/questions/136174/im-rotating-an-object-on-two-axes-so-why-does-it-keep-twisting-around-the-thir) - a very detailed explaination of the issue that I was facing and I actually felt like I was enlightened by the knowlegde it contains.<br>
I found everything I needed there - the cause, the math, the solution and beyond everything the awareness of my misconception about how rotation works and that **euler angles are not meant to be used to manipulate rotation** - if you are using them to rotate your object, you are probably doing it wrong. Well, maybe not completly wrong, I exaggerated xd but from my understanding, euler angles are not suitable for complex rotation combination which means if you just want to apply one rotation at a time its totally fine to do that without any unpleasant surprises **but** if you are about to combine a series of rotations then-use-quaternions, period.<br>

