# Drone Lesson 2: Flying a Path

**Goal:** move the drone a measured distance, turn it, and fly a square — first the long way, then with a loop.

**You need:** everything from Lesson 1, plus a tape measure and floor tape.

**Before you start:** the distance commands in this lesson use the downward camera to judge how far the drone has traveled. They need a **patterned, well-lit floor**. Over plain or shiny flooring the numbers come out wrong and it is not your code's fault.

---

## 1. Warm-up: out and back

New file, `path.py`. The starter code is already there — you are filling in the middle.

```python
from codrone_edu.drone import *

drone = Drone()
drone.pair()

drone.takeoff()
drone.hover(1)

drone.move_forward(50, "cm", 1)     # 50 cm forward at 1 m/s
drone.hover(1)
drone.move_backward(50, "cm", 1)    # and back

drone.land()
drone.close()
```

The three values are **distance**, **unit**, and **speed in meters per second**. Units can be `"cm"`, `"m"`, `"in"`, or `"ft"`. Speed goes up to 2.0.

**Measure it.** Tape a mark where the drone starts. Run the program. Tape where it ends up after the forward move, then measure. Write down what you asked for and what you got.

| Asked for | Measured | Difference |
|---|---|---|
| 50 cm | | |

Run it two more times and measure again. The drone does not land in exactly the same spot every time. That is normal, and it is the reason drone competitions give you a target area instead of a target point.

---

## 2. Turning

```python
drone.turn_degree(90)      # turn LEFT 90 degrees
drone.turn_degree(-90)     # turn RIGHT 90 degrees
```

**Positive is left. Negative is right.** That is backwards from what most people guess, so guess wrong once now instead of during a graded run.

`turn_degree()` measures from the direction the drone was facing **when it took off**, not from wherever it is now. So `turn_degree(90)` twice in a row does not spin it 180 degrees — the second one has nothing left to do.

Try this and watch:

```python
drone.takeoff()
drone.hover(1)
drone.turn_degree(90)
drone.hover(1)
drone.turn_degree(90)      # what happens here?
drone.hover(1)
drone.land()
```

Write down what you expected and what actually happened.

---

## 3. A square, the long way

Four sides, four turns, written out. Since `turn_degree()` counts from the takeoff heading, each turn has to be a bigger number than the last.

```python
drone.takeoff()
drone.hover(1)

drone.move_forward(50, "cm", 1)
drone.turn_degree(90)

drone.move_forward(50, "cm", 1)
drone.turn_degree(180)

drone.move_forward(50, "cm", 1)
drone.turn_degree(270)

drone.move_forward(50, "cm", 1)

drone.land()
drone.close()
```

Tape a 50 cm square on the floor and see how close it stays to your tape.

---

## 4. The same square, with a loop

Look at the code above. Two lines repeat four times with only the turn number changing. That is what a `for` loop is for.

```python
for i in range(4):                  # do this four times
    drone.move_forward(50, "cm", 1)
    drone.turn_left()               # turn 90 from where it is facing now
```

`turn_left()` turns 90 degrees relative to the drone's current heading, so the loop does not need to keep track of a growing angle the way section 3 did. Use `turn_left(45)` or `turn_right(30)` for other angles.

`range(4)` gives the loop four passes. The indented lines are the body — they are the part that repeats. **The indentation is what makes them part of the loop.** Lines that are not indented run once, after the loop finishes.

Change `range(4)` to `range(3)` and `turn_left()` to `turn_left(120)`. What shape do you get?

---

## 5. Build a course

Tape a course on the floor with three turns in it. Before you write any code:

1. Walk it and measure each leg in centimeters.
2. Write the legs and turns on paper as a list of steps.
3. Fly the course by hand with the controller.
4. *Then* write the program.

If you cannot fly it by hand, you are not ready to code it.

Rules for the run:
- The drone stays inside the taped area.
- Speed 1 or lower. Fast does not help.
- Controller in your hands the whole time. The editor's Land and Emergency Stop buttons are your backup.

Not sure what a command takes? The **Documentation** panel on the right has the syntax, the parameters, and a runnable example for every function.

---

## 6. If it goes wrong

| What you see | What it usually is |
|---|---|
| Distances are short or long | The floor. The downward sensor needs a patterned, well-lit surface. |
| Drone turns the wrong way | Positive is left, negative is right. |
| Second turn does nothing | `turn_degree()` counts from the takeoff heading, not the current one. |
| Drone drifts off the path | Trim it with the direction pad before blaming the code. |
| Only the first line of the loop repeats | Indentation. Every line in the loop body needs the same indent. |

---

## Turn in

Download the file first — Menu > File > Download All, or right-click `path.py` and download. The editor does not push to GitHub for you.

Push `path.py` and a `README.md` with:

1. Your measurement table from step 1 — asked for, measured, difference, across three runs.
2. What you expected from the two `turn_degree(90)` calls in step 2, and what really happened.
3. Your course sketch with the leg lengths, and how close the drone stayed to it.
