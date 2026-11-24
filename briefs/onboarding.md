# Mission: test the adaptive onboarding of pais-solo

You are a QA tester playing ONE persona (find yours in personas.json with the
PERSONA id you were given). Drive the phone where the app is installed and open.
Budget: about 30 actions, screenshot after EVERY action, then write the report.

## Device commands (this brief is self-contained — use exactly these)

Android
- See the screen: `python3 scripts/android/screen.py results/<persona> <NN>` (NN = 01, 02...).
  Read the PNG it writes, then the element list (centres in real pixels).
- Tap: `adb shell input tap X Y` - Swipe/scroll: `adb input swipe X1 Y1 X2 Y2 400` → use
  `adb shell input swipe X1 Y1 X2 Y2 400`
- Back: `adb shell input keyevent KEYCODE_BACK` - Type: `python3 scripts/android/type.py "text"`
iOS
- See the screen: `python3 scripts/ios/screen.py results/<persona> <NN>` (positions in points).
- `bash scripts/ios/gesture.sh tap X Y | scroll down | back | dismiss-sheet | enter | relaunch`
- Type: `python3 scripts/ios/type.py "text"` (tap the field first).

Never clear app data or reinstall. A blocked step is a finding: write it down, change ONE
thing, try again. Write everything under `results/<persona>/`.

## Setup
- Register with e-mail `success+<persona>+<UNIQUE>@simulator.amazonses.com`, where `<UNIQUE>` is
  a fresh suffix you invent (e.g. the current date plus a word: `nov24-zebra`) so the address was
  NEVER used in a previous run — accounts from earlier runs are already registered and will block
  you. Any strong password; name as your persona would write it.
- Write the exact e-mail you used in your report so the team can find the account later.
- You reach the onboarding wizard right after registering.

## What to verify (in this order)
1. **Branching:** answer so a later question must disappear (e.g. say you have NO
   children / not disabled, then check the follow-up question does not appear).
   Report any question that appears when it should not, or disappears when it should not.
2. **Inference chips:** register at least one child with a birth date, then look for the
   "Sugerido automaticamente" chip on inferred questions. Wrong inference (e.g. wrong
   age band), missing chip, or chip without explanation = finding.
3. **Progress and summary:** the stage indicator (A to I) must match the real flow
   (skipped stages must not look broken). The review summary before the plan must show
   your answers and let you tap one to edit it.
4. **Resume:** kill the app mid-wizard, reopen: it must land on the same question with
   answers kept. Any lost answer = blocker.
5. **Interpolation:** questions must cite your child's real name/age where they exist.
   Generic "seu filho(a)" with a name typed = minor. Wrong name/age = major.

## Out of scope
Payments, deleting the account, real NFC-e scanning, other users' data.

## Final report
Same as the main brief: `results/<persona>/report.md` and
`results/<persona>/findings.json` (severity: blocker / major / minor / cosmetic),
issues most severe first, with screenshot numbers and steps to reproduce.
