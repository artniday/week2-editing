# Cut Detective

A continuity editing lab for Applied Media students. Students watch fifteen marked edits in a short film, name the technique at each one, and write a one sentence justification. The activity self marks and exports a CSV for submission.

## Deploy

1. Create a repository and upload the contents of this folder, keeping the structure below.
2. Settings, Pages, Build and deployment, Source: Deploy from a branch. Pick `main` and `/ (root)`.
3. Open the published URL. No build step, no dependencies to install.

## Structure

```
index.html                  the whole activity, single file
assets/
  videos/
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

Clips are 854 pixels wide, roughly 17 MB in total, well inside GitHub file and repository limits. The feature film itself is not stored here. It streams from its published video URL, so nothing large needs hosting.

## Instructor settings

Open **Instructor setup** at the bottom of the Activity view.

- **Film video ID.** The eleven character ID of the hosted film. Change it if the link ever moves.
- **Timecode offset.** All fifteen questions are timed against the 11:36 release at 24 fps. Play question 1 and confirm the first cut lands at 01:18.9 on the readout. If the hosted copy runs early or late, enter a correction in seconds and press Apply. The offset shifts every question and every ribbon mark together.

## How students use it

1. Enter name and HCT ID. Both are required before the activity opens.
2. Read the technique reference. Eleven cards, each with a clip that defines the technique.
3. Work through the fifteen questions. Each one plays its own passage and stops on its own.
4. Replay any clip as often as needed, before or after answering.
5. Submit an answer. It locks. The correct answer, the reasoning, and the matching reference clip appear.
6. Export the CSV from the Results view if you have been asked to hand one in. The work is recorded automatically either way.

## Instructor dashboard

Open **Instructor sign in**, on the entry screen and again in the small print at the bottom of the activity, or go straight to `index.html#instructor`. The PIN is set in the `CONFIG` block at the top of the script in `index.html`.

The dashboard shows every attempt as it happens: student name, HCT ID, which attempt this is for that ID, start time, last activity, how many of the fifteen are answered, running score, and whether the attempt is finished. Click a row to read that student's fifteen answers with their written explanations. A question difficulty panel on the right shows the percentage correct for each question across the whole class, so you can see at a glance which edit the group is not reading correctly. The table refreshes every twenty seconds while it is open. Export all as CSV gives one row per student per question, ready for a gradebook.

Records are written as the student works, not only at the end, so an abandoned attempt still shows what was completed.

The PIN protects against a student wandering in. It is not real security, since anyone can read it in the page source. Do not treat the dashboard as confidential.

### Collecting a whole class

Out of the box the dashboard runs in device only mode: attempts are stored in the browser, so you see the attempts made on that one computer. That is fine for a single lab machine, and it needs no setup.

To collect every student onto one dashboard, create a Firebase Realtime Database and paste its URL into `CONFIG.FIREBASE_DB_URL` at the top of the script in `index.html`, with no trailing slash:

```js
FIREBASE_DB_URL: "https://your-project-default-rtdb.firebaseio.com"
```

The page writes each attempt to `/attempts/<attemptId>` over the REST API, so no Firebase SDK is loaded and the file stays self contained. Rules need to allow read and write on `attempts` for the activity to work without student sign in, which also means the data is not private. Keep it to names, IDs and scores, which is all this app stores, and clear the node between semesters with the Clear all attempts button.

If Firebase is unreachable at any moment, the attempt still saves to the device and the save indicator in the top bar says so, so no student loses work.

## Marking

The CSV carries the student name, HCT ID, timestamp, every answer, whether it was correct, and every written justification. The automatic score covers identification only. The justification column is where you mark understanding, so weight it accordingly in your rubric.

Three of the eleven techniques never appear in the film and are marked as such on their reference cards. Ruling a technique out for a stated reason should earn the same credit as naming one.

## Replacing the clips

Each reference card looks for `assets/videos/<id>.mp4`, where the id is set in the `TECHNIQUES` array near the top of the script block in `index.html`. Drop in a replacement file with the same name and it appears with no other changes.
