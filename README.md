# Cut Detective

A continuity editing lab for Applied Media students, in two parts.

**Part 1, Identify.** Fifteen marked edits in a short film. Students name the technique at each one and write a one sentence justification. Each question plays its own passage and stops on its own, and answers lock on submission.

**Part 2, Quiz.** Ten multiple choice questions drawn from the *A Brief History of Editing* lecture, covering early cinema, the Lumiere brothers, Porter, Griffith and the birth of continuity, Soviet montage, the French New Wave and the jump cut, and the arrival of synchronised sound. Four options each, one at a time, answers lock on submission, and the reason appears whether the answer was right or wrong.

Both parts self mark, out of 25 in total, and everything is recorded to the instructor dashboard as the student works.

## Deploy

1. Create a repository and upload the contents of this folder, keeping the structure below.
2. Settings, Pages, Build and deployment, Source: Deploy from a branch. Pick `main` and `/ (root)`.
3. Open the published URL. No build step, no dependencies to install.

## Structure

```
index.html                  the whole activity, single file
assets/
  videos/
    night-shift.mp4         the film the quiz is built on, 37 MB
    cut-on-action.mp4       one reference clip per technique
    eyeline-match.mp4
    graphic-match.mp4
    rule-180.mp4
    shot-reverse-shot.mp4
    elliptical-edit.mp4
    cross-cutting.mp4
    eye-trace.mp4
    split-edit.mp4
    intellectual-montage.mp4
    insert-shot.mp4
```

Everything is 854 pixels wide. The eleven reference clips are about 17 MB. The film is a further 37 MB and is optional, see Film source below.

## Film source

The film can play two ways, set in the `CONFIG` block at the top of the script in `index.html` and switchable from the dashboard under **Film source**.

- `FILM_SOURCE: "youtube"` streams from `FILM_YOUTUBE_ID`. Keeps the repository small. Needs an open connection, and the arrow keys land within a few frames rather than exactly one, because streaming players seek to the nearest keyframe.
- `FILM_SOURCE: "file"` plays `assets/videos/night-shift.mp4` from the repository. Works on a locked down lab network, and the arrow keys step exactly one frame at 24 fps, which is what the cut on action questions were built for.

`FILM_OFFSET` corrects a copy that runs early or late. The bundled file needs `0`. Any other copy must be checked: play question 1 and confirm the first cut of the film lands at **01:18.9** on the timecode readout. If it does not, put the difference in seconds into the offset and every question and ribbon mark shifts together. Do this once before a class uses it, because if the copy is misaligned every single answer will look wrong for reasons that have nothing to do with editing.

The dashboard panel accepts a full link or a bare video ID, applies the change on your computer so you can check the alignment straight away, and then hands you the exact `CONFIG` lines to paste into `index.html`. Until those lines are in the published file, students are still watching whatever the file says.

If you settle on streaming, delete `assets/videos/night-shift.mp4` and the repository drops to about 17 MB.

## How students use it

1. Enter name and HCT ID. Both are required before the activity opens.
2. Read the technique reference. Eleven cards, each with a clip that defines the technique.
3. Work through the fifteen questions. Each one plays its own passage and stops on its own.
4. Replay any clip as often as needed, before or after answering.
5. Submit an answer. It locks. The correct answer, the reasoning, and the matching reference clip appear.
6. After the fifteenth answer the activity moves to Part 2 on its own. Work through the ten quiz questions.
7. The Results view shows both parts and the total out of 25. Export the CSV if you have been asked to hand one in, then press **Finish and return to start**, which clears the screen for the next student. The work is recorded either way.

## Instructor dashboard

Open **Instructor sign in**, on the entry screen and again in the small print at the bottom of the activity, or go straight to `index.html#instructor`. The PIN is set in the `CONFIG` block at the top of the script in `index.html`.

The dashboard shows every attempt as it happens: student name, HCT ID, which attempt this is for that ID, start time, last activity, the Part 1 score with how many questions are done, the Part 2 score with how many are done, the total out of 25, and whether the attempt is finished. An attempt only counts as finished when both parts are submitted. Click a row to read that student's fifteen answers with their written explanations, followed by every Part 2 answer. A difficulty panel on the right shows the percentage correct for each question across the class, grouped into Part 1 and Part 2, so you can see at a glance whether the group is failing on recognition in the film or on the lecture content. The table refreshes every twenty seconds while it is open. Export all as CSV gives one row per student per item across both parts, ready for a gradebook.

