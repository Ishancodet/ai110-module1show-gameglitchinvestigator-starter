## 📝 Document Your Experience

This is a Streamlit number guessing game that an AI wrote with several bugs. The player picks a difficulty, guesses a number, and gets Higher/Lower hints and a score.

Bugs I found:
1. The hints were backwards. A guess above the secret said "Go HIGHER".
2. The attempt counter started at 1 instead of 0, and invalid input like "abc" still used up an attempt.
3. New Game did not reset status, score, or history, so after winning you were stuck on the "You already won" screen. It also ignored the difficulty range.

Fixes I applied:
- Moved `get_range_for_difficulty`, `parse_guess`, `check_guess`, and `update_score` out of `app.py` into `logic_utils.py`, and swapped the hint messages in `check_guess` so they point the right way.
- Rewrote the `if new_game:` block to reset `attempts`, `score`, `status`, and `history`, and to draw the new secret from the current difficulty's range.
- Updated the starter tests to match the `(outcome, message)` return value and added two regression tests for the hint text. Added `pytest.ini` so `pytest` can import `logic_utils`.

Bug 2 (attempt counter) is documented but not yet fixed.

## 📸 Demo Walkthrough

1. Run `python -m streamlit run app.py`.
2. Expand "Developer Debug Info" to see the secret number.
3. Enter a guess higher than the secret and click Submit. The hint says go lower.
4. Enter a guess lower than the secret. The hint says go higher.
5. Enter the secret number. Balloons appear and the win message shows your final score.
6. Click "New Game 🔁". The score resets to 0, the win message is gone, and Debug Info shows a new secret inside the difficulty range.

## 🧪 Test Results

cat test_results.txt
============================= test session starts ==============================
platform darwin -- Python 3.14.2, pytest-9.1.1, pluggy-1.6.0
rootdir: /Users/ishan/ai110-module1show-gameglitchinvestigator-starter
configfile: pytest.ini
plugins: anyio-4.15.1
collected 5 items

tests/test_game_logic.py .....                                           [100%]

============================== 5 passed in 0.02s ===============================
(.venv) ishan@Ishans-MacBook-Pro-8 ai110-module1show-gameglitchinvestigator-starter % 