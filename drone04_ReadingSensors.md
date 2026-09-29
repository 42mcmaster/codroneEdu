# Drone Lesson 4: Reading Sensors

**Goal:** get numbers out of the drone's sensors, and understand what those numbers actually mean.

**Most of this lesson needs no flying.** The drone sits on your desk and you read it. Only sections 6 and 7 take off.

**File name:** `drone04.py`

---

## 1. What a getter is

A command like `takeoff()` tells the drone to *do* something. A command like `get_battery()` asks the drone a question and hands the answer back.

Commands that hand something back are called **getters**. You can spot them — they all start with `get_`.

Here is the trap. This line runs, gets an answer, and throws it away:

```python
drone.get_battery()        # does nothing you can see
```

The drone answered. Nothing caught the answer. You have to do one of three things with it:

```python
print(drone.get_battery())              # 1. show it

battery = drone.get_battery()           # 2. store it in a variable
print("Battery:", battery, "%")

if drone.get_battery() < 50:            # 3. test it
    print("Swap the battery")
```

Storing it in a variable is usually best, because then you can use the same number more than once without asking the drone again.

---

## 2. Battery

```python
battery = drone.get_battery()
print("Battery:", battery, "%")
```

Returns a percentage as a plain number — `87`, not `"87%"`. That matters, because you can do math on a number:

```python
battery = drone.get_battery()
print("Battery:", battery, "%")
print("Flights left, roughly:", battery // 15)
```

Run this at the start of every program you write for the rest of the unit.

---

## 3. Height and the bottom range sensor

The drone has a sensor pointing straight down.

```python
height = drone.get_height()           # centimeters by default
print(height)

print(drone.get_height("in"))         # "m", "cm", "mm", or "in"
```

`get_bottom_range()` reads the same sensor and behaves the same way.

**Range is about 150 cm.** Beyond that it cannot see the floor.

**Watch for junk values.** The sensor does not return "I don't know." It returns numbers that are not heights:

| What you get | What it means |
|---|---|
| A normal number | A real measurement |
| **999.9** | Nothing found in range, or the sensor timed out |
| **-100 or 0** | The sensor errored |

Neither 999.9 nor -100 is a height. If you use them in math, your program will do something strange and you will blame the wrong thing.

**Try it on the desk.** Print the height with the drone sitting flat, then hold it up 30 cm, then hold it near the ceiling. Write down all three numbers.

---

## 4. The front range sensor

Same idea, pointing forward.

```python
distance = drone.get_front_range()        # cm by default
print(distance)
```

Range is about 150 cm. Junk values here are **999** for nothing in range, and **-10 or 0** for an error.

**Try it on the desk.** Set the drone down facing a wall or a book. Print the reading. Now slide the book slowly away and print again. Find the exact distance where it flips to 999. Write that number down — it is the real edge of what this sensor can see, and it is not always exactly 150.

There are two helpers built on top of this sensor:

```python
if drone.detect_wall(50):        # True if something is closer than 50 cm
    print("Wall ahead")
```

`detect_wall()` does the comparison for you and hands back `True` or `False` instead of a number. You will use that in the next lesson.

---

## 5. Color

The drone has two color sensors on its underside, one front and one back. They are calibrated for the eight colors on the cards in your kit.

```python
colors = drone.get_colors()
print("Front:", colors[0])
print("Back:", colors[1])
```

You get back a **list of two strings**. `colors[0]` is the front sensor and `colors[1]` is the back one. Counting from zero is normal in Python — the first item is item 0.

Each string is one of: Red, Green, Yellow, Blue, Cyan, Magenta, Black, White, or **Unknown**.

**Try it on the desk.** Hold a color card flat, a few centimeters under the drone, and print the result. Then try these and write down what happens:

- Card at an angle instead of flat
- Card in the shadow of your hand
- Card 20 cm away

You will get Unknown a lot. That is the sensor being honest — it could not decide. Lighting and angle change the reading more than you would expect.

