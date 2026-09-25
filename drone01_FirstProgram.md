# Drone Lesson 1: Your First Program

**Goal:** run a program that reads the drone's battery, then run a program that takes off, hovers, and lands.

**Before this lesson:** finish Task 00b, the editor tour. This lesson assumes you can already connect and find your way around the editor.

**You need:** your drone and its matching controller, the data cable, a charged battery, and your taped square on the floor.

**Keep open:** the CoDrone EDU Python Command Reference. Section 2 is the controller, section 3 on is the code.

---

## 1. Pre-flight checks

Every flight, every time:

- Battery in the drone, charged.
- All four propellers on, none chipped or loose.
- Your square is clear. Nothing fragile inside it.
- Controller paired to your drone. The screen should not say SEARCHING.

**Three ways to stop the drone.** Know all three before you click Run:

| Where | How | What happens |
|---|---|---|
| Controller | Hold `L1` and pull the left joystick down | Motors cut. The drone drops. |
| Editor | Land button | Controlled landing, wherever the program is. |
| Editor | Emergency Stop button | Motors cut. Try to catch the drone. |

The controller works no matter what the computer is doing. Keep it in your hands.

---

## 2. Connect and open a file

1. Battery into the drone.
2. Controller into the computer with the data cable. It should pair and go to LINK state on its own. If not, press the power button once.
3. Click **Connect** in the connection window, bottom left. Pick your drone in the popup.
4. The connection window turns green and says CoDrone EDU.

Right-click `my projects` and make a new file called `drone01.py`.

---

## 3. What the starter code does

Every new file comes with this already written:

```python
from codrone_edu.drone import *    # load the drone library

drone = Drone()                    # create an object named drone
drone.pair()                       # connect through the controller

# your code goes here

drone.close()                      # disconnect
```

You do not type these lines. You add to the middle.

`Drone()` makes an object that stands for your physical drone. Everything you write from here starts with `drone.` because you are telling that object what to do. The name has to stay `drone` — the editor does not allow anything else.

If you delete a starter line by accident, opening a new tab brings it back.

---

## 4. First program: no flying

Add two lines in the middle:

```python
from codrone_edu.drone import *

drone = Drone()
drone.pair()

battery = drone.get_battery()      # ask the drone how much charge is left
print("Battery:", battery, "%")    # show it in the Console

drone.close()
```

Click **Run**. Open the **Console** on the right and read the number.

**What happened.** `get_battery()` is a *getter*. It hands a value back to your program, and you have to catch it or it disappears. Here it went into the variable `battery`, and `print()` sent it to the Console. A getter on a line by itself does nothing you can see.

Under 50% battery, swap it. The drone gets unreliable and will not flip.

---

## 5. First flight

Put the drone in the middle of your square, facing away from you. Add three lines:

```python
from codrone_edu.drone import *

drone = Drone()
drone.pair()

print("Battery:", drone.get_battery(), "%")

drone.takeoff()      # lift to about 80 cm and hover
drone.hover(3)       # stay there for 3 seconds
drone.land()         # settle down

drone.close()
```

Before you click Run:

- Controller in your hands.
- Eyes on the drone, not the screen.
- Say "flying" so the people near you know.

Run it.

**The one rule that catches everyone:** you need `hover()` or a `time.sleep()` between `takeoff()` and `land()`. Without it, the drone is still stabilizing when the land command arrives and it never hears it. Delete the `hover(3)` line and run it again — the drone takes off and just sits there. Worth seeing once so you recognize it later.

Use the editor's **Land** button to bring it down after that.

---

## 6. Change three things

Run each as its own flight. Land between attempts.

1. Change `hover(3)` to `hover(1)`, then `hover(6)`. Watch how steady it is at the end of the long one.
2. Add a light before takeoff:
   ```python
   drone.set_drone_LED(0, 255, 0, 100)    # red, green, blue, brightness
   ```
   Make it your own color. Colors go 0–255, brightness 0–100.
3. Add a sound:
   ```python
   drone.drone_buzzer(440, 500)    # 440 Hz for 500 milliseconds
   ```
   The second number is **milliseconds**. 500 is half a second.

Not sure what a command takes? Look it up in the **Documentation** panel on the right. Every function there has its syntax, parameters, and a runnable example.

---

## 7. When it does not work

| What you see | What it usually is |
|---|---|
| Nothing happens when you click Run | Not connected, or the controller is not in LINK state. Check that the connection window is green. |
| Will not connect | Another browser tab still has the drone. Close it. |
| Drone takes off and lands right away | Missing `hover()` between `takeoff()` and `land()`. |
| `land()` gets ignored | Same thing. Add `hover(1)`. |
| Red text in the code | A typo. Spelling and capitalization have to be exact. |
| Red text in the Console | Read the last line first. It usually names the line number. |
| Drone drifts while hovering | It needs trim. Use the direction pad on the controller. Reference section 2.8. |

---

## 8. Save and submit

1. **Name it right in the editor.** This one is `drone01.py`.
2. **Download it.** Right-click the file in the file panel and choose download. Single file, not Download All.
3. **Move it into your repo.** Drag it from Downloads into `Documents\GitHub\CoDrone`.
4. **Commit** in GitHub Desktop with a real summary.
5. **Push origin,** then check github.com. If you cannot see it in a browser, it did not submit.

---

## Turn in

`drone01.py` in your CoDrone repo, plus a `README.md` answering:

1. What was the battery percentage on your first run?
2. What happens if you remove the `hover()` line, and why?
3. What did you change in section 6, and what did the drone do?
