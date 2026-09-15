# CoDrone EDU: Python Command Reference

The commands you will use all year, with what each one does and what goes wrong. Keep this open while you code.

Written for the **Python for Robolink** browser editor and the **codrone-edu** library, version 2.8.

## Table of Contents

1. [Before You Fly](#1-before-you-fly)
2. [Connecting to the Drone](#2-connecting-to-the-drone)
   - [2.1 The program skeleton](#21-the-program-skeleton)
   - [2.2 pair and close](#22-pair-and-close)
3. [Taking Off and Landing](#3-taking-off-and-landing)
4. [Moving: Two Different Ways](#4-moving-two-different-ways)
   - [4.1 Distance commands](#41-distance-commands)
   - [4.2 Power and time commands](#42-power-and-time-commands)
   - [4.3 Which one to use](#43-which-one-to-use)
5. [Turning](#5-turning)
6. [Ready-Made Shapes](#6-ready-made-shapes)
7. [Lights and Sound](#7-lights-and-sound)
8. [Sensors](#8-sensors)
   - [8.1 Battery](#81-battery)
   - [8.2 Height and the bottom range sensor](#82-height-and-the-bottom-range-sensor)
   - [8.3 The front range sensor](#83-the-front-range-sensor)
   - [8.4 Color](#84-color)
   - [8.5 Angles and position](#85-angles-and-position)
9. [Using Sensors in Loops and Conditions](#9-using-sensors-in-loops-and-conditions)
10. [Fixing Drift with Trim](#10-fixing-drift-with-trim)
11. [When Something Goes Wrong](#11-when-something-goes-wrong)
12. [Quick Reference Table](#12-quick-reference-table)

---

## 1. Before You Fly

Every flight, every time:

- **Battery.** Check the percentage in code before you take off. Under 50% the drone refuses to flip and gets unreliable. Under 20%, swap it.
- **Propellers.** Look at all four. A chipped or loose prop makes the drone pull to one side, and you will waste the period blaming your code.
- **Your square.** The drone stays inside your taped area. If it leaves, land it.
- **Hands off.** Never grab a flying drone. Use `emergency_stop()` or the controller.
- **Eyes on the drone, not the screen.** Run the program, then watch the drone.

The drone takes off to about 80 cm and you cannot change that. Plan your flight path knowing it starts at roughly waist height.

---

## 2. Connecting to the Drone

### 2.1 The program skeleton

Every program you write this unit has the same four bookends. Write these first, then put your flight code in the middle.

```python
from codrone_edu.drone import *   # load the drone commands

drone = Drone()                   # create the drone object
drone.pair()                      # connect through the controller

# ---- your flight code goes here ----

drone.close()                     # disconnect when the program ends
```

`drone = Drone()` makes an **object**. Every command after that is called on it, which is why they all start with `drone.` — `drone.takeoff()`, `drone.land()`, and so on.

### 2.2 pair and close

`pair()` connects your program to the controller, which is talking to the drone over its own radio link. The USB cable goes to the controller, not to the drone.

```python
drone.pair()              # find the controller automatically
drone.pair('COM3')        # or name the port yourself if the automatic one fails
```

`close()` hangs up the connection. If you forget it, the next program you run may refuse to connect because the port is still held open by the last one.

---

## 3. Taking Off and Landing

```python
drone.takeoff()     # lifts to about 80 cm and hovers
drone.hover(3)      # stay in place for 3 seconds
drone.land()        # settle down gently where it is
```

**The rule that catches everyone:** put a `hover()` or a `time.sleep()` between `takeoff()` and `land()`. Without it the drone is still stabilizing when the land command arrives and it misses the command entirely. Same goes for `emergency_stop()`.

```python
drone.takeoff()
drone.hover(1)      # required, or the land below may be ignored
drone.land()
```

`hover()` with no number hovers until the next command arrives.

`emergency_stop()` cuts the motors immediately. The drone drops. Use it when something is going wrong, not to end a normal flight.

---

## 4. Moving: Two Different Ways

The library gives you two separate ways to move, and mixing them up is the most common source of confusion. Learn both, then pick one per program.

### 4.1 Distance commands

You say how far to go, and the drone works out the rest.

```python
drone.move_forward(50, "cm", 1)    # 50 cm forward at 1 m/s
drone.move_backward(50, "cm", 1)
drone.move_left(30, "cm", 1)
drone.move_right(30, "cm", 1)
```

The three values are **distance**, **unit**, and **speed in meters per second**. Units can be `"cm"`, `"m"`, `"in"`, or `"ft"`. Speed defaults to 1.0 and maxes out at 2.0.

`move_distance()` moves on all three axes at once, and it **only takes meters** — no unit string:

```python
# forward 0.5 m, left 0.5 m, and up 0.25 m, all at the same time, at 1 m/s
drone.move_distance(0.5, 0.5, 0.25, 1)

# straight back 0.75 m
drone.move_distance(-0.75, 0, 0, 0.75)
```

The four values are **x** (forward and back), **y** (left and right), **z** (up and down), and **speed**. Positive x is forward, positive y is left, positive z is up.

These commands use the downward-facing optical flow sensor to judge distance, so they need a **well-lit, patterned floor**. Over a plain glossy surface the drone cannot see itself moving and the distances come out wrong.

### 4.2 Power and time commands

Here you set how hard to push in each direction, then say how long to push. Nothing moves until you call `move()`.

```python
drone.set_pitch(50)   # forward at 50% power
drone.move(1)         # do it for 1 second
```

The four flight variables:

| Command | Positive value | Negative value |
|---|---|---|
| `set_pitch(power)` | forward | backward |
| `set_roll(power)` | right | left |
| `set_throttle(power)` | up | down |
| `set_yaw(power)` | turn left | turn right |

Power runs from -100 to 100.

**The trap:** these values stay set. If you set pitch to 50 and never change it, every later `move()` in your program still flies forward, even the ones you meant to go sideways. Reset when you are done:

```python
drone.set_pitch(50)
drone.move(1)

drone.reset_move_values()   # back to 0 for all four

drone.set_roll(50)
drone.move(1)               # now this goes right, and only right
```

You can also check what they are currently set to:

```python
values = drone.get_move_values()
print("roll:", values[0])
print("pitch:", values[1])
print("yaw:", values[2])
print("throttle:", values[3])
```

### 4.3 Which one to use

- **Flying a measured path** — a taped course, a set distance — use the distance commands. They are more accurate and easier to read.
- **Reacting to a sensor while flying** — creeping forward until a wall appears — use power and time, because you can call `move()` over and over inside a loop.

Do not mix the two styles inside one flight unless you have a reason. Pick one, keep the program readable.

---

## 5. Turning

```python
drone.turn_degree(90)      # turn left 90 degrees
drone.turn_degree(-90)     # turn right 90 degrees

drone.turn_left()          # left 90 by default
drone.turn_right(45)       # right 45 degrees

drone.turn(50, 2)          # turn left at 50% power for 2 seconds
drone.turn(-20, 5)         # turn right at 20% power for 5 seconds
```

**Positive is left, negative is right.** That is true for `turn()`, `turn_degree()`, and `set_yaw()`. It trips people up because it is the opposite of what most people guess.

`turn_degree()` measures against the heading the drone had when it took off, using the gyroscope, so 90 means 90 from the start — not 90 more than wherever it is now.

---

## 6. Ready-Made Shapes

The library has whole maneuvers built in.

```python
drone.square()      # fly a square
drone.triangle()    # fly a triangle
drone.circle()      # fly a circle
drone.spiral()      # spiral downward
drone.sway()        # sway side to side
drone.flip("back")  # flip: "back", "front", "left", or "right"
```

Each shape takes optional values for speed, how long each side takes, and direction (1 or -1). `drone.square(50, 3, 1)` flies a bigger, slower square than the default.

Two things about `flip()`:

- **It will not run below 50% battery.** It fails silently and your program moves on.
- The drone needs **3 to 4 seconds** to settle afterward. Put a `time.sleep(4)` after it or your next command gets eaten.

```python
import time

drone.hover(3)
drone.flip("back")
time.sleep(4)        # let it recover before the next command
```

These shapes are fun, but writing a square yourself out of `move_forward()` and `turn_degree()` is the point of Unit 3. Use the built-ins to see what the shape should look like, then build your own.

---

## 7. Lights and Sound

```python
drone.set_drone_LED(0, 0, 255, 100)        # red, green, blue, brightness
drone.drone_LED_off()

drone.set_controller_LED(255, 0, 0, 100)
drone.controller_LED_off()
```

Colors are RGB values from 0 to 255. Brightness is 0 to 100. `(255, 0, 0)` is red, `(0, 255, 0)` green, `(0, 0, 255)` blue, `(255, 255, 0)` yellow.

```python
drone.drone_buzzer(400, 300)        # 400 Hz for 300 milliseconds
drone.controller_buzzer(600, 300)
```

The note is a frequency in hertz and the duration is in **milliseconds**, not seconds. `drone_buzzer(440, 1000)` is an A note for one second.

To make sound play *while* the drone does something else:

```python
drone.start_drone_buzzer(500)
# other commands run here while the tone continues
drone.stop_drone_buzzer()
```

LEDs are the easiest way to see what your program is doing without reading the console. Flash green when a sensor check passes, red when it fails.

---

## 8. Sensors

Getter functions read a value and hand it back to your program. You have to do something with what comes back — print it, store it in a variable, or test it in an `if`.

### 8.1 Battery

```python
battery = drone.get_battery()
print("Battery:", battery, "%")
```

Returns the percentage as a number. Check this at the start of every program.

### 8.2 Height and the bottom range sensor

```python
height = drone.get_height()          # centimeters by default
height = drone.get_height("in")      # or "m", "cm", "mm", "in"
```

Measures from the drone down to whatever is under it. Range is up to 150 cm.

Watch the return value:

- **999.9** means nothing was found in range, or the sensor timed out.
- **-100 or 0** means the sensor errored.

Neither of those is a real height, so check for them before you use the number in a calculation.

`get_bottom_range()` reads the same sensor and behaves the same way.

### 8.3 The front range sensor

```python
distance = drone.get_front_range()        # centimeters by default
print(distance)
```

Measures straight ahead, up to 150 cm. Returns **999** when nothing is in range and **-10 or 0** on an error.

Two helpers are built on top of it:

```python
if drone.detect_wall(50):        # True if something is closer than 50 cm
    print("Wall ahead")

drone.avoid_wall(10, 50)         # fly forward for up to 10 s, stop 50 cm from the wall
drone.keep_distance(10, 60)      # fly forward, then hold 60 cm away for 10 s
```

`detect_wall()` defaults to 50 cm. `avoid_wall()` defaults to 2 seconds and 70 cm. `keep_distance()` defaults to 2 seconds and 50 cm.

### 8.4 Color

The drone has two color sensors, one front and one back, pre-calibrated for eight colors that match the color cards.

```python
colors = drone.get_colors()
print("Front:", colors[0])
print("Back:", colors[1])
```

You get back a list of two strings. Each is one of: Red, Green, Yellow, Blue, Cyan, Magenta, Black, White, or **Unknown** when it cannot decide.

Hold the card flat, a few centimeters from the sensor, under steady light. Shadow and angle change the reading more than you would expect, which is why Unknown shows up so often.

`get_color_data()` returns the raw numbers behind the guess if you want to see what the sensor actually measures.

> Training the drone on your own custom colors is not available in the Python for Robolink browser editor. Use the eight built-in colors.

### 8.5 Angles and position

```python
print(drone.get_angle_x())    # roll, in degrees
print(drone.get_angle_y())    # pitch
print(drone.get_angle_z())    # yaw, the direction it is facing

print(drone.get_pos_x())      # forward and back from where it took off, in cm
print(drone.get_pos_y())      # left and right
print(drone.get_pos_z())      # up and down
```

**Both angles and positions reset to zero at takeoff.** Everything they report is measured from the takeoff spot and the takeoff heading, not from any fixed point in the room.

`drone.reset_gyro()` zeroes the angles manually.

If you want everything at once, `get_sensor_data()` returns a list of 31 values in one request, which is faster than calling five getters in a row.

---

## 9. Using Sensors in Loops and Conditions

This is where the drone stops following a script and starts reacting. The pattern is always the same: read the sensor, test the value, decide what to do.

**Test once with an `if`:**

```python
drone.takeoff()
drone.hover(1)

if drone.get_front_range() < 60:
    print("Too close, backing up")
    drone.move_backward(30, "cm", 1)
else:
    print("Clear ahead")
    drone.move_forward(30, "cm", 1)

drone.land()
```

**Keep checking with a `while`:**

```python
drone.takeoff()
drone.set_pitch(30)               # forward, gently

while drone.get_front_range() > 50:
    drone.move()                  # move() with no number keeps going
    print(drone.get_front_range())

drone.set_pitch(0)                # stop pushing forward
drone.hover(1)
drone.land()
```

Read that loop carefully. It creeps forward and re-reads the sensor every pass, and the moment the wall is closer than 50 cm the condition fails and the loop ends. That is the whole idea behind `avoid_wall()`, written out by hand.

**Repeat a fixed number of times with a `for`:**

```python
for i in range(4):                # four sides of a square
    drone.move_forward(50, "cm", 1)
    drone.turn_degree(90)
```

**Always give a `while` loop a way out.** If the sensor returns 999 because nothing is in range, `999 > 50` stays true forever and the drone flies until the battery dies. Guard against it:

```python
distance = drone.get_front_range()

while distance > 50 and distance != 999:
    drone.move()
    distance = drone.get_front_range()
```

---

## 10. Fixing Drift with Trim

If your drone slides to one side while hovering with no commands running, it needs trim.

```python
print(drone.get_trim())      # [roll trim, pitch trim]

drone.set_trim(-5, 0)        # drifting right? trim roll a little to the left
drone.reset_trim()           # back to zero
```

Trim values run -100 to 100 and are **saved on the drone even after you power it off**, so a drone someone else trimmed badly stays bad until you reset it. If a drone flies strangely from the first second, check the trim before you rewrite your code.

If you set trim right before takeoff, put a `time.sleep(1)` in between or the takeoff gets skipped.

---

## 11. When Something Goes Wrong

| What you see | What it usually is |
|---|---|
| Program will not connect | The last program did not call `close()`. Close the other tab or unplug and replug the controller. |
| Drone takes off and immediately lands | No `hover()` between `takeoff()` and the next command. |
| `land()` seems to be ignored | Same cause. Add `hover(1)` before it. |
| Nothing happens at all | The controller is connected but the drone is off or not paired to that controller. |
| Drone keeps flying in one direction | A flight variable is still set from earlier. Call `reset_move_values()`. |
| Distances are wrong | Flying over a plain or shiny floor. The optical flow sensor needs a patterned, well-lit surface. |
| `flip()` does nothing | Battery is under 50%. |
| Command right after a flip is skipped | No `time.sleep(4)` after the flip. |
| Sensor returns 999 or 999.9 | Nothing within 150 cm. That is not a distance, it is "I see nothing." |
| Sensor returns -10, -100, or 0 | Sensor error. Land, wait, try again. |
| Color comes back Unknown | Card is too far, at an angle, or in shadow. |
| Drone drifts while hovering | Trim. See section 10. |

To ask the drone what it thinks is wrong:

```python
print(drone.get_error_data())      # any error states the drone is reporting
print(drone.get_flight_state())    # what it thinks it is doing right now
```

---

## 12. Quick Reference Table

**Connection**

| Command | What it does |
|---|---|
| `pair()` | Connect to the controller |
| `close()` | Disconnect |

**Flight**

| Command | What it does |
|---|---|
| `takeoff()` | Lift to about 80 cm and hover |
| `land()` | Settle down where it is |
| `hover(seconds)` | Stay in place |
| `emergency_stop()` | Cut the motors immediately |

**Movement by distance**

| Command | What it does |
|---|---|
| `move_forward(distance, unit, speed)` | Forward a set distance |
| `move_backward(distance, unit, speed)` | Backward |
| `move_left(distance, unit, speed)` | Left |
| `move_right(distance, unit, speed)` | Right |
| `move_distance(x, y, z, speed)` | All three axes at once, in meters |

**Movement by power and time**

| Command | What it does |
|---|---|
| `set_pitch(power)` | Forward (+) or backward (-) |
| `set_roll(power)` | Right (+) or left (-) |
| `set_throttle(power)` | Up (+) or down (-) |
| `set_yaw(power)` | Turn left (+) or right (-) |
| `move(seconds)` | Run the set values |
| `reset_move_values()` | Set all four back to 0 |
| `get_move_values()` | Read all four |

**Turning**

| Command | What it does |
|---|---|
| `turn_degree(degrees)` | Turn left (+) or right (-) by angle |
| `turn_left(degrees)` | Left, 90 by default |
| `turn_right(degrees)` | Right, 90 by default |
| `turn(power, seconds)` | Turn by power and time |

**Shapes**

| Command | What it does |
|---|---|
| `square()` `triangle()` `circle()` `spiral()` `sway()` | Fly that shape |
| `flip(direction)` | Flip; needs over 50% battery |

**Lights and sound**

| Command | What it does |
|---|---|
| `set_drone_LED(r, g, b, brightness)` | Set the drone light |
| `drone_LED_off()` | Turn it off |
| `set_controller_LED(r, g, b, brightness)` | Set the controller light |
| `drone_buzzer(hz, milliseconds)` | Play a note |
| `start_drone_buzzer(hz)` / `stop_drone_buzzer()` | Play a tone in the background |

**Sensors**

| Command | Returns |
|---|---|
| `get_battery()` | Battery percentage |
| `get_height(unit)` | Height off the floor |
| `get_bottom_range(unit)` | Distance to the floor |
| `get_front_range(unit)` | Distance straight ahead |
| `detect_wall(distance)` | True if something is closer than that |
| `avoid_wall(timeout, distance)` | Fly forward, stop short of a wall |
| `keep_distance(timeout, distance)` | Fly forward, then hold a distance |
| `get_colors()` | List: front color, back color |
| `get_angle_x()` `get_angle_y()` `get_angle_z()` | Roll, pitch, yaw in degrees |
| `get_pos_x()` `get_pos_y()` `get_pos_z()` | Position from the takeoff point |
| `get_sensor_data()` | All 31 sensor values at once |
| `get_error_data()` | Any errors the drone is reporting |

**Trim**

| Command | What it does |
|---|---|
| `get_trim()` | Read roll and pitch trim |
| `set_trim(roll, pitch)` | Correct for drift |
| `reset_trim()` | Back to zero |
