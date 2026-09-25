# Drone Task 00b: The Editor Tour

**Goal:** learn your way around Python for Robolink before you fly anything. No flying in this task.

**Time:** about 25 minutes.

**You need:** your drone, a battery, your controller, and the data cable from your kit.

---

## 1. Watch the tour

Watch the Python for Robolink tour video your instructor plays. Follow along on your own machine as it goes. You will use the checklist in section 6 to prove you found everything.

Python for Robolink is an IDE — an integrated development environment. That is a coding platform built for one job. This one is built for the CoDrone EDU.

Open **codrone.robolink.com/edu/python/** in **Google Chrome**. Not Edge, not Safari, not Firefox. Some features only work in Chrome.

---

## 2. Connect

The connection window is in the **bottom left corner**.

1. Put a battery in the drone to power it on.
2. Plug the controller into the computer with the data cable. The drone and controller pair automatically and the controller should switch to the **LINK state**.
3. If it does not go into LINK state, press the power button once.
4. Click **Connect** in the connection window. A popup appears. Pick your CoDrone EDU and click connect.
5. The connection window turns **green** and says CoDrone EDU. You are connected.

LINK state is the mode where the controller passes your program's commands out to the drone. When the controller is in LINK state, the joysticks do not fly the drone. Press the power button once to go back to hand flying.

---

## 3. The file panel

The file panel is on the **left**. You start with three things:

| Item | What it is |
|---|---|
| `my projects` | Your main project folder. Cannot be deleted. |
| `main.py` | Your starting file. Can be renamed. Cannot be deleted unless another file exists. |
| `color data` | Stores color sensor data from the Color Tool. Cannot be deleted. |

Right-click `my projects` to make a new file, a new folder, or download the whole project. The four icons at the top of the panel do the same jobs: upload a file, upload a folder, new file, new folder.

**File names must end in `.py` or `.txt`.** Nothing else is accepted. No spaces in names.

Clicking a file opens it as a tab at the top of the code area. Closing the tab does not delete the file. To delete, right-click the file and choose delete.

---

## 4. The code area

Open `main.py`. There is already code there — the editor writes a starter program for you:

```python
from codrone_edu.drone import *    # line 1: loads the drone library

drone = Drone()                    # line 3: creates an object named drone
drone.pair()                       # line 4: lets the IDE talk to the drone

# line 6: a comment

drone.close()                      # line 8: disconnects at the end of the program
```

You write your program in the blank space in the middle. Click at the end of a line and press Enter to make room.

**The object has to stay named `drone`.** The editor does not allow a different name. Every command you write starts with `drone.` because you are telling that object to do something.

### Autocomplete

Type `drone.` and a list of every available function drops down. Scroll it. This is how you find out what the drone can do without looking anything up.

If you type a function by hand instead, **spelling and capitalization have to be exact**. `get_front_range()` works. `get_Front_Range()` does not. Mistakes show up in red.

### What the colors mean

| Color | What it is |
|---|---|
| Blue | Python keywords and functions |
| Yellow | Parentheses and brackets — nested ones change color so you can match pairs |
| Green | Comments |
| Orange | Strings |
| Light green | Integers and floats |
| White | Everything else |

Blank lines are skipped when the program runs, so use them to group your code.

---

## 5. The rest of the screen

### Program controls (top, next to the menu)

| Button | What it does |
|---|---|
| **Run** | Runs your program. |
| **Land** | Brings the drone down for a controlled landing, wherever the program happens to be. |
| **Emergency Stop** | Cuts the motors. Emergencies only. Try to catch the drone. |

You now have two ways to stop a flight: these buttons, and the controller in your hand. Know where both are before you ever click Run.

### Tools panel (right side)

- **Console** — where `print()` output shows up, and where errors and warnings appear.
- **Color Tool** — collects color samples so the drone can recognize colors beyond the cards in your kit. Later lesson.
- **Documentation** — the whole CoDrone EDU library. Pick any function and read its description, syntax, parameters, what it returns, and an example. You can open the example straight into the editor and run it. **When you do not know what a command does, look here first.**

### Sensor dashboard

Click the **double arrows in the upper right corner**. Live readings from the drone's sensors. Pick the drone up and tilt it and watch the numbers move. Some sensors only report when the drone is on a flat surface.

### Menu

- **File > Download All** saves the whole project to your computer.
- **File > Reset Workspace** wipes everything back to default. It warns you first. Do not click this by accident.
- **Help** has system requirements and release notes.

---

## 6. Checklist

Do all of these. Check them off as you go.

- [ ] Opened the editor in Chrome
- [ ] Connected the drone — connection window is green and says CoDrone EDU
- [ ] Made a new folder inside `my projects`
- [ ] Made a new file named `drone00b.py` inside that folder
- [ ] In `drone00b.py`, printed your name to the Console with `print("your name")` and clicked Run
- [ ] Typed `drone.` and scrolled the autocomplete list
- [ ] Found `takeoff()` in the Documentation panel and read what it does
- [ ] Opened the sensor dashboard and tilted the drone to make the numbers change
- [ ] Downloaded the project with Menu > File > Download All

---

## 7. Save and submit

Same five steps for every drone assignment this unit.

1. **Name it right in the editor.** This one is `drone00b.py`. The file name is part of the grade.
2. **Download it.** Right-click the file in the file panel and choose download. Use the single-file download, not Menu > File > Download All — that packages the whole project and you would have to unpack it.
3. **Move it into your repo.** Drag it out of Downloads into `Documents\GitHub\CoDrone`. It has to be in that folder or GitHub Desktop cannot see it.
4. **Commit.** In GitHub Desktop the file shows up under Changes. Write a real summary, then Commit to main.
5. **Push origin,** then open your repo on github.com and confirm the file is there. If you cannot see it in a browser, it did not submit.

Do this at the end of every session, even if the work is not finished. The editor autosaves, but it does not back up to anything you own.

---

## Turn in

`drone00b.py` in your CoDrone repo, plus a `README.md` answering:

1. What did the connection window look like before you connected, and after?
2. Name three functions you found in the autocomplete list that you have not used yet.
3. What did the Documentation panel say `takeoff()` does?
4. Name one sensor on the dashboard that only reads when the drone is sitting on a flat surface.
