Back to [Samples](../../samples.md)

# Platformer Sample - Camera
The `OrbitCameraSystem` handles all camera logic. It gets its inputs from the `PlatformerPlayerInputs` component, and rotates/moves the camera accordingly.

The actual rendered camera is a GameObject in the scene (named "GameObjectCamera"), but it constantly copies the transform of the entity marked with the [MainEntityCamera] component. The GameObject camera registers itself as the rendering camera with the `MainGameObjectCamera` monobehaviour, and the `MainCameraSystem` handles setting its transform accordingly. 