# CoDrone EDU: Command Menu

A list of things you can tell the drone to do, short enough to skim. Pick something, try it, see what happens.

This is the companion to the **Python Command Reference**. The reference explains how each command works and what goes wrong. This one is just the menu — when you want to know *why*, go back to the reference.

Every snippet here assumes you already have the skeleton around it:

```python
from codrone_edu.drone import *

drone = Drone()
drone.pair()

# ---- snippets from this menu go here ----

drone.close()
```

## Table of Contents

1. [Rules That Bite](#1-rules-that-bite)
2. [Start and Stop](#2-start-and-stop)
3. [Moving by Distance](#3-moving-by-distance)
4. [Moving by Power and Time](#4-moving-by-power-and-time)
5. [Turning](#5-turning)
6. [Ready-Made Shapes](#6-ready-made-shapes)
7. [Lights](#7-lights)
8. [Sounds](#8-sounds)
9. [Reading Sensors](#9-reading-sensors)
10. [Reacting to Sensors](#10-reacting-to-sensors)
11. [Trim and Housekeeping](#11-trim-and-housekeeping)
12. [Experiments Worth Running](#12-experiments-worth-running)

---

## 1.  Importante Rules

Read these five before you start experimenting. They cause most of the "my code does nothing" moments.

1. Put `hover(1)` between `takeoff()` and whatever comes next, or the next command gets skipped.
2. `set_pitch()`, `set_roll()`, `set_throttle()`, and `set_yaw()` stay set until you change them. Call `reset_move_values()` when you are done.
3. Positive is **left** for every turn command.
4. `flip()` does nothing under 50% battery, and it needs `time.sleep(4)` after it.
5. Distance commands need a patterned, well-lit floor.

---

## 2. Start and Stop

```python
drone.takeoff()             # lift to about 80 cm
drone.hover(3)              # hold position for 3 seconds
drone.hover()               # hold until the next command
drone.land()                # settle down gently
drone.emergency_stop()      # cut the motors, drone drops
```

**Try:** take off, hover 1, land. Then take off, hover 5, land. Watch how steady it is at the end of a long hover compared to the start.

---

## 3. Moving by Distance

You say how far. The drone figures out the rest.

```python
drone.move_forward(50, "cm", 1)
drone.move_backward(50, "cm", 1)
drone.move_left(30, "cm", 1)
drone.move_right(30, "cm", 1)

drone.move_forward(2, "ft", 0.5)      # units: "cm", "m", "in", "ft"
drone.move_forward(50, "cm", 2)       # speed maxes at 2.0
```

All three axes at once, **meters only, no unit string**:

```python
drone.move_distance(0.5, 0.5, 0.25, 1)    # forward 0.5, left 0.5, up 0.25, at 1 m/s
drone.move_distance(-0.75, 0, 0, 0.75)    # straight back 0.75 m
```

Positive x is forward, positive y is left, positive z is up.

**Try:** same distance at speed 0.5, then 1, then 2. Measure where it lands each time. Faster is not more accurate.

---

## 4. Moving by Power and Time

You say how hard to push and for how long. Nothing moves until `move()`.

```python
drone.set_pitch(50)          # forward at 50% power
drone.move(1)                # do it for 1 second

drone.set_roll(-30)          # left at 30%
drone.move(2)

drone.set_throttle(40)       # up
drone.move(1)

drone.set_yaw(50)            # spin left
drone.move(1)

drone.reset_move_values()    # all four back to 0
drone.move()                 # no number = keep going until the next command
```

Check what is currently set:

```python
values = drone.get_move_values()      # [roll, pitch, yaw, throttle]
print(values)
```

**Try:** set pitch to 50, move 1 second, then set roll to 50 and move again — without resetting in between. Watch it fly diagonally. Now add `reset_move_values()` and run it again.

---

## 5. Turning

```python
drone.turn_degree(90)        # left 90, measured from the takeoff heading
drone.turn_degree(-90)       # right 90

drone.turn_left()            # left 90 from where it is facing now
drone.turn_right(45)         # right 45
drone.turn_left(120)

drone.turn(50, 2)            # spin left at 50% power for 2 seconds
drone.turn(-20, 5)           # spin right at 20% power for 5 seconds
```

**Try:** `turn_degree(90)` twice in a row, then `turn_left()` twice in a row. They do different things. Figure out why before you read section 6 of the reference.

---

## 6. Ready-Made Shapes

```python
drone.square()
drone.triangle()
drone.circle()
drone.spiral()
drone.sway()
drone.flip("back")           # "back", "front", "left", "right"
```

Each shape takes optional speed, seconds per side, and direction:

```python
drone.square(50, 3, 1)       # bigger and slower than the default
drone.square(30, 1, -1)      # other direction
```

Flips need battery over 50% and recovery time:

```python
import time

drone.hover(3)
drone.flip("back")
time.sleep(4)
```

**Try:** run `drone.square()` and watch it. Then write your own square with `move_forward()` and `turn_left()` in a loop and compare. Which one looks better, and which one can you change?

---

## 7. Lights

```python
drone.set_drone_LED(255, 0, 0, 100)         # red, green, blue, brightness
drone.set_drone_LED(0, 255, 0, 50)          # green, half brightness
drone.drone_LED_off()

drone.set_controller_LED(0, 0, 255, 100)
drone.controller_LED_off()
```

Colors are 0–255 each. Brightness is 0–100.

**Try:** flash green when a sensor check passes and red when it fails. It is faster than reading the Console while you are watching the drone.

---

## 8. Sounds

```python
drone.drone_buzzer(440, 500)         # 440 Hz for 500 milliseconds
drone.controller_buzzer(880, 200)

drone.start_drone_buzzer(500)        # tone keeps playing
drone.hover(2)                       # while other things happen
drone.stop_drone_buzzer()
```

Duration is **milliseconds**. 1000 is one second.

**Try:** play a short tune before takeoff. Middle C is about 262 Hz, and each octave doubles the number.

---

## 9. Reading Sensors

Getters hand a value back. Print it, store it, or test it — otherwise it is gone.

```python
print(drone.get_battery())               # percent

print(drone.get_height())                # cm to the floor, up to 150
print(drone.get_height("in"))            # "m", "cm", "mm", "in"
print(drone.get_bottom_range())

print(drone.get_front_range())           # cm straight ahead, up to 150
print(drone.detect_wall(50))             # True or False

colors = drone.get_colors()              # [front, back]
print(colors[0])

print(drone.get_angle_x())               # roll
print(drone.get_angle_y())               # pitch
print(drone.get_angle_z())               # yaw, the direction it faces

print(drone.get_pos_x())                 # cm from the takeoff spot
print(drone.get_pos_y())
print(drone.get_pos_z())

print(drone.get_sensor_data())           # all 31 values at once
print(drone.get_error_data())
print(drone.get_flight_state())
```

Watch for junk values: **999 or 999.9** means "nothing in range," and **-10, -100, or 0** means the sensor errored. Neither is a real measurement.

Angles and positions **reset to zero at takeoff** — everything is measured from where it started.

**Try:** run a loop that prints `get_front_range()` while you walk a book slowly toward the drone on the ground. Find the exact distance where it switches to 999.

---

## 10. Reacting to Sensors

Read, test, decide. That is the whole pattern.

```python
if drone.get_front_range() < 60:
    drone.move_backward(30, "cm", 1)
else:
    drone.move_forward(30, "cm", 1)
```

```python
drone.set_pitch(30)
while drone.get_front_range() > 50:
    drone.move()
drone.set_pitch(0)
drone.hover(1)
```

Always give a `while` loop a way out, or a 999 reading will keep it running until the battery dies:

```python
distance = drone.get_front_range()
while distance > 50 and distance != 999:
    drone.move()
    distance = drone.get_front_range()
```

Built-in versions of the same idea:

```python
drone.avoid_wall(10, 50)        # fly forward up to 10 s, stop 50 cm short
drone.keep_distance(10, 60)     # fly forward, then hold 60 cm
```

**Try:** write your own `avoid_wall` with a while loop, then run the built-in one. Time both.

---

## 11. Trim and Housekeeping

```python
print(drone.get_trim())          # [roll trim, pitch trim]
drone.set_trim(-5, 0)            # drifting right? trim left
drone.reset_trim()

drone.reset_gyro()               # zero the angles
```

Trim is stored **on the drone** and survives a power cycle. If a drone flies strangely from the first second, check trim before you rewrite anything.

---

## 12. Experiments Worth Running

Pick one, write it, fly it, write down what happened.

1. **Height check.** Take off, print `get_height()` five times over ten seconds. Does it hold still?
2. **Battery drain.** Print the battery at the start and end of a two-minute flight. How many minutes do you actually get?
3. **Speed and accuracy.** Fly 100 cm at speed 0.5, 1.0, and 2.0. Measure all three.
4. **Floor test.** Fly the same 50 cm move over carpet, then over a smooth floor. Measure both.
5. **Drift test.** Take off, hover 20 seconds, land. Measure how far from the start it ended up. Trim it and repeat.
6. **Color landing.** Fly forward until `get_colors()` sees red, then land.
7. **Light meter.** Turn the drone LED a different color for each color card it reads.
8. **Wall follower.** Use `get_front_range()` in a loop to hold a steady distance from a moving object.
9. **Turn accuracy.** `turn_degree(90)` four times, then measure how far off the original heading it is.
10. **Sound and motion.** Play a rising tone while the drone climbs and a falling tone while it descends.
