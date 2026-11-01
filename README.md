# Shopping Game (v3)

A tablet task that measures how young children balance their own memory against an external memory aid. Developed by the Shalev Lab (Lifespan Cognitive Development) for children aged 5 to 6 years.

The whole task lives in one file, `index.html`. It needs no server, no build step and no external libraries. Images load from a `stimuli/` folder next to the file.

## Contents

1. [Overview and background](#1-overview-and-background)
2. [Quick start for the experimenter](#2-quick-start-for-the-experimenter)
3. [Repository layout and stimuli](#3-repository-layout-and-stimuli)
4. [How a session runs](#4-how-a-session-runs)
5. [Task mechanics](#5-task-mechanics)
6. [Parameters](#6-parameters)
7. [Unreliable schedules in detail](#7-unreliable-schedules-in-detail)
8. [Designs, block order and trial numbering](#8-designs-block-order-and-trial-numbering)
9. [Data output](#9-data-output)
10. [Cloud saving](#10-cloud-saving)
11. [Timing measures: list time, search time and effort time](#11-timing-measures-list-time-search-time-and-effort-time)
12. [Known quirks of the logs](#12-known-quirks-of-the-logs)
13. [Troubleshooting](#13-troubleshooting)
14. [What changed in this version](#14-what-changed-in-this-version)
15. [References](#15-references)

---

## 1. Overview and background

The child plays a shopping game. A shopping list shows a few pictures. A store shows a grid of pictures. The list and the store never share the screen. The child taps a button to switch between them and picks the store pictures that match the list.

The experiment manipulates how reliably the child can reach the list.

| Condition | Icon and look | Meaning for the child |
|---|---|---|
| Reliable | Brown clipboard | The list is always there when the child checks it. |
| Unreliable | Black tablet | The list may be costly to open, blank, or gone. |

The two conditions differ in the list frame and in how the child can reach the list. The store, the scoring and the trial structure stay identical.

The task extends Liang, Kaldy and Blaser (2025). In that study, 5- to 6-year-old children who saw the list as reliably available checked it more often, for shorter visits, and remembered fewer items. The present version keeps the logic and adds several ways to make the list unreliable (section 7). It also replaces the original pictures with novel objects from the NOUN database, which children rarely name.

The main behavioural measures are the number of list visits, the time spent on the list, the time spent searching the store, and the accuracy of the picks.

---

## 2. Quick start for the experimenter

1. Open the GitHub Pages address of this repository on the tablet. Hold the tablet in landscape.
2. Read the cloud status line at the bottom of the configuration screen. It should say that cloud saving is on.
3. Tap **Test cloud save**. A green message names the Drive folder when the connection works.
4. Check the participant fields. The ID fills in automatically. Enter the session number and the child's age in years.
5. Review the parameters (section 6). The defaults suit the standard protocol.
6. Tap **Start Experiment**. The page loads the images and shows the first block screen.
7. Hand the tablet to the child. The block screen explains the game in child-friendly words.
8. After each practice trial, press **Next** for another practice trial. Press **Ready to begin the experiment** when the child is ready for the real trials.
9. At the end, check the green "Saved to cloud" line on the final screen. The CSV files also download to the tablet.
10. Tap **New Session** for the next child. The ID refreshes and the other settings stay.

### Experimenter controls

| Control | Where | What it does |
|---|---|---|
| Test cloud save | Configuration screen | Sends a test message to the cloud script and reports the Drive folder. |
| Load last config | Configuration screen | Restores the settings saved at the last start (not the participant ID or notes). |
| Start Experiment | Configuration screen | Reads the form, validates it, loads images and begins. |
| End Session | Top bar during play | Asks for confirmation, saves everything so far, downloads the CSVs and returns to the configuration screen. |
| Done | Top bar during play | Ends the current trial at once. The task logs the trial with the end reason `cart`. |
| Ready to begin the experiment | After each practice trial | Skips the remaining practice and moves to the test trials. Appears only when endless practice is on. |
| Download CSVs | Final screen | Downloads another copy of the session files. |
| Retry cloud save | Final screen | Appears after a failed upload. Sends the queued data again. |
| New Session | Final screen | Returns to the configuration screen with a fresh participant ID. |

The top bar also shows a small line with the trial label, the condition, the schedule and the array size. The child can see that line.

---

## 3. Repository layout and stimuli

```
index.html     the whole task
stimuli/       the pictures (1.jpg, 2.jpg, ...)
README.md      this file
```

### Stimulus rules

* Put the pictures in `stimuli/`, or set another folder in the configuration screen.
* The task detects the naming pattern from the first picture. It tries these patterns in order: `1.jpg`, `1.png`, `01.jpg`, `001.jpg`, `noun01.jpg`, `Object1.jpg`.
* The task then uses only the first pattern that matches. Use one pattern per folder.
* If index 1 does not exist, the task probes indices 2 to 5 before it gives up.
* The task reads indices 1 to 80 in blocks of ten (1 to 10, 11 to 20 and so on). It stops when a whole block holds no picture. Leave no empty block.
* The folder needs at least as many pictures as the largest search array. The default array has 10 items.
* Every trial draws a fresh random set of unique pictures from the whole folder. The draw ignores condition.
* The NOUN pictures belong to their authors. Follow the database terms when you share the folder.

---

## 4. How a session runs

```
Configuration screen
   |
   v
Block screen   (names the game and explains it)
   |
   v
Trial screen   ("Trial 1 (Practice)" with the clipboard or tablet icon)
   |
   v
Store  <---->  List         (the child toggles as often as they like)
   |
   v
Trial ends when all targets are found, or Done is pressed
   |
   v
Feedback screen  (Next, and Ready to begin during practice)
   |
   v
More trials ... then optional metacognition questions ... then the final screen
```

The blocked design shows a block screen for each condition. The mixed design shows a "Practice" screen and a "Real Game" screen. Section 8 describes both.

The metacognition questions appear after the last trial when the option is on and both conditions ran. The child answers two questions by tapping the clipboard or the tablet icon:

1. Which game did you think was easier?
2. Which game would you play again to earn more stars?

The left-right order of the two icons is random for each question.

---

## 5. Task mechanics

### What the child sees

* **Store screen.** A grid of pictures and a **See my list** button with the condition icon.
* **List screen.** The list pictures in a clipboard or tablet frame and a **Back to store** button.
* **Top bar.** The star count, the trial label, the **End Session** button and the **Done** button.

### Picking items

* The list holds the target pictures. The store holds the targets plus distractors.
* Tapping a target scores a correct pick. The picture fades and the child cannot tap it again. A rising two-tone sound plays. The star count rises by one.
* Tapping a distractor scores an incorrect pick. The picture stays in place and stays tappable. A low buzz plays. The star count drops by one but never below zero.
* A second tap on a distractor logs as a repeated error.
* Targets that the child has found appear crossed out on later list visits.
* The star count resets at the start of every trial.

### How a trial ends

| End reason | Trigger |
|---|---|
| `completed` | The number of correct picks reaches **Targets to find per trial**. |
| `cart` | Someone presses **Done**. |
| `terminated` | Someone confirms **End Session** during a trial. |

After a trial, the child sees a random compliment, a short chime and the message that the items are found. The same message appears whatever ended the trial.

### Choosing the list items

At the start of each trial the task picks the list items at random from the store items. The number of list items equals **Items on shopping list**. The list stays the same for the whole trial.

### Store grid layout

The store grid adapts to the array size. Positions count from 0, row by row, starting at the top left.

| Array size | Rows x columns |
|---|---|
| 10 | 2 x 5 |
| 12 | 3 x 4 |
| 15 | 3 x 5 |
| 16 | 4 x 4 |
| 18 | 3 x 6 |
| 20 | 4 x 5 |
| 21 | 3 x 7 |
| 24 | 4 x 6 |
| Any other size | 3 rows up to 21 items and 4 rows above. Columns equal the size divided by the rows, rounded up. Spare cells stay empty and hidden. |

The list grid follows the list size. Sizes 2 and 3 use one row. Size 4 uses 2 x 2. Sizes 5 and 6 use 2 x 3. Sizes 7 and 8 use 2 x 4.

### Shuffling

* **List shuffle.** When the setting is N, the list order changes on every Nth return visit, counted after the first visit. A value of 0 never shuffles.
* **Store shuffle.** When the setting is N, the store changes on every Nth return from the list. The task replaces the unpicked distractors with fresh pictures and moves every picture to a new position. The targets stay in the store but change position. Pictures that the child already found stay faded.

### Opacity jitter

The jitter applies only to the unreliable condition and only when the list is visible. Each uncrossed list picture gets a random opacity between 15% and 100% plus a random noise overlay. The task rolls new values at every visit. The jitter is off by default.

---

## 6. Parameters

All parameters sit on the configuration screen. The **JSON key** column gives the name of each setting in the saved configuration file. Defaults reflect this version.

### Participant

| Setting | JSON key | Default | Effect |
|---|---|---|---|
| Participant ID | `participantId` | Generated from the clock, starting with `P` | Names the files and drives counterbalancing (section 8). |
| Session number | `session` | 1 | Names the files and appears in every log row. |
| Age (years) | `age` | Empty | Written to the summary file. Does not change the task. |
| Notes | `notes` | Empty | Free text in the configuration file. Cloud uploads leave it out unless the code sets `includeNotes` to `true`. |

### Task parameters

| Setting | JSON key | Default | Range | Effect |
|---|---|---|---|---|
| Items on shopping list | `listSize` | 4 | 2 to 8 | Number of pictures on the list. |
| Search array size | `arrayMode` | Fixed | Fixed or Variable | Fixed uses one store size for every trial. Variable picks the low or the high size at random for each trial, with equal chances and no balancing. |
| Array size (fixed) | `arraySize` | 10 | 8 to 24 | Number of pictures in the store in Fixed mode. |
| Low load (variable) | `arrayLow` | 12 | 12 to 24 | Smaller store size in Variable mode. |
| High load (variable) | `arrayHigh` | 24 | 12 to 24 | Larger store size in Variable mode. |
| Targets to find per trial | `targetsPerTrial` | 4 | 1 to 8 | Number of correct picks that ends the trial. With a value below the list size, the trial ends before the child finds every list item. |
| Shuffle list every N visits | `listShuffleN` | 0 | 0 to 20 | See "Shuffling" in section 5. |
| Shuffle store every N visits | `storeShuffleN` | 0 | 0 to 20 | See "Shuffling" in section 5. |
| Keep same item set across trials within a block | `stableItemsAcrossTrials` | Off | On or off | When on, every trial of a block reuses one set of pictures and one layout. Targets still change from trial to trial. Works only with the blocked design and Fixed array size. Practice and test trials of one block share the set. |

### Conditions

| Setting | JSON key | Default | Range | Effect |
|---|---|---|---|---|
| Include RELIABLE condition | `includeReliable` | On | On or off | Adds the clipboard trials. |
| Include UNRELIABLE condition | `includeUnreliable` | On | On or off | Adds the tablet trials. |
| Design | `design` | Blocked | Blocked or Mixed | Section 8. |
| Block order (if blocked) | `blockOrderPref` | Counterbalanced | Counterbalanced, Reliable first, Unreliable first | Section 8. |
| Reliable: # test trials | `reliableTestTrials` | 12 | 1 to 20 | Scored clipboard trials. |
| Reliable: # practice | `reliablePractice` | 1 | 0 to 5 | Clipboard practice trials. |
| Unreliable: # test trials | `unreliableTrials` | 12 | 1 to 30 | Scored tablet trials. |
| Unreliable: # practice | `unreliablePractice` | 1 | 0 to 5 | Tablet practice trials. Practice trials use the same schedule settings as the test trials. |
| Unreliable schedule | `schedule` | Effort | Effort, Countdown, Permanent, Every-Nth, Outage | Defines how the tablet list misbehaves. Section 7. |
| Total taps required | `effortTaps` | 2 | 2 to 6 | Effort schedule only. An empty field falls back to 3 in the code. |
| Delay between buttons (ms) | `effortDelayMs` | 1000 | 200 to 3000 | Effort schedule only. |
| Countdown (seconds) | `countdownSec` | 3 | 1 to 60 | Countdown schedule only. |
| Disappear after N visits (permanent) | `permanentN` | 2 | 1, 2 or Mixed | Permanent schedule only. |
| Blank every N visits | `everyNthN` | 2 | 2 to 10 | Every-Nth schedule only. |
| Disappear after N visits (outage) | `outageDisappear` | 1 | 1 to 10 | Outage schedule only. |
| Return after M more trips | `outageReturn` | 2 | 1 to 10 | Outage schedule only. |
| List opacity jitter (unreliable) | `opacityJitter` | Off | On or off | Section 5. |

### Other

| Setting | JSON key | Default | Effect |
|---|---|---|---|
| Stimulus folder | `stimDir` | `stimuli/` | Folder with the pictures, relative to `index.html`. |
| Endless practice | `endlessPractice` | On | A practice trial repeats until the experimenter presses **Ready to begin the experiment**. The number of completed practice trials goes to the configuration file as `practiceAttempts`. |
| Show metacognition questions at end | `showMeta` | Off | Asks the two questions in section 4. Needs both conditions. |
| Try to enter fullscreen on Start | `tryFullscreen` | On | Requests fullscreen and a landscape lock where the browser allows them. |

### Checks at start

The task refuses to start and shows a message in these cases:

* The form selects neither condition.
* Targets per trial exceed the list size.
* The list size exceeds the fixed array size, or the low load in Variable mode.
* The stimulus folder holds no images.
* The folder holds fewer images than the largest array size.

Fill in every numeric field before you start.

### Saved settings

The task stores the settings in the browser at every start under the key `shoppingGameConfig_v3`. **Load last config** reads them back. The participant ID and the notes are never saved. Each tablet keeps its own copy, so a tablet that ran an older version can show older values after **Load last config**.

---

## 7. Unreliable schedules in detail

Reliable trials always show the list. Unreliable trials follow one schedule for the whole session. In every schedule a **visit** is one trip to the list screen. Visits count from 1 within each trial.

| Schedule | What the child experiences | Parameters |
|---|---|---|
| Effort | The child taps **See my list**. The button disappears. A **See my list** button then pops up in a random bottom corner after a delay. Each tap triggers the next button. The list opens after the last tap. | Total taps (counts the first tap), delay between buttons |
| Countdown | After the child returns to the store, the list button locks and shows a timer. A tap during the lock flashes the remaining seconds and opens nothing. | Countdown seconds |
| Permanent | The list is visible on visits 1 to N. It stays blank from visit N + 1 onward. | N is 1, 2, or Mixed |
| Every-Nth | The list is visible on visits 1, N + 1, 2N + 1 and so on. It is blank on the visits in between. | N |
| Outage | The list is visible on visits 1 to D. It is blank on the next M visits. It is visible again afterwards. | D (disappear after) and M (return after) |

Further details:

* **Effort.** The first tap counts as tap 1. With the default of 2 taps, the child taps once on the normal button and once on one popped-up button. The task logs every sequence as an `effort_sequence` row. The list is always visible once it opens.
* **Countdown.** The first visit of a trial is never delayed. The timer starts when the child returns to the store. The task logs every blocked tap as a `denied_access` row. The list is always visible once it opens.
* **Permanent.** This schedule matches the original paper. With the Mixed setting, the trials alternate between N = 1 and N = 2, and the test trials are then shuffled.
* **Blank visits.** A blank visit shows an empty frame. The visit still counts, and the child still leaves through **Back to store**.
* **Outage.** One outage happens per trial.
* **Child instructions.** The block screen text changes with the schedule. The text lives in the function `showBlockIntro`.

---

## 8. Designs, block order and trial numbering

### Blocked design

Each condition runs as its own block. A block holds that condition's practice trials, then its test trials. The task shuffles the test trials of a block.

```
Block screen -> practice trials -> test trials
Block screen -> practice trials -> test trials
```

### Mixed design

The task pools the practice trials of both conditions and shuffles them. It then pools the test trials of both conditions and shuffles them. The icon at the start of each trial shows the condition.

```
Practice screen  -> pooled practice trials
Real Game screen -> pooled test trials
```

### Block order

* **Reliable first** and **Unreliable first** fix the order.
* **Counterbalanced** reads the last character of the participant ID. An ID that ends in 0, 2, 4, 6 or 8 runs reliable first. An ID that ends in 1, 3, 5, 7 or 9 runs unreliable first. A final letter follows the parity of its character code.
* Block order applies only to the blocked design with both conditions.

### Session length with the defaults

Each condition has 12 test trials, so a session holds 24 test trials plus the practice attempts that the experimenter allows.

### Trial numbering

Two numbers exist for every trial.

| Number | Where it appears | Behaviour |
|---|---|---|
| Displayed trial number | Trial screen and top bar | Restarts at 1 when practice ends and at each block screen. Practice reads "Trial 1 (Practice)". The first test trial reads "Trial 1". |
| `trialId` | Every log file | Runs without a break across the whole session. It identifies the planned trial. |

The displayed number does not appear in the files. Use `trialId` and the timestamps to match the screen to the data.

With endless practice, one practice slot repeats. Every repeat keeps the same `trialId`. The summary file holds one row per attempt, and `trialStartUTC` tells the attempts apart.

---

## 9. Data output

### Files per session

| File | One row per | Written when |
|---|---|---|
| `..._encoding.csv` | List visit, effort sequence or denied tap | At least one such event happened |
| `..._shopping.csv` | Pick | At least one pick happened |
| `..._summary.csv` | Finished trial attempt | At least one trial finished |
| `..._config.json` | Session | Always |

The three CSV files use UTF-8 with a header row. The task quotes any cell that holds a comma, a quote or a line break. Empty cells mean "not applicable". Booleans appear as `true` and `false`.

### File names

Local downloads use this pattern:

```
<participantId>_S<session>_<UTC date-time>[_<STATUS>]_<file>
```

The date-time reads `YYYY-MM-DD-HH-MM-SS` in UTC. The status suffix is `COMPLETE` after a normal finish and `TERMINATED` after **End Session**. The **Download CSVs** button adds no suffix. Cloud copies use the session key as the base name. The key follows the same pattern without a status.

### When files are written

* After every finished trial, the task uploads a snapshot to the cloud with the status `IN_PROGRESS`.
* At the end of the session, the task downloads the files and uploads a final snapshot with the status `COMPLETE`.
* After **End Session**, the task downloads the files and uploads a snapshot with the status `TERMINATED`.
* A trial that is still running when the tablet dies is lost. Finished trials survive in the last snapshot that reached the cloud.

### Time fields

* Fields ending in `MsInTrial` or `InTrial` count milliseconds from the start of the trial.
* `timestampAbsoluteMs` counts milliseconds from the start of the session.
* The summary file gives trial start and end as UTC date-times.

### 9.1 `encoding.csv`: list visits, effort sequences and denied taps

The file mixes three row types. The column `trialType` names the type. The column `eventType` repeats it. Column order in the file can vary. Cells that do not apply to a row type stay empty.

| Row type | Created when |
|---|---|
| `list_visit` | The child leaves the list screen. One row per visit, blank visits included. |
| `effort_sequence` | The child finishes the tap sequence of the effort schedule. |
| `denied_access` | The child taps the list button during a countdown lock. |

| Column | Row types | Meaning |
|---|---|---|
| `participantId`, `session` | All | Copied from the configuration screen. |
| `trialId` | All | Planned trial number, unbroken across the session. |
| `condition` | All | `reliable` or `unreliable`. |
| `trialType`, `eventType` | All | Row type, as in the table above. |
| `isPractice` | All | `true` for practice trials. |
| `arraySize`, `listSize` | All | Store size and list size of the trial. |
| `listShuffleN`, `storeShuffleN` | All | Session settings for shuffling. |
| `scheduleUsed` | All | `reliable` on clipboard trials, otherwise the schedule name. |
| `tripIndex` | All | Visit number within the trial, starting at 1. For `effort_sequence`, the number of the visit that the sequence opens. For `denied_access`, always -1. |
| `startMsInTrial`, `endMsInTrial` | All | List visit: when the list opened and closed. Effort sequence: first and last tap. Denied tap: both equal the time of the tap. |
| `dwellMs` | `list_visit` | Time the list screen stayed open. Zero on other rows. |
| `wallClockMs` | `list_visit`, `effort_sequence` | List visit: equals `dwellMs`. Effort sequence: duration of the tap sequence. Zero on denied rows. |
| `activeTimeMs` | `list_visit` | Equals `dwellMs` in this version. Zero on other rows. |
| `listVisible` | All | `false` on a blank visit. Always `false` on effort and denied rows. |
| `listWasShuffledThisVisit` | All | `true` when the task shuffled the list order for this visit. |
| `storeWasShuffledOnPriorReturn` | All | `true` when the task reshuffled the store at the previous return from the list. The store in view before this visit then had a new layout. Always `false` on effort rows. |
| `opacityJitterMean` | `list_visit`, `effort_sequence` | Mean opacity of the list items shown. The value is 1 when jitter is off. Always 1 on effort rows. |
| `opacityJitter` | `denied_access` | Constant 1. |
| `opacityJitterPerItem` | `list_visit`, `effort_sequence` | JSON list of `{itemIdx, opacity}` for the list items shown. `[]` on effort rows. |
| `picksInPriorVisit` | All | Number of picks, correct and incorrect, in the store episode before this event. |
| `countdownRemainingSec` | `denied_access` | Whole seconds left on the timer at the tap. |
| `effortTapsCompleted` | `effort_sequence` | Number of taps in the sequence. |
| `effortTapLog` | `effort_sequence` | JSON list of `{tapIndex, position, timestampMs}`. The first tap has position `center`. Later taps read `left` or `right`. `timestampMs` counts from trial start. |
| `timestampAbsoluteMs` | All | List visit: when the list opened. Effort sequence: first tap. Denied tap: time of the tap. |

### 9.2 `shopping.csv`: picks

One row per tap on a store picture.

| Column | Meaning |
|---|---|
| `participantId`, `session`, `trialId` | As in the encoding file. |
| `condition`, `schedule`, `isPractice` | Condition, schedule name and practice flag of the trial. |
| `arraySize`, `listSize` | Store size and list size. |
| `pickIndexInTrial` | Order of the pick within the trial, starting at 1. |
| `pickIndexInVisit` | Order of the pick within the current store episode, starting at 1. |
| `tripsBeforePick` | Number of list visits that the child had started before this pick. |
| `itemId`, `filename` | The picture tapped. |
| `positionInGrid` | Grid position of the picture at the time of the pick. Counts from 0, row by row. |
| `isTarget`, `correct` | `true` when the picture is on the list. Both columns always agree. |
| `repeated` | `true` when the child tapped the same picture before in this trial. |
| `clickNumberOnItem` | How many times the child has tapped this picture, this tap included. |
| `rtMs` | Time since the latest of three events: the start of the trial, the return to the store, or the previous pick. |
| `timestampInTrial` | Milliseconds from trial start. |
| `timestampAbsoluteMs` | Milliseconds from session start. |

### 9.3 `summary.csv`: one row per trial attempt

The file has one row for every trial that ended. A trial ended by **Done** or **End Session** gets a row too.

| Column | Meaning |
|---|---|
| `participantId`, `session`, `age` | Participant fields. |
| `trialId` | Planned trial number. Repeated practice attempts share it. |
| `condition` | `reliable` or `unreliable`. |
| `schedule` | `reliable` or the schedule name. |
| `disappearAfter` | Permanent schedule: visits before the list vanishes. Empty otherwise. |
| `blankEvery` | Every-Nth schedule: the value of N. Empty otherwise. |
| `outageDisappear`, `outageReturn` | Outage schedule: D and M. Empty otherwise. |
| `countdownSec` | Countdown schedule: lock length. Empty otherwise. |
| `isPractice` | `true` for practice. |
| `design` | `blocked` or `mixed`. |
| `arraySize`, `listSize`, `targetsPerTrial` | Trial settings. |
| `listShuffleN`, `storeShuffleN` | Shuffle settings. |
| `nTrips` | Number of completed list visits. |
| `nDeniedAttempts` | Number of taps blocked by the countdown lock. |
| `totalDwellMs` | Sum of the time on the list screen over all visits. |
| `totalWallClockMs` | Equals `totalDwellMs` in this version. |
| `totalActiveMs` | Equals `totalDwellMs` in this version. |
| `meanDwellMs` | `totalDwellMs` divided by `nTrips`, rounded. |
| `nPicks` | All picks, including incorrect and repeated ones. |
| `nCorrect`, `nIncorrect` | Counts of correct and incorrect picks. |
| `totalCorrectPicks` | Correct picks at the end of the trial. |
| `totalStars` | Star count at the end of the trial. |
| `accuracy` | `nCorrect` divided by `nPicks`, to three decimals. |
| `meanRtMs` | Mean `rtMs` over all picks, rounded. |
| `meanStreakCorrect` | Mean length of the opening run of correct picks in each store episode that held a pick. A run stops at the first incorrect pick. Two decimals. |
| `meanItemsPickedPerVisit` | `nPicks` divided by `nTrips`. Equals `nPicks` when the child never opened the list. Two decimals. |
| `durationMs` | Trial length from start to end. |
| `endReason` | `completed`, `cart` or `terminated`. |
| `trialStartUTC`, `trialEndUTC` | UTC date-times in ISO format. |
| `easierResponse`, `preferredResponse` | Answers to the metacognition questions (`reliable` or `unreliable`). The same values repeat on every row of the session. Empty when the questions did not run. |
| `totalSearchMs` | Time in the store view, without list visits and without effort tap time. Section 11 gives the exact rule. |
| `meanSearchMsPerVisit` | `totalSearchMs` divided by the number of store episodes, rounded. |
| `meanRtCorrectMs` | Mean `rtMs` over correct picks only, rounded. |
| `totalEffortMs` | Time spent on effort tap sequences. Zero outside the effort schedule. |

The four columns from `totalSearchMs` onward sit at the end of the file. Earlier columns keep their names and positions.

### 9.4 `config.json`

The file records every setting and the course of the session.

| Key | Content |
|---|---|
| `config` | All settings from section 6 under their JSON keys, plus participant ID, session, age and notes. Cloud copies omit `notes` by default. |
| `blockOrder` | Conditions in the order they ran. |
| `metaResponses` | The answers `easier` and `preferred`, when asked. |
| `sessionStartUTC` | Session start as a UTC date-time. |
| `sessionStatus` | `IN_PROGRESS`, `COMPLETE`, `TERMINATED` or `MANUAL_DOWNLOAD`. |
| `trialPlanSummary` | Number of planned trials, practice and test. Repeated practice attempts do not count. |
| `practiceAttempts` | Number of practice trials that the child completed, repeats included, both blocks together. |
| `sessionKey` | Session identifier. Present in cloud copies only. |

---

## 10. Cloud saving

Cloud saving sends each session to a Google Drive folder through a Google Apps Script web app. The script project (`Code.gs`) decides the folder name and the file handling. Check it for the exact destination.

### Settings in `index.html`

The constant `CLOUD` near the end of the script holds the settings.

| Field | Meaning |
|---|---|
| `url` | Web app address from Apps Script. It ends in `/exec`. |
| `key` | Shared key. It must equal `SHARED_KEY` in `Code.gs`. |
| `includeNotes` | `false` keeps the free-text notes out of uploads. |
| `timeoutMs` | Wait before an upload counts as failed. The value is 20000. |
| `queueKey` | Browser storage key for the upload queue. |

Leave `url` and `key` empty to switch cloud saving off. The task then saves locally only.

### What the browser sends

Each upload is one POST request with a JSON body. The body holds `key`, `sessionKey`, `status`, `clientTimeUTC` and a `files` list of `{name, content}` entries. Every upload carries the whole session so far, so a newer upload replaces an older one for the same session key.

### Behaviour on a poor connection

* The game never waits for an upload. A failed upload cannot stall a trial.
* Failed uploads stay in the browser storage of the tablet. The queue holds only the newest snapshot per session.
* The task retries when the tablet comes back online, when the page loads, when the experimenter returns to the configuration screen, and when someone presses **Retry cloud save**.
* A small dot in the bottom right corner shows the state: grey means idle, amber means saving, green means saved and red means failed.
* The local CSV download at the end of each session is the backup. Treat it as the safe copy until the cloud upload turns green.
* Clearing the site data of the browser deletes the queue.

### Security note

The shared key sits in `index.html`. Anyone who can open the page can read it. Treat the key as a light guard against stray uploads and not as strong protection. Change it in `Code.gs` and in `CLOUD.key` together when needed.

---

## 11. Timing measures: list time, search time and effort time

A trial moves through three kinds of time.

```
trial start | store | [effort taps] | LIST VISIT 1 | store | LIST VISIT 2 | store | trial end
```

| Kind of time | Definition | Summary column |
|---|---|---|
| List time | From the moment the list screen opens until the child taps **Back to store**. Blank visits count. | `totalDwellMs`, `meanDwellMs` |
| Effort time | From the first tap of an effort sequence until the list opens. | `totalEffortMs` |
| Search time | Time in the store view, minus effort time. | `totalSearchMs`, `meanSearchMsPerVisit` |

### Rules for search time

* A **store episode** runs from trial start to the first list visit, between two list visits, or from the last list visit to the end of the trial.
* The task adds up all store episodes. It then subtracts the effort time that fell inside them.
* The first store episode includes the time before the child has seen the list.
* Countdown waits stay inside search time, because the child keeps searching during the lock.
* A list visit that was still open when the trial ended is not a store episode and is not a logged visit. Its time appears in neither list time nor search time. The three kinds of time then add up to less than `durationMs`.
* `meanSearchMsPerVisit` divides by the number of store episodes, which is one more than `nTrips`. It differs from `meanRtMs`, which averages the time per pick.

### Pick times

`rtMs` runs from the child's return to the store (or from the previous pick) to the pick. Search that ends in a list check instead of a pick adds nothing to any `rtMs` value. `totalSearchMs` captures that time.

---

## 12. Known quirks of the logs

The notes below describe how the current code behaves. Keep them in mind when you analyse the data.

1. **Repeated practice attempts share one `trialId`.** The summary file holds one row for each attempt. Separate the attempts with `trialStartUTC`. In the encoding and shopping files, separate them with `timestampAbsoluteMs`.
2. **Blank visits keep stale fields.** On a blank visit the list is not drawn. The columns `listWasShuffledThisVisit`, `opacityJitterMean` and `opacityJitterPerItem` can repeat the values from the previous visit. Ignore those columns where `listVisible` is `false`.
3. **Three timing columns match.** `wallClockMs` and `activeTimeMs` always equal `dwellMs` for list visits. The matching summary columns equal `totalDwellMs`.
4. **Open list visits go unlogged.** A trial that ends on the list screen (by **Done** or **End Session**) never logs the open visit.
5. **Two jitter column names.** List visits and effort rows use `opacityJitterMean`. Denied rows use `opacityJitter`.
6. **Opacity range on screen.** The configuration label for jitter says 50 to 100 percent. The code draws between 15 and 100 percent.
7. **Effort field fallback.** An empty "Total taps required" field falls back to 3 taps.
8. **Summary rows exist only for finished attempts.** A trial still running when the tablet dies has no summary row.
9. **Feedback wording.** The feedback screen always says that the child found all the items, even after **Done**.

---

## 13. Troubleshooting

| What you see | What it means and what to do |
|---|---|
| "No images found" | The stimulus folder is empty, missing or uses an unsupported naming pattern. Check section 3. |
| "Not enough images" | The folder holds fewer pictures than the largest array. Add pictures or lower the array size. |
| "Could only build N/M unique items for this trial" | The picture pool could not fill the store with unique files. Add pictures. |
| "Duplicate image detected in store" | The same file reached the store twice. Open the browser console for the file list. |
| "Select at least one condition." | Both condition boxes are off. |
| "Targets per trial cannot exceed list size." | Lower the targets or raise the list size. |
| "List size exceeds array size" or "exceeds low load" | Raise the array size or lower the list size. |
| Cloud line: "Cloud saving is off" | `CLOUD.url` or `CLOUD.key` is empty in `index.html`. |
| Cloud line: "no connection" or "timed out" | The tablet cannot reach the script. Check the network. The data stays queued. |
| Cloud line: "unexpected reply" | The reply was not valid JSON. Check that the web app is deployed for "Anyone". |
| Cloud line: "server error" followed by a number | The script returned an HTTP error. Open the script's execution log. |
| Cloud line: "upload rejected" or another message from the script | The script refused the upload. Compare `CLOUD.key` with `SHARED_KEY` and read the script log. |
| Old defaults appear after **Load last config** | The tablet saved an older configuration. Start one run with the new values to overwrite it. |
| Old page version appears | Reload the page hard or clear the site data for the page. |

The browser console logs the detected naming pattern, the number of images found and the pictures drawn for each trial.

---

## 14. What changed in this version

* The displayed trial number now restarts at 1 after practice and at each block. The files keep the unbroken `trialId`.
* New defaults: a list of 4 items, a fixed array of 10 and 4 targets. Each condition has 12 test trials and 1 practice trial. The effort schedule needs 2 taps. Jitter and the metacognition questions are off.
* The fixed array size accepts values from 8. A 2 x 5 layout serves the array of 10.
* The summary file gained four columns: `totalSearchMs`, `meanSearchMsPerVisit`, `meanRtCorrectMs` and `totalEffortMs`.
* `nDeniedAttempts` now counts only the denied taps inside the trial's own time window. Repeated practice attempts no longer add up.
* Cloud saving keeps its earlier behaviour.

---

## 15. References

Horst, J. S., & Hout, M. C. (2016). The Novel Object and Unusual Name (NOUN) Database: A collection of novel images for use in experimental research. *Behavior Research Methods, 48*(4), 1393-1409.

Liang, Y., Kaldy, Z., & Blaser, E. (2025). My tablet's about to go dead! 5- to 6-year-old children adjust their cognitive strategies depending on whether an external resource is reliably available. *Cognitive Development, 73*.
