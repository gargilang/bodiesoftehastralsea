# Camera, Mobile Controls, and Accessibility

## Camera and Player Perspective

Avoid first person because open 3D movement can cause motion sickness. Boat travel uses a stable, elevated third-person camera behind and above the vessel, inspired by the readability of *Dredge* and *Spilled*. It keeps the boat, water, horizon, and environmental vectors visible without excessive roll or uncontrolled motion.

Land exploration uses an isometric or three-quarter orthographic-style camera with four fixed orientations snapping in 90-degree steps, preserving touchscreen legibility.

At sea, the boat becomes the embodied character. Immersion comes from readable components; physical interaction framing; visible wind, waves, wake, current, heel, spray, and stress; stable camera motion; a preserved horizon; restrained and reducible camera shake; and danger conveyed through scale and sound rather than forced first-person motion.

The target is cinematic but readable: close enough to feel responsible for the vessel and distant enough to understand forces.

## Gesture-Based Mobile Controls

The Android design uses touchscreen gestures and optional gyroscope controls to interact with physical boat parts rather than abstract buttons. Interactions may include:

* rotating a temporary wheel overlay through multiple turns while the rudder remains limited to about 35 degrees per side
* pulling a rope to trim the sail
* swiping a mast rope to raise or lower sail
* tapping the compass
* drawing, pinching, or rotating charts
* pulling the throttle lever
* sliding a reefing control
* aiming a spyglass or inspecting the horizon with the gyroscope
* rotating the phone to inspect instruments
* dragging cargo to redistribute weight

Gestures are contextual and grounded in visible ropes, levers, and instruments. The wheel overlay is a touchscreen-precision exception: it aligns with the helm, fades on release, and does not obscure sea feedback. Haptic steering can signal resistance and assistance, but every cue also needs visual or audio expression.

## Gyroscope and Accessibility

Gyroscope actions may include raising the phone as a spyglass, scanning the horizon, inspecting stars, aligning instruments, or looking around the boat.

They must never be mandatory. Players may be lying down, in public, traveling, using weak sensors, unable to move broadly, or sensitive to motion. Every gyroscope action needs a touch alternative—for example, dragging to aim the spyglass.

Immersion must not come at accessibility’s expense.