Records are written as the student works, not only at the end, so an abandoned attempt still shows what was completed.

Each row has a delete button at the right for removing a single attempt, useful when a student restarts after a false start or a test run needs clearing. Clear all attempts empties the whole record. Both ask for confirmation and neither can be undone.

The PIN protects against a student wandering in. It is not real security, since anyone can read it in the page source. Do not treat the dashboard as confidential.

### Collecting a whole class with Firebase

Out of the box the dashboard runs in device only mode: attempts are stored in the browser, so you see the attempts made on that one computer. That is fine for a single lab machine and needs no setup. To gather a whole class onto one dashboard, connect a Firebase Realtime Database. It takes about two minutes and needs no code beyond one line.

Everything below is also built into the dashboard itself, under **Class database connection**, including a live test.

1. Open the Firebase console and create a project. Analytics can be skipped.
2. Go to **Build, Realtime Database, Create Database**. Pick the region closest to you.
3. Copy the URL shown at the top of the database. It looks like `https://your-project-default-rtdb.firebaseio.com`, or with a region in it such as `https://your-project-default-rtdb.europe-west1.firebasedatabase.app`.
4. Paste it into the dashboard's **Realtime Database URL** box and press **Test connection**. The app writes a test record, reads it back, deletes it, and tells you exactly what failed if anything did.
5. Open the **Rules** tab in Firebase, replace the contents with the block below, and press Publish.

```json
{
  "rules": {
    "attempts": {
      ".read": true,
      ".write": true
    }
  }
}
```

Do not leave the database on the default test mode rules. Those expire after thirty days, after which every save fails silently and falls back to the device.

6. Press **Use on this computer** to start collecting straight away, then copy the `FIREBASE_DB_URL` line the panel shows you into the `CONFIG` block at the top of the script in `index.html`, and push that to GitHub.

Step 6 matters. The value in `CONFIG` is the one every student's browser loads, so until it is in the file and published, only your own machine is writing to the shared database. The dashboard says so plainly while that is the case.

The page talks to the database over the REST API, so no Firebase SDK is loaded and `index.html` stays self contained. Attempts are written to `/attempts/<attemptId>`, one record per attempt.

### What the open rules mean

Those rules let anyone who knows the URL read, write and delete the attempts node. That is the trade for having no student sign in. This app stores only names, HCT IDs, answers and scores, so keep it to that and clear the node at the end of each semester. The rules cover the attempts node alone, so nothing else you put in that database is exposed. If the class list is sensitive in your context, keep the repository private so the URL is not published with it.

If Firebase is unreachable at any moment, the attempt still saves to the device and the save indicator in the top bar says which happened, so no student loses work.

## Marking

The CSV carries the student name, HCT ID, timestamp, both part scores and the total, every answer, whether it was correct, and every written justification.

The two parts test different things and are worth reading separately. Part 2 is lecture recall, which rewards attendance and revision. Part 1 is application against real footage, which is harder, and the written justification is where you see whether they actually understand what carries across a cut. A student who scores 9 on Part 2 and 5 on Part 1 has done the reading and cannot yet use it, which is a different intervention from a student who fails both. The automatic score covers identification and multiple choice only, so weight the justification column accordingly in your rubric.

### Editing the quiz

The ten questions live in the `QUIZ` array near the top of the script in `index.html`. Each entry has the prompt, four options, the index of the correct one counting from zero, and the explanation shown after submission. Add or remove entries freely, everything downstream counts the array rather than assuming ten.

Three of the eleven techniques never appear in the film and are marked as such on their reference cards. Ruling a technique out for a stated reason should earn the same credit as naming one.

## Replacing the clips

Each reference card looks for `assets/videos/<id>.mp4`, where the id is set in the `TECHNIQUES` array near the top of the script block in `index.html`. Drop in a replacement file with the same name and it appears with no other changes.
