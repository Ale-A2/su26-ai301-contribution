# su26-ai301-contribution


# Contribution [2210]: [Allow UI setting to control MAX FRAME-rate]

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

[Create an interface withing the established gui of the game and include a slider that should change the frame rate of the game and take effect when the setting menu is closed]

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
- **Screenshots/logs:** 
- **My findings:** [Reproducing the game is not complicated, actually it is simply by following their guide of installation and having a proper visual studio set up. However, I tried hours on my main computer that is not as properly set up as my lapton and it wasn't able to replicate. I am still not sure why that is the case, but for proper execution Cmake and visual studio should handle all of the dependancies and setting up the environment for the user. Wait time is still long]

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
- [ ] Test case 2: enable the fps data using the game engine 
- [ ] Test case 3: Using the fps frame data and a build that contains the changes done for the issue, the slider should show in the preference setting and the frame data should reflect the change.

### Integration Tests

- [ ] File were modified and immediate tried to compile and link the c++ files to see how the code affected that actual assets in game( Compilation took half and hour)
- [ ] Test with provided frame data

### Manual Testing

To ensure that the slider works, I had to enable a flag for the executable of the game --fps. This enables a table that contains frame data of the image drawing renderer. Usually the game starts with fps unlimited which matches the frame rate of the monitor. With the slider, at the beginning, was showing and was reacting but the actual drawing manager file was not updating how the frames should be drawn. After some testing and rebuilding the project, the implemented changes had an effect of the display frame rate. 

However, this effect is volatile. The game doesn't save the setting the user has chosen so whenever the start a new session their preference for frames is set to unlimited(Monitor refresh rate).

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

### Week [4] Progress

- Re-structuring the slider so each desired frame gap is 5 steps and instead of having the choice available of 0 frames(which would make no sense), now the slider provides the options from 10-60 and pulling the tab at the end of the line enables the "unlimited" frame rate.
  - Gui file was modified for these changes.
```
[slider]
 id = "max_fps_slider"
 definition = "minimal"
 minimum_value,maximum_value = 10,65
 step_size = 5
 tooltip = _ "Limit the maximum frame rate. Set to ‘Unlimited’ to use your monitor’s native refresh rate."
[/slider]
```
- When the user has set a max-fps cap below the monitor's refresh rate, also throttle frames that *were* drawn
  otherwise actively-rendered frames are paced only by hardware vsync, so the cap has no effect (especially with vsync disabled).
- Preference diaologue is what actually handles the setting of fps. It takes the data from the slider to set the refresh_rate
- 

### Code Changes

- **Files modified:** 3_Display.cfg, preferences_diaolog.cpp
- **Key commits:** (https://github.com/Ale-A2/wesnoth_ALE_contrib/commit/8b93f165c322a76de1488f8aa03b5740cb6abd1f)
- **Approach decisions:** [Why you chose certain approaches]
<img width="1916" height="1126" alt="image" src="https://github.com/user-attachments/assets/49ade386-7ea7-4adc-ac51-de70c213bd2b" />
<img width="1912" height="1120" alt="image" src="https://github.com/user-attachments/assets/0df8e366-8a20-468b-b55c-568cac961a82" />

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: I was immediate met with information about the issue, and the policies regarding the use of AI, so I have been researching the functionalities 
of the source code to ensure that the code is good for deployment, **then I will make my pull request**.
- [Date]: [How you addressed it]

**Status:** Awaiting

---

## Learnings & Reflections

### Technical Skills Gained
- This was my first time setting up such a large and dense project. I waas glad that cmake could handle building the project and taking care of the dependencies but wht I was not prepared for was the about 4 hours of downloading packages, setting environment and creating executable. This is skill I would like to polish so in the future I can implement it for my own projects.
- Documentations reading. While this is skill I have been aware throughout my college career it had never really been put to the test- not at this level. 
  - I had to read documentation for the installation of the project. How to run it and eventually I implemented the use of flag which in my experience has been limited to cli instruction but never to run projects like this. That flag was used to enable a fps information overlay.
- Use of C++ style conventions and project coding stye conventions had to be applied.
- Careful use of AI to understand the code base and the tools used to replicate the projects 

### Challenges Overcome
- At the beginning I had issues setting up the environment. I was attempting to use my desktop because it has more graphical power and processing power. I am still unsure of the reason why that computer couldn't find the proper path for some of the files withing the directory of the project. 
  - I moved the project onto my personal laptop. This is a machine I had previously use to work on the basic game engine I worked for a CSCI class. I alread had cmake and visual studio installed in it, so it was a matter of giving it the proper file path to start the building process.
- The slider was only visual. At some point using the slider did not really change the frame rate for the game. If used it had no effect on the actual draw loop for the game. 
  - I wasn't sure how to change it or what could have caused it. So I asked the AI agent to suggest a solution by describing the problem and giving it detailed information on what I wanted the change to be, it suggested a quick solution that took a couple of line changes.
- The size of the codebase was too large in my opinion for a first time oss contribution. I had to use AI to parse the code safely and narrow down the code reading.
- I think I overly relied on AI. This meant that is created an overhead at the end of the project so that I had to understand the changes and justify so that the maintainers actually accept the contribution.

### What I'd Do Differently Next Time
- Before I move to implementation I should do a thorough scan of the code base to understand how things are tied, and what files I should be able to modify.
- I should spend more time checking if the scope of the project is something I can get into. Otherwise I would have to spend a lot of time reviewing and feeling lost due to the sheer amount of components to the project.

---

## Resources Used

- [Project Coding Standards](https://wiki.wesnoth.org/CodingStandards#C.2B.2B_version)
