# CoDrone Task 00b: Set Up Your CoDrone Repo

**Goal:** build one repository called `CoDrone` on your machine and on GitHub, and get your first Python file into it.

**You will use this repo for every drone assignment this unit.** Build it once, correctly, and the rest of the unit is just adding files to it.

---

## 1. Make the repository

In **GitHub Desktop**:

1. Make sure you are signed in to your own GitHub account. File > Options > Accounts if you are not sure.
2. **File > New repository.**
3. Name: `CoDrone` — exactly that, capital C, capital D, no spaces.
4. Local path: your **GitHub** folder. GitHub Desktop usually fills this in already. Do not change it to Desktop or Downloads.
5. Check **Initialize this repository with a README**.
6. Click **Create repository**.

You now have a folder at `Documents\GitHub\CoDrone` with a README in it.

---

## 2. Publish it

The repo only exists on your machine so far. GitHub does not know about it.

1. Click **Publish repository** at the top of GitHub Desktop.
2. **Uncheck "Keep this code private."** It has to be public to be graded.
3. Click **Publish repository**.

Go to github.com and confirm `CoDrone` is there. If you cannot see it in a browser, it did not publish.

---

## 3. Open the folder in VS Code

In GitHub Desktop, click **Open in Visual Studio Code**. This opens the `CoDrone` folder, not a single file.

Check the title bar or the Explorer panel on the left — it should say CoDrone. If VS Code opened some other folder, close it and click the button again. Files saved into the wrong folder do not show up in GitHub Desktop, and that is the most common reason work goes missing.

---

## 4. Get your Python file into the folder

1. In Python for Robolink, make sure your file is named exactly what the assignment asks for. For this one it is `drone00b.py`.
2. Right-click the file in the editor's file panel and choose **download**. Use the single-file download, not Menu > File > Download All — that packages the whole project and you would have to unpack it.
3. Open your Downloads folder and drag `drone00b.py` into `Documents\GitHub\CoDrone`.
4. In VS Code, open the file and confirm your code is actually in it.

The file has to sit inside the CoDrone folder. A file saved to Desktop or left in Downloads does not exist as far as git is concerned, and that is the most common reason work goes missing.

If you need to edit the code afterward, do it in VS Code and save with Ctrl+S. An unsaved file has a dot on its tab and GitHub Desktop cannot see the change.

---

## 5. Commit and push

Back in **GitHub Desktop**:

1. `drone00b.py` shows up on the left under **Changes**. If it does not, you either did not save or the file is not in the CoDrone folder.
2. Type a summary in the box at the bottom left. Something real: `added editor tour code`. Not `asdf`.
3. Click **Commit to main**.
4. Click **Push origin** at the top. Committing saves it locally. Pushing sends it to GitHub.

Open your repo on github.com and confirm `drone00b.py` is there with your code in it. **If you cannot see it in the browser, I cannot grade it.**

---

## 6. Every assignment after this

Same five steps, every time:

1. Write the code in Python for Robolink, named exactly what the assignment asks for — `drone01.py`, `drone02.py`, and so on.
2. Right-click the file in the editor and download it.
3. Drag it from Downloads into `Documents\GitHub\CoDrone`.
4. Commit in GitHub Desktop with a real summary.
5. Push origin, then check github.com.

You only build the repo once. Everything else goes into the same one.

If you download the same file twice you will end up with `drone01 (1).py` in Downloads. Delete the extras before you move anything, or you will submit the wrong version.

---

## Troubleshooting

| What you see | What it usually is |
|---|---|
| File does not appear under Changes | Not saved, or the file is not in the CoDrone folder. |
| Wrong version of the code on GitHub | You moved an older copy out of Downloads. Check for `(1)` in the file name. |
| No Publish repository button | Already published. Look for Push origin instead. |
| Repo is not on github.com | You committed but never pushed. |
| Commit button is greyed out | The summary box is empty. |
| VS Code shows the wrong files | It opened a different folder. Use Open in Visual Studio Code from GitHub Desktop. |
| Asked to sign in repeatedly | Signed out of GitHub Desktop. File > Options > Accounts. |

---

## Turn in

Your `CoDrone` repo, public, containing `drone00b.py` and a README. Nothing else to submit — checking the repo is the grade.
