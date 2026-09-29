# Drone Lesson 5: Making Decisions

**Goal:** write a program that reads a sensor and decides what to do, instead of following a fixed script.

Every program you have written so far does the same thing every time you run it. This one doesn't. The drone reacts to what is in front of it.

**File name:** `drone05.py`

**Before you fly anything in this lesson, test the logic on the ground.** Section 3 shows you how. Debugging a loop with a drone in the air is a bad way to spend a period.

---

## 1. Comparing numbers

A decision starts with a comparison. Python compares two values and hands back `True` or `False`.

| Operator | Means |
|---|---|
| `<` | less than |
| `>` | greater than |
| `<=` | less than or equal to |
| `>=` | greater than or equal to |
| `==` | equal to |
| `!=` | not equal to |

**`==` is two equals signs.** One equals sign stores a value; two compares. This is the single most common typo in the language.

```python
distance = 45
print(distance < 60)      # True
print(distance == 45)     # True
print(distance != 45)     # False
```

Try it on the desk with a real sensor:

```python
print(drone.get_front_range() < 60)
```

Put your hand in front of the drone and run it again. Same line, different answer.

---

## 2. if and else

```python
if drone.get_front_range() < 60:
    print("Something is close")
else:
    print("Clear ahead")
```

Read it out loud: *if the front range is under 60, do the first block, otherwise do the second.*

Two things Python is strict about:

**The colon.** The `if` line ends with `:`. So does the `else` line.

**The indentation.** The lines that belong to the `if` are indented. That indentation is not decoration — it is how Python knows which lines are inside the decision.

```python
if drone.get_front_range() < 60:
    print("close")          # inside the if
    print("backing up")     # also inside the if
print("done")               # NOT inside — runs either way
```

You do not need an `else`. Sometimes you only want to act when something is true:

```python
if drone.get_battery() < 50:
    print("Battery low, swap it")
```

---

## 3. Test on the ground first

Before any of this flies, run it with no takeoff in the program at all:

```python
from codrone_edu.drone import *

drone = Drone()
drone.pair()

distance = drone.get_front_range()
print("Distance:", distance)

if distance < 60:
    drone.set_drone_LED(255, 0, 0, 100)     # red means "would back up"
    print("Would back up")
else:
    drone.set_drone_LED(0, 255, 0, 100)     # green means "would go forward"
    print("Would go forward")

time.sleep(2)
drone.drone_LED_off()
drone.close()
```

Slide a book toward the drone and run it again. The light and the message should flip.

**Get the logic right on the desk, then add flight.** You already know how to use lights as status indicators from lesson 3 — this is where that pays off, because in the air you will be watching the drone, not the Console.

---

## 4. Deciding in the air

Now the same decision with the drone flying:

```python
drone.takeoff()
drone.hover(1)

if drone.get_front_range() < 60:
    drone.set_drone_LED(255, 0, 0, 100)
    drone.move_backward(30, "cm", 1)
else:
    drone.set_drone_LED(0, 255, 0, 100)
    drone.move_forward(30, "cm", 1)

drone.hover(1)
drone.land()
drone.drone_LED_off()
```

Run it twice — once with clear space ahead, once with a box in front of the drone. Same program, two different flights.

**This only checks once.** The `if` runs a single time, right after the hover. Whatever happens after that, the drone is not looking anymore.

---

## 5. while: checking over and over

A `while` loop keeps running as long as its condition stays true.

```python
drone.takeoff()
drone.hover(1)
drone.set_pitch(30)                        # lean forward, gently

while drone.get_front_range() > 50:
    drone.move()                           # move() with no number keeps going
    print(drone.get_front_range())

drone.set_pitch(0)                         # stop pushing forward
drone.hover(1)
drone.land()
```

Walk through what happens:

1. Check the sensor. Is it more than 50 cm to whatever is ahead?
2. If yes, run the loop body — nudge forward, print the reading.
3. Go back to step 1 and check *again*.
4. When the reading finally drops to 50 or below, the condition is false and the loop ends.
5. The program continues with the lines after the loop.

That is the difference between `if` and `while`. An `if` asks once. A `while` asks again every time around.

Notice `set_pitch(30)` is outside the loop and `move()` is inside. You set the direction once, then repeatedly tell it to keep going. And `set_pitch(0)` after the loop matters — without it, the drone is still leaning forward when the loop ends.

