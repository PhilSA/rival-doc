
Back to [Tutorial](../tutorial.md)

# Tutorial - Standard Character Overview

We will now take a moment to go over every part of the standard third-person character in order to gain a better understanding of it before we continue. Most of these explanations also apply to the first-person character

We have 3 main entities: 
- Player (`ThirdPersonPlayer`): Represents the human gathering input and controlling the Character & Camera
- Character (`ThirdPersonCharacter`): Represents the character that moves around in the world
- Camera (`OrbitCamera`): represents the camera that orbits around the character

The idea here is that the Player gathers input, transfers that input to components on the Character & Camera, and finally the Character & Camera move with that information. Let's take a closer look at how this happens in code:

### Player
1. `ThirdPersonPlayerSystem` gathers input from Unity's input system, and then iterates on all entities with a `ThirdPersonPlayer` component. 
1. For each one of those player entities, if it has a valid `player.ControlledCharacter`, we get the `ThirdPersonCharacterInputs` component on it and write inputs into that component. Notice that we don't directly write raw input in there; instead, we write inputs in a way that is easy for the character to understand. We convert our raw move input to a camera-relative worldspace vector, etc....
1. We do the same for the `player.ControlledCamera`; we write into the `OrbitCameraInputs` in order to tell the camera what to do

### Character
1. `ThirdPersonCharacterMovementSystem` updates at a fixed timestep, and schedules a `ThirdPersonCharacterJob` that handles all character movement & collision.
1. in the `ThirdPersonCharacterJob`, we build a `ThirdPersonCharacterProcessor` struct with all the data it needs, and call `OnUpdate()` on it. The character processor is essentially a single place where you implement the character movement logic and have access to all the data you need.
1. in the `ThirdPersonCharacterProcessor.OnUpdate()`, we have all the various steps required for character control. The part that you are most likely to want to modify & customize is `HandleCharacterControl()`. This is where you tell the character which velocity it should have right now.
1. the default code in `ThirdPersonCharacterProcessor.HandleCharacterControl()` reads from `ThirdPersonCharacterInputs` (the component our Player system was writing its inputs to), and controls the velocity accordingly

### Camera
1. `OrbitCameraSystem` reads from `OrbitCameraInputs` (the component our Player system was writing its inputs to), and moves the camera accordingly while always staying anchored to its `orbitCamera.FollowedCharacterEntity`

#

This separation between Player, Character, and Camera, as well as the separation between "raw" player input and "character/camera" input, is all done for several reasons:
1. The same Character prefab should be controllable by either a human player, or an AI controller. This is why "Player" is a separate entity from "Character", and why we have "CharacterInputs" on the character instead of the raw input (an AI controller wouldn't use raw input to control the character. Instead, it would directly calculate the desired movement, and feed that as input for the character). So our CharacterInputs essentially act as a common interface for controlling the character, which can be used both by human players or AI controllers
1. It wouldn't make sense for an AI character to have any knowledge of a "Camera", but a human-controlled character must often move relatively to the camera orientation. For this reason, calculating a "camera-relative move input" is the repsonsibility of the human Player; not of the Character. The human Player knows about the camera, and feeds camera-relative input to its controlled character. That way, the character can move relatively to the camera, without ever having to keep any kind of reference to a camera.
1. There are often cases where we need to destroy our Character entity, but we want the concepts of our "Player" and our "Camera" to survive. For example, in an online shooter game, you may have your character die, but your Player and your Camera persist while awaiting the respawning of your Character. Alternatively, we might also want to switch our controlled Character at runtime, but still preserve some data belonging to our "Player" in general (score, name, money, etc...) as well as the Player's input-processing logic. In these cases, it's very useful to make sure the Player, Character and Camera are separate entities. This way we can destroy the Character without destroying the Player or the Camera


For more details on the standard character & camera setup, visit the following pages:
* [Third Person Character](../StandardCharacters/stdcharacters-thirdperson.md) 
* [First Person Character](../StandardCharacters/stdcharacters-firstperson.md) 
* [DOTS Camera Setup](../StandardCharacters/stdcharacters-maincamera.md)