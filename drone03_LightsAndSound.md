# Drone Lesson 3: Lights and Sound

**Goal:** control the drone's LED and buzzer, and use them to show what your program is doing.

**Most of this lesson needs no flying.** The drone sits on your desk, plugged in through the controller, and you watch it light up and beep. Only the last section takes off.

**File name:** `drone03.py`

---

## 1. Colors

```python
drone.set_drone_LED(255, 0, 0, 100)      # red
drone.set_drone_LED(0, 255, 0, 100)      # green
drone.set_drone_LED(0, 0, 255, 100)      # blue
```

The four numbers are **red, green, blue, brightness**. The colors go 0 to 255. Brightness goes 0 to 100.

Every color on a screen is made this way — three lights mixed together. Nothing in there is "orange"; orange is a lot of red plus some green.

| Color | Red | Green | Blue |
|---|---|---|---|
| Red | 255 | 0 | 0 |
| Green | 0 | 255 | 0 |
| Blue | 0 | 0 | 255 |
| Yellow | 255 | 255 | 0 |
| Purple | 128 | 0 | 128 |
| Orange | 255 | 165 | 0 |
| White | 255 | 255 | 255 |

**Try it.** Make the drone show five colors in a row, one second apart:

```python
import time

drone.set_drone_LED(255, 0, 0, 100)
time.sleep(1)
drone.set_drone_LED(255, 165, 0, 100)
time.sleep(1)
drone.set_drone_LED(255, 255, 0, 100)
time.sleep(1)
```

`time.sleep(1)` pauses the program for one second. Without it, all three lines run instantly and you only ever see the last color.

Add your own two colors to the end. Then make one the library has no name for and write down the numbers.

---

## 2. Brightness and turning it off

```python
drone.set_drone_LED(0, 0, 255, 100)     # full
drone.set_drone_LED(0, 0, 255, 50)      # half
drone.set_drone_LED(0, 0, 255, 10)      # dim

drone.drone_LED_off()
```

The controller has its own LED:

```python
drone.set_controller_LED(0, 255, 0, 100)
drone.controller_LED_off()
```

**Turn the lights off at the end of your program.** The LED stays on whatever you last set it to, even after the program ends. It drains the battery and it confuses the next person.

---

## 3. The buzzer

```python
drone.drone_buzzer(440, 500)         # 440 Hz for 500 milliseconds
drone.controller_buzzer(880, 200)
```

Two numbers: **frequency in hertz** and **duration in milliseconds**.

**The duration is milliseconds, not seconds.** 1000 is one second. If you write `drone_buzzer(440, 2)` you get a two-millisecond beep, which you will not hear, and you will think the command is broken.

Higher frequency means a higher pitch. Doubling the frequency raises it one octave.

| Note | Hz |
|---|---|
| C (middle) | 262 |
| D | 294 |
| E | 330 |
| F | 349 |
| G | 392 |
| A | 440 |
| B | 494 |
| C (one octave up) | 523 |

**Try it.** Play the first five notes going up, then back down. Use 300 milliseconds each.

---

## 4. A sound that keeps playing

`drone_buzzer()` stops the program while the tone plays. Sometimes you want the sound to keep going while other things happen:

```python
drone.start_drone_buzzer(500)        # starts and does not wait
drone.set_drone_LED(255, 0, 0, 100)  # this happens during the tone
time.sleep(2)
drone.stop_drone_buzzer()            # you have to stop it yourself
```

If you forget `stop_drone_buzzer()`, it keeps buzzing after your program ends. Pull the battery if that happens.

---

## 5. Status lights

This is the part that is actually useful. Lights and sounds tell you what your program is doing while you are watching the drone instead of the screen.

Pick a color for each stage and add them to a flight:

```python
drone.set_drone_LED(0, 0, 255, 100)      # blue: getting ready
drone.drone_buzzer(392, 200)
time.sleep(1)

drone.takeoff()
drone.set_drone_LED(0, 255, 0, 100)      # green: flying
drone.hover(3)

drone.set_drone_LED(255, 255, 0, 100)    # yellow: about to land
drone.land()

drone.drone_buzzer(262, 400)
drone.drone_LED_off()
```

Fly it and watch the drone, not the Console. You can tell exactly where the program is without reading anything.

You will use this for the rest of the unit. When a sensor program does something strange, a color change at the right moment tells you which part of your code ran.

---

## 6. Build one

Write a startup sequence of your own. It has to include:

- At least three colors
- At least three different tones
- `time.sleep()` used so each step is visible or audible
- Lights and buzzer both off at the end

No flying required. Make it something you would actually want to hear when the drone powers up.

**Going further, if you finish early:** play a short tune you know. Use the note table in section 3 and adjust the durations. Eight to ten notes is plenty.

---

## 7. When it does not work

| What you see | What it usually is |
|---|---|
| Only the last color shows | No `time.sleep()` between them. The lines run instantly. |
| No sound at all | The duration is in milliseconds. 2 is too short to hear. Try 500. |
| Buzzer will not stop | Missing `stop_drone_buzzer()`. Pull the battery. |
| LED stays on after the program ends | That is normal. Add `drone_LED_off()` at the end. |
| `time` is not defined | You need `import time` at the top of your file. |

---

## 8. Save and submit

1. **Name it right in the editor.** This one is `drone03.py`.
2. **Download it.** Right-click the file in the file panel and choose download. Single file, not Download All.
3. **Move it into your repo.** Drag it from Downloads into `Documents\GitHub\CoDrone`.
4. **Commit** in GitHub Desktop with a real summary.
5. **Push origin,** then check github.com.

---

## Turn in

`drone03.py` in your CoDrone repo, plus a `README.md` answering:

1. What RGB numbers did you use for the color you invented, and what does it look like?
2. What does `time.sleep()` do, and what happens to your color sequence without it?
3. Which stages of your flight got which color in section 5?
