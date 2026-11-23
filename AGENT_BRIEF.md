# Mission: test the app as a real new user

You are a QA tester playing ONE persona (find yours in personas.json with the
PERSONA id you were given). You drive a real phone (Android emulator or iOS
Simulator, see PLATFORM) where the app is already installed and open.
Goal: find bugs, crashes and confusing moments, and report them precisely and
verifiably. Play the persona honestly (patience, skills, goal), but stay a tester.

## Tools (only these)
Android
- See the screen: `python3 scripts/android/screen.py results/<persona> <NN>` (NN = 01, 02...).
  Read the PNG it writes, then the element list (centres in real pixels).
- Tap: `adb shell input tap X Y` - Swipe/scroll: `adb shell input swipe X1 Y1 X2 Y2 400`
- Back: `adb shell input keyevent KEYCODE_BACK` - Type: `python3 scripts/android/type.py "text"`
iOS
- See the screen: `python3 scripts/ios/screen.py results/<persona> <NN>` (positions in points).
- `bash scripts/ios/gesture.sh tap X Y | scroll down | back | dismiss-sheet | enter | relaunch`
- Type: `python3 scripts/ios/type.py "text"` (tap the field first). It pastes: if iOS asks to
  paste from "CoreSimulatorBridge", tap Allow Paste. That alert comes from the tool, not the app.

## Rules
- Take a screenshot after EVERY action and look at it before the next one. Number them in order.
- Never clear app data or reinstall. Never use a real e-mail: use the test address pattern
  given in your persona (`email`), or `success+<persona>@simulator.amazonses.com`.
- A blocked step is a finding, not the end of the test. Write it down, then try what a
  real user would: change ONE thing at a time (another e-mail such as `<persona>@example.com`,
  all lower case, a longer password, scroll, go back, another button) until you get through.
- Before you report that a setting does nothing, leave the screen it changes (back to the
  main screen), open that screen again and look. Many apps read settings only when a screen opens.
- Budget: about 30 actions, then write the report. Use it: stop earlier only if the app
  crashes twice, or after 4 different attempts on the same blocked step.
- Write everything under `results/<persona>/`. Nothing else.

## The app (pais-solo)
Brazilian app for single fathers: legal questionnaire (pensão alimentícia) in PT-BR only.
Flow: LoginScreen -> register (name, e-mail, password) -> diagnostic onboarding wizard
(questions A to I) -> action plan (dashboard). The backend is the production API; it is
reachable. Create ONLY your own test account with the e-mail pattern below. Never use a
real e-mail, never delete accounts, never pay anything.

## What to check
1. First launch and registration until the onboarding starts. E-mail:
   `success+<persona>@simulator.amazonses.com`.
2. The adaptive onboarding (the core of this release):
   - questions are skipped automatically depending on earlier answers (branching);
     a skipped question that seems necessary, or a visible one that seems useless, is a finding;
   - answers the app infers from the children you registered show a
     "Sugerido automaticamente" chip — is it clear and trustworthy?
   - progress indicator per stage (A to I) and the review summary before the plan;
   - close the app in the middle of the wizard, reopen it: it must resume on the same
     question with the answers kept;
   - questions interpolate real names/ages of the children you typed — wrong or generic
     text (e.g. "seu filho(a)" when a name exists) is a finding.
3. The persona's goal (personas.json, `goal`).
4. Layout on THIS device: cut text, buttons hidden by the keyboard, notch or home bar,
   large text sizes, dark mode contrast.
5. Error and empty states: no network answer (airplane mode), wrong input, duplicated
   e-mail, weak password.
6. Sensitive questions (health/disability of a child): a transition screen and explicit
   consent must appear before them (LGPD). Missing consent is a major finding.
7. Accessibility: when `screen.py` ends with an `accessibility check` line (controls with no
   name, touch targets under 48 dp / 44 pt), look at those controls on the screenshot. Report
   each kind once, with the screens where you saw it: minor, unless it stops a task.

## Final report
Write both files with the Write tool (writing through the shell is blocked).
1. `results/<persona>/report.md`
   - 5-line summary with the device.
   - Issues, most severe first. For each: screenshot number(s), steps to reproduce,
     actual result, expected result, severity (blocker / major / minor / cosmetic).
   - Device-specific layout problems (same format, shorter).
   - The persona's opinion in their own words: would they keep the app? score /10.
2. `results/<persona>/findings.json`:
   `{"score": 7, "issues": [{"title": "...", "severity": "major", "screen": "Sign-up", "screens": ["05", "06"]}]}`
