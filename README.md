<div align="center">
	<br/>
	<br/>
	<img src="addons/tattomoosa.vision_cone_3d/icons/VisionCone3D.svg" width="100"/>
	<br/>
	<h1>
		VisionCone3D
		<br/>
		<sub>
		<sub>
		<sub>
		Simple but configurable 3D vision cone node for <a href="https://godotengine.org/">Godot</a>
		</sub>
		</sub>
		</sub>
		<br/>
		<br/>
		<br/>
	</h1>
	<br/>
	<br/>
	<img src="./readme_images/demo.png" height="140">
	<img src="./readme_images/stress_test.png" height="140">
	<img src="./readme_images/editor_view.png" height="140">
	<br/>
	<br/>
</div>

Adds VisionCone3D, which tracks whether or not objects within its cone shape can be "seen".
This can be used to let objects in your game "see" multiple objects efficiently.
Default configuration should work for most use-cases out of the box.

## Features

* Edit range/angle of cone via 3D viewport editor gizmo
* Debug visualization to easily diagnose any issues
* Works with complex objects that have many collision shapes
* Configurable vision probe settings allow tuning effectiveness and performance to your use-case
* Ignore some physics bodies (eg the parent body)
* Separate masks for bodies that can be seen and bodies that can only occlude other objects

## Installation

Install via the AssetLib tab within Godot by searching for VisionCone3D

## Usage

Add the VisionCone3D node to your scene. Turn on debug draw to see it working. Then you can...

### Connect to the body visible signals

These signals fire when a body is newly visible or newly hidden.

```gdscript
extends Node3D

@export var vision_cone : VisionCone3D

func _ready():
	vision_cone.body_sighted.connect(_on_body_sighted)
	vision_cone.body_hidden.connect(_on_body_hidden)

func _on_body_sighted(body: Node3D):
	print("body sighted: ", body.name)

func _on_body_hidden(body: Node3D):
	print("body hidden: ", body.name)
```

### Poll the currently visible bodies

Get a list of the bodies which are currently visible

```
extends Node3D
@export var vision_cone : VisionCone3D

func _physics_process():
	print("bodies visible: ", vision_cone.get_visible_bodies())
```

## Performance Tuning

### Vision Test Mode

#### Center

Samples only the center point (position) of the CollisionShape. Most efficient, least effective

```js
vision_cone.vision_test_mode = VisionCone3D.VisionTestMode.SAMPLE_CENTER
```

#### Sample Random Vertices

Uses CollisionShape's `get_debug_mesh` to get a mesh representation of the CollisionShape,
then samples random vertex points from that mesh.
Effectiveness determined by the 

```python
vision_cone.vision_test_mode = VisionCone3D.VisionTestMode.SAMPLE_RANDOM_VERTICES
vision_cone.vision_test_max_bodies = 50 # Bodies probed, per-frame
vision_cone.vision_test_shape_max_probe_count = 5 # Probes per hidden shape
```

### Collision Masks

VisionCone3D has 2 collision masks, one used for bodies that can be seen by the cone and one for an environment,
which can occlude seen bodies but is not itself probed for visibility.

For example, add the level collision layer to `collision_environment_mask` and the player/enemy/object collision layer to the `collision_mask`.
The player/enemy/object can then hide behind the level, but no processing/probing will occur on the level collision geometry itself.

## The Future

### 2D Support?

I am open to adding a 2D version of this addon if there is sufficient interest.

See if [VisionCone2D](https://github.com/d-bucur/godot-vision-cone) meets your needs in the meantime. No relation.
