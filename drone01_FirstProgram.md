# Drone Lesson 1: Your First Program

**Goal:** connect the drone to the browser editor, run a program that checks the battery, then run a program that takes off, hovers, and lands.

**You need:** your drone and its matching controller, the USB cable, a charged drone battery, and your taped square on the floor.

**Keep open:** the CoDrone EDU Python Command Reference. Section 2 is the controller, section 3 is the code.

---

## 1. Before you touch the computer

Do the same checks you did for hand flying:

- Battery in the drone, charged.
- All four propellers on, none chipped or loose.
- Your square is clear. Nothing fragile inside it.
- Controller on, paired to your drone. The screen should not say SEARCHING.

**Your controller stays in your hands for the whole lesson.** If the drone does something you did not expect, press and hold `L1` and pull down on the left joystick. That is emergency stop, and it works no matter what your program is doing.

---

## 2. Open the editor

Go to **codrone.robolink.com/edu/python/** in **Google Chrome**. It has to be Chrome, on a laptop or Chromebook. Tablets and phones will not work.

Four parts of the screen to know:

| Part | Where | What it is for |
|---|---|---|
| Files | Left side | Your Python files. New file and upload icons are at the top right of this panel. |
| Code editor | Middle | Where you type. Each file you open gets a tab. |
| Console | Right side, click the Console button | Where `print()` output shows up, and where errors appear. |
| Connect | Top of the page | Connects the editor to your controller. |

The layout changes a little when Robolink updates the site. If a button is not exactly where this says, look around for the same word.

Make a new Python file and name it `first_flight.py`. Do not use spaces in the file name.

---

## 3. Plug in and connect

1. Plug the USB cable into the **controller**, not the drone. The drone has no data port you will ever use.
2. Click **Connect** in the editor. Chrome will pop up a list of ports it can see. Pick the one that appeared when you plugged in the controller and confirm.
3. The controller should switch to the **LINK state**. That is the mode where it passes your program's commands out to the drone.

Two things that go wrong here:

- **No ports in the list.** Try the other end of the cable, or a different cable. Some USB cables are charge-only and cannot carry data.
- **"Drone is connected in another tab."** You or someone else left the editor open somewhere. Close the other tab and try again.

---

## 4. Your first program: no flying

Type this. Do not paste it — typing it is how you learn where the parentheses go.

```python
from codrone_edu.drone import *    # load the drone commands

drone = Drone()                    # create the drone object
drone.pair()                       # connect through the controller

battery = drone.get_battery()      # ask the drone how much charge is left
print("Battery:", battery, "%")    # show it in the Console

drone.close()                      # disconnect
```

Run it. Open the Console and read the number.

**What just happened.** `Drone()` made an object that represents your drone. Every command after that starts with `drone.` because you are telling that object to do something. `get_battery()` is a *getter* — it hands a value back to your program, and you have to do something with it or it disappears. Here we stored it in `battery` and printed it.

If the number is under 50%, swap the battery before you fly. Under 50% the drone gets unreliable and will not flip.

---

## 5. Your first flight

Put the drone in the middle of your square, facing away from you. Add three lines to the middle of your program:

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
- Call out "flying" so the people near you know.

Run it.

**The one rule that catches everyone:** you need `hover()` or a `time.sleep()` between `takeoff()` and `land()`. Without it, the drone is still stabilizing when the land command arrives and it never hears it. If you delete the `hover(3)` line and run it again, you will see the drone take off and just sit there. Try it — it is worth seeing once.

---

## 6. Change three things

Run each of these as its own flight. Land the drone between attempts.

1. Change `hover(3)` to `hover(1)` and then to `hover(6)`. Watch how long it holds position.
2. Add a light before takeoff:
   ```python
   drone.set_drone_LED(0, 255, 0, 100)    # green, full brightness
   ```
   The four numbers are red, green, blue, and brightness. Make it your own color.
3. Add a sound:
   ```python
   drone.drone_buzzer(440, 500)    # 440 Hz for 500 milliseconds
   ```
   The second number is **milliseconds**, not seconds. 500 is half a second.

---

## 7. When it does not work

| What you see | What it usually is |
|---|---|
| Nothing happens when you click Run | Not connected, or the controller is not in the LINK state. |
| Program will not connect | Another tab still has the drone. Close it. |
| Drone takes off and lands right away | Missing `hover()` between `takeoff()` and `land()`. |
| `land()` gets ignored | Same thing. Add `hover(1)`. |
| Red error text in the Console | Read the last line first. It usually names the line number and the misspelled command. |
| Drone drifts while hovering | It needs trim. Use the direction pad on the controller. See section 2.8 of the reference. |

---

## Turn in

Push `first_flight.py` to your repo, plus a short `README.md` that answers:

1. What was the battery percentage on your first run?
2. What happens if you remove the `hover()` line, and why?
3. What did you change in step 6, and what did the drone do?