---

## 6. The trap that will get you

The front range sensor returns **999** when nothing is in range. Look at the condition again:

```python
while drone.get_front_range() > 50:
```

Is 999 greater than 50? Yes. So if the drone is pointed at open space, the condition stays true, the loop never ends, and the drone flies forward until the battery dies or it hits something.

**Every `while` loop needs a way out.** Guard against the junk value:

```python
distance = drone.get_front_range()

while distance > 50 and distance != 999:
    drone.move()
    distance = drone.get_front_range()      # update it, or the loop never changes
```

Two things changed here:

**The `and`.** Both conditions have to be true to keep looping. Real distance over 50 *and* not the "I see nothing" value.

**The variable gets updated inside the loop.** Read that last line carefully. If you read the sensor once before the loop and never again, `distance` holds the same number forever and the loop runs forever. The loop has to re-read the thing it is testing.

**Controller in your hands for every `while` loop you write.** This is the lesson where a program can genuinely run away from you.

---

## 7. A second way out: give it a time limit

You can also stop a loop after a set amount of time, whatever the sensor says:

```python
import time

start = time.time()                        # seconds, right now

while time.time() - start < 5:             # keep going for 5 seconds
    print(drone.get_front_range())
    time.sleep(0.5)
```

`time.time()` gives you a number of seconds. Subtract the start from the current time and you have how long you have been running.

A timeout is a good safety net on any loop that depends on a sensor. If the sensor misbehaves, the clock still runs out.

---

## 8. The built-in versions

The library already has these two patterns written for you:

```python
drone.avoid_wall(10, 50)        # fly forward up to 10 seconds, stop 50 cm short
drone.keep_distance(10, 60)     # fly forward, then hold 60 cm away for 10 seconds
```

Both take a timeout and a distance, which is exactly the two exits from sections 6 and 7.

Fly your own version from section 6, then fly `avoid_wall(10, 50)`. They do the same job. Write down which one stopped closer to the wall and which one you could actually change if you needed different behavior.

Knowing what is inside `avoid_wall()` is the point. Anyone can call a function. Writing the loop yourself is what tells you why it needs a timeout.

---

## 9. Build one

Write a program that does all of this:

- Checks the battery before takeoff and prints it
- Takes off
- Uses a `while` loop to creep toward something, with **both** guards — the junk-value check and a timeout
- Changes the LED color when the loop ends
- Lands

Test the loop logic on the ground first, the way section 3 shows. Then fly it.

**Going further, if you finish early:** land on a color. Fly forward until `get_front_range()` says you are close to something, land, then read `get_colors()` and beep a different tone depending on what color the drone is sitting on. Remember the color sensors only work on the ground.

---

## 10. When it does not work

| What you see | What it usually is |
|---|---|
| Drone flies forever | No guard against 999, or the variable is never updated inside the loop. |
| Loop ends immediately | The condition was already false on the first check. Print the sensor value and see. |
| `SyntaxError` on the `if` line | Missing colon at the end. |
| `IndentationError` | Lines inside an `if` or `while` need to be indented the same amount. |
| Only the first line repeats | The rest of the body is not indented, so it is outside the loop. |
| Drone keeps drifting after the loop | You never set the flight values back to 0. Add `set_pitch(0)` or `reset_move_values()`. |
| Comparison is always True | You wrote `=` instead of `==`. |
| Works on the desk, not in the air | The readings are noisier while hovering. Check lesson 4 section 7. |

---

## 11. Save and submit

1. **Name it right in the editor.** This one is `drone05.py`.
2. **Download it.** Right-click the file in the file panel and choose download. Single file, not Download All.
3. **Move it into your repo.** Drag it from Downloads into `Documents\GitHub\CoDrone`.
4. **Commit** in GitHub Desktop with a real summary.
5. **Push origin,** then check github.com.

---

## Turn in

`drone05.py` in your CoDrone repo, plus a `README.md` answering:

1. What is the difference between `if` and `while`, in your own words?
2. Why does `while drone.get_front_range() > 50:` never end when the drone faces open space?
3. What are the two guards you put on your loop in section 9, and what does each one protect against?
4. From section 8: your loop versus `avoid_wall()` — which stopped closer, and which would you rather use if the rules changed?