> The color sensors are turned off while the drone is flying. They only read when it is on the ground.

---

## 6. Angles and position

These need the drone in the air, so read this section before you fly.

```python
print(drone.get_angle_x())    # roll — tipping side to side
print(drone.get_angle_y())    # pitch — tipping nose up and down
print(drone.get_angle_z())    # yaw — which direction it faces

print(drone.get_pos_x())      # cm forward and back from where it took off
print(drone.get_pos_y())      # cm left and right
print(drone.get_pos_z())      # cm up and down
```

**Everything here resets to zero at takeoff.** These are not compass headings or room coordinates. They are measured from the exact spot and the exact direction where the drone left the ground. Move the drone to a new starting spot and zero moves with it.

Fly this and watch the numbers:

```python
drone.takeoff()
drone.hover(1)

print("Start:", drone.get_pos_x(), drone.get_pos_y())

drone.move_forward(50, "cm", 1)
drone.hover(1)
print("After forward:", drone.get_pos_x(), drone.get_pos_y())

drone.move_left(30, "cm", 1)
drone.hover(1)
print("After left:", drone.get_pos_x(), drone.get_pos_y())

drone.land()
```

You asked for 50 cm forward and 30 cm left. Compare that to what the drone says it did, and compare both to where it actually is on the floor. Three different numbers, and none of them are wrong exactly — they are measurement, command, and reality.

`drone.reset_gyro()` zeroes the angles by hand if you need to.

---

## 7. Hovering is not holding still

```python
drone.takeoff()
drone.hover(1)

for i in range(10):
    print(drone.get_height())
    time.sleep(1)

drone.land()
```

Ten height readings over ten seconds while the drone just sits there. They will not all be the same number.

Write down the highest and the lowest. That spread is what your programs have to survive. When you write a program next lesson that reacts to a sensor, this is the noise it is reacting to.

---

## 8. The sensor dashboard

Click the **double arrows in the upper right corner** of the editor to open the sensor dashboard. Live readings, no code required.

Put it side by side with your program. Run a getter, and confirm the number your code printed matches what the dashboard shows. If they disagree, you are reading the wrong sensor.

The dashboard is the fastest way to answer "is the sensor broken or is my code broken." Some sensors only report when the drone is on a flat surface.

**All at once.** If you want every sensor in one request:

```python
data = drone.get_sensor_data()     # a list of 31 values
print(len(data))
```

One request for 31 values is much faster than calling five getters in a row. You will not need this yet, but it is there.

---

## 9. When it does not work

| What you see | What it usually is |
|---|---|
| Nothing prints | You called a getter without `print()` or a variable. |
| 999 or 999.9 | Nothing is in range. That is not a distance. |
| -10, -100, or 0 | Sensor error. Land, wait a moment, try again. |
| Color is always Unknown | Card too far, at an angle, or shadowed. Try flat and close. |
| Color readings while flying | Not possible. Color sensors are off in the air. |
| Angles are not what you expect | They reset at takeoff. Everything is measured from there. |
| Numbers jump around while hovering | That is real. See section 7. |
| `time` is not defined | Add `import time` at the top. |

---

## 10. Save and submit

1. **Name it right in the editor.** This one is `drone04.py`.
2. **Download it.** Right-click the file in the file panel and choose download. Single file, not Download All.
3. **Move it into your repo.** Drag it from Downloads into `Documents\GitHub\CoDrone`.
4. **Commit** in GitHub Desktop with a real summary.
5. **Push origin,** then check github.com.

---

## Turn in

`drone04.py` in your CoDrone repo, plus a `README.md` answering:

1. Your three height readings from section 3 — flat on the desk, held at 30 cm, held near the ceiling.
2. The exact distance where the front range sensor flipped to 999.
3. What does 999 mean, and why can't you treat it as a distance?
4. Your color sensor results — flat and close, at an angle, in shadow, and far away.
5. From section 6: what you asked for, what the drone reported, and where it actually ended up.
6. From section 7: the highest and lowest height readings while hovering in place.
