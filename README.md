# su26-ai301-contribution


# Contribution [#]: [Allow UI setting to control MAX FRAME-rate]

**Contribution Number:** [1]  
**Student:** [Anthony Aleman]  
**Issue:** [GitHub issue link] (https://github.com/wesnoth/wesnoth/issues/2210)
**Status:** [Phase I ] [Complete]

---

## Why I Chose This Issue

[During spring 2026 semester I took a class engines on my school. It really did not involve to much working with vigeo games, or as much as I would have expected, but we did work on creating a simple game engine. Although simple, I appreciate the ground work to break into this field. With this project I aim to Explore working on more video games coded in C++./]

---

## Understanding the Issue

### Problem Description

[The game has defaul frame setting that woek with the images in their project. They would like to expand this feature by creating a frame control slider.]

### Expected Behavior

[Create an interface to users to be able to choose the max frame rate for their game]

### Current Behavior

[For now the game only has defaul setting, and a contributor disabled this feature while working on it.]

### Affected Components

[The files of the code base most likely involved are the source code that use C++, these files would be the display, draw manager, frame, animation, and menu event which would be where the toggle option would be added]

---

## Reproduction Process

### Environment Setup

[Notes on setting up your local development environment - challenges you faced, how you solved them]

### Steps to Reproduce

1. [Fork the repo with a recursive flag to replicate all of the files and repos contained in the main]
2. [Download Visual Studio community along with VCPKG to download all of the dependancied of the project]
3. [During this step I found some set up bug, particularly, the package downloader had a hard time finding some files on my computer. It look me a considerable amount of time and debugging with claude to get it working] 
4. [Observed result: The game runs properly and diagnositcs display in visual studio as they should. No bugs by just running the game]

### Reproduction Evidence

- **Commit showing reproduction:** [https://github.com/Ale-A2/wesnoth_ALE_contrib.git]
- **Screenshots/logs:** [If applicable]
- **My findings:** [Reproducing the game is not complicated, actually it is simply by following their guide of installation and having a proper visual studio set up. However, I tried hours on my main computer that is not a properly set up as my lapton and it wasn't able to replicate. I am still not sure why that is the case, but for proper execution Cmake and visual studio should handle all of the dependancies and setting up the environment for the user. Wait time is still long]

---

## Solution Approach

### Analysis

[Currently the game does have an option that modifies the max framerate, however this is not a UI fearture. Users can only control via the --max-fps CLI flag, which makes it difficult for non technical users and is under documented. The preference value already exist in the engine, so the goal is to implement the UI setting slider in the preferences.]

### Proposed Solution

[The Display Preferences panel already has a "limit max frame-rate" toggle that needs to be enabled. The fix is to add a slider beneath it that lets users set the actual FPS value when that toggle is enabled. This exposes the existing --max-fps engine logic through a friendly UI, with no need to rewrite the frame-rate cap mechanism itself.]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [The game doesn't have an enabled max frame rate toggle and it must be added, preferably as an slider.]

**Match:** [Wesnoth has a purpose-built WML system for exactly this. The [advanced_preference] tag defines entries in the Advanced Preferences dialog. For integer types it supports min, max, and step attributes. The field key must match what the game engine requests internally. This is the pattern to follow ]

**Plan:** [Step-by-step implementation plan]
1. [Find the engine's preference key — search src/preferences/preferences.cpp and src/preferences/preferences.hpp for max_fps to find the exact field name the engine reads.]
2. [Add the WML preference entry — in data/hardwired/ (or wherever existing [advanced_preference] entries live, check data/core/), add]
 ```
  [advanced_preference]
       field = "max_fps"
       name = _ "Max Frame Rate"
       description = _ "Maximum frames per second. Lower values reduce battery usage."
       default = 60
       type = "int"
       min = 15
       max = 1000
       step = 5
   [/advanced_preference]
```
3. [Wire the toggle — if there's an existing "Limit frame rate" boolean toggle in src/gui/dialogs/preferences_dialog.cpp (or .hpp), make sure it enables/disables the slider. When the toggle is off, max_fps should be set to a sentinel (e.g. 0 or 1000) meaning uncapped.]
4. [Verify the engine reads the preference — confirm display.cpp or draw_manager.cpp reads prefs::get().max_fps() (not just the CLI argument) so the UI change actually affects the frame cap at runtime.]
5. [Test — use the in-game :fps command to verify the cap changes live when the preference is updated.]

**Implement:** [https://github.com/Ale-A2/wesnoth_ALE_contrib.git]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [With --fps enabled, confirm the "act" column matches the slider value set in preferences. Test at 15, 30, 60, and monitor-rate values.]

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: Enter a normal run of the game and check the setting. Normalyy it would not contain a slider for frame rate
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] File were modified and immediate tried to compile and link the c++ files to see how the code affected that actual assets in game( Compilation took half and hour)
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [3] Progress

- [have parsed the data base to find where the implemenation of the frame rate slider could actually be done]
- 03_display.cfg — added a "Max frame rate:" row (label + max_fps_slider + max_fps_slider_label value display) directly below the VSync toggle, since both relate to rendering.
- preferences_dialog.cpp — two additions:
  - After the VSync block: registered max_fps_slider against refresh_rate, set its range to 10 … native_refresh_rate, and a value-label transform where the top position shows "Unlimited".
In pre_show: bind_default_status_label(...) so the value label updates live as you drag.

**Challanges**:
- Since source code was modified, the project had to be compiler again though cmake, this only took a couple of hours. However this came with the realization that any change made in the project would take a bunch of time to see some result that might end in failure.

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** 3_Display.cfg, preferences_diaolog.cpp
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
