### SL-1 Intro to VR

- VR definition
	- inducing *targeted behavior* in an *organism* by using *artificial sensory stimulation* while the organism has little or no *awareness* of the interference
- real world vs. virtual world
- interaction
	- *open-loop*: sensory stimulation does NOT depend on actions taken by the organism
	- *closed-loop*: does depend
- virutal world
	- *synthetic*: invent completely from geometric primitives and simulated physics, synthetic representation of ourselves are *avatars*
	- *captured*: invent using modern imaging techniques
- modern VR
	- video games, immersive cinema, telepresence, virtual societies, empathy, etc.
- history
	- sensorama -> VPL research -> IEEE VR, etc.

### SL-2 VR Systems

- hardware: sensors (input) -> computers -> displays (output)
	- sensor vs. sense organ (of organism)
	- configuration space (of sense organ) -> degrees of freedom (DOFs)
	- user-fixed visual display
	- VR sickness -> if not transform in a timely and accurate manner
	- *display*
		- the hardware that VR system uses to hijack the sense organ by replacing natural stimulation with artifical ones
		- types: visual, sound and haptic displays
	- *sensors*
		- set up positions and orentation of the sense organ
		- inertial measurement unit (IMU), (depth) cameras, keyboards, optical mouse, etc.
	- computers
		- executes *virtual world generator* (VWG)
		- mobile VR, all-in-one headsets, etc.
- software
	- VR engine (game engines)
	- virtual world: synthetic models vs. recorded physical world
	- matched motion, matched zone
	- user *locomotion*: moving oneself in the virtual world, while this motion is not mathced in the real world
	- physics: collision detection algorithm, how stimuli are created and propgate through the virtual world
	- networked experiences
- human physiology and perception
	- perceptual psychology: understand how the brain converts sensory stimulation into perceived phenomena
	- optical illusions: unusual stimulus that highlights limitations of our vision systems
	- proprioception: ability to sense the relative positions of parts of our bodies, etc.
	- *vection*: illusion of self motion, caused by locomotion operation
	- adaptation: the perceived effect of stimuli changes over time

### SL-3.1 Geometric Models

- *primitives*: use finite points to define infinite points in 3D
- right-handed (z-axis on the left) vs. left-handed (z-axis on the right, Unity)
- vertices: use a planar triangle surface to represent all points "inside" and boundary, ((x1, y1, z1), (x2, y2, z2), (x3, y3, z3))
- *mesh*: for modeling complicated objects, from numerous triangles
- stationary vs. movable models
	- viewing transformations: determine where the points in virtual world should appear on the display
	- rendering: determine how each part of the model should appear aftering taking into account lighting sources and sufrace properties

### SL-3.2 Changing Position and Orientation

- some math
	- dot product of a,b given their angle t: a * b = |a||b|cos(t)
	- collinear: a * b = |a||b| = ab
	- opposite collinear: a * b = -ab
	- perpendicular: a * b = 0
	- same direction: a * b > 0
	- opposite directon: a * b < 0
- transform rigid bodies
	- translation: 2 DOF in 2D, 3 DOF in 3D
	- rotation: 1 DOF in 2D, 3 DOF in 3D
	- both: 3 DOF in 2D, 6 DOF in 3D
- translation
	- translation matrix T (3x1)
	- formula (trivial)
	- relativity (move center, or move points)
- 2D rotation
	- rotation matrix M (2x2)
	- need to ensure model not distorted after rotation
	- M satisfy
		- no stretching of axes: M has unit length (M column lies on unit circle)
		- no shearing: coordinate axes remain perpendicular (M column dot product = 0)
		- no mirror images: M determinant positive (with above rules together, need determinant = 1 or -1)
	- M row_1 = [cos(t) -sin(t)], row_2 = [sin(t) cos(t)]
- 3D rotation
	- Yaw, Pitch, Row = y, x, z
	- formula (omit)
	- rotation matrix R (3x3) = R_yaw * R_pitch * R_row
	- not commutative, order matters
- both
	- transformed point (3x1) = R * point + T
	- homogeneous transformation matrix T_rb
		- first 3x3 = R
		- right 3x1 = T
		- last 1x4 = [0 0 0 1]
		- T_rb is a rigid body transform -> does not distort object
	- inverting transform formula T_rbi = A (4x4) * B (4x4)
		- A first 3x3 = R
		- A right 3x1 = [0 0 0]
		- A last 1x4 = [0 0 0 1]
		- B first 3x3 = Identity Matrix
		- B right 3x1 = -T
		- B last 1x4 = [0 0 0 1]

### SL-3.3 Axis-Angle Representations of Rotation

- Euler's rotation theorem: every 3D rotation = unit direction vector v = (v1,v2,v3) + rotation angle t
- *quaternion* q
	- we only use unit q = (a,b,c,d) where a^2 + b^2 + c^2 + d^2 = 1
	- let (v, t) be the axis-angle representation of 3D rotation
	- then q = (cos(t/2), v1 * sin(t/2), v2 * sin(t/2), v3 * sin(t/2))
	- recover v = (b,c,d)/sqrt(1-a^2), t = 2 * cos^(-1)(a)
- quaternion properties
	- [description, axis-angle (v, t), quaternion (a,b,c,d)]
	- identity rotation, (undefined, 0), (1,0,0,0)
	- pitch by pi, ((1,0,0), pi), (0,1,0,0)
	- yaw by pi, ((0,0,1), pi), (0,0,1,0)
	- roll by pi, ((0,0,1), pi), (0,0,0,1)
	- etc.
- quaternion inverse: (a,b,c,d) -> (a,-b,-c,-d)
- quaternion identity: (a,b,c,d) == (-a,-b,-c,-d)
- quaternion multiplication: q3 = q1 * q2, formula (omit)
- quaternion rotation
	- rotated p = q * p * inv(q)
- 3D point to 4D unit quaternion
	- (x,y,z) -> (sqrt(1/(x^2 + y^2 + z^2)), x, y, z)
	- tranform back (trivial)

### SL-3.4 Viewing Transformations

- eye rotation -> virtual world
	- suppose eye at position e (3x1) rotates R (3x3)
	- then virtual world center coordinate should be inversely transformed by T_eye
	- T_eye = A (4x4) * B (4x4)
	 	- A first 3x3 = R^T
	 	- A right 3x1 = [0 0 0]
	 	- A last 1x4 = [0 0 0 1]
	 	- B first 3x3 = identity matrix
	 	- B right 3x1 = -e
	 	- B last 1x4 = [0 0 0 1]
- *look-at*
	- position of eye: e (3x1)
	- central looking direction of eye: c (3x1) = normalize(p - e)
	- up direction: u (3x1)
	- we want to construct T_eye: e is known, R is unknown
		- R right column z = -c, because we let eye to always watch the opposite z direction
		- R left column x = u * z, becuase u is up -> x is the left over direction of u * z
		- R center column y = z * x, now we can infer y from x and z
- projection 3D into 2D
	- *orthographic projection*: all (x,y,z) -> (x,y,0)
	- *perspective projection*: project all points into a planar "movie screen"
		- consider a line through the origin: i.e. the set of all points with (lx, ly, lz) where l can be any real number
		- put "movie screen" at z = -1
		- then (x,y,z) -> (-x/z,-y/z)
		- more generally, for any location d along the z axis
		- (x,y,z) -> (dx/z,dy/z)
