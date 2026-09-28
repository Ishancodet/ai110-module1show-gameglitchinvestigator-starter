# Reflection: Game Glitch Investigator

## 1. Bug reproduction log

| Bug | What I did | What happened | What should have happened |
|---|---|---|---|
| Backwards hints | Checked the secret in Developer Debug Info (it was 42), then guessed 60 | The hint said "📈 Go HIGHER!" | It should have told me to go lower, since 60 is above 42 |
| Attempts off by one | Started a Normal game (8 attempts) and looked at "Attempts left" before guessing. Then typed "abc" and clicked Submit | It showed 7 attempts left before my first guess, and the invalid input still used up an attempt | It should show 8 left at the start, and a non-number should not cost an attempt |
| New Game does not reset | Guessed the secret to win, then clicked "New Game 🔁" | The page still said "You already won" and I could not play. The score also carried over | It should give a fresh game with status "playing", score 0, empty history, and a new secret in the difficulty range |

## 2. How did you use AI as a teammate?

### A suggestion I accepted

I asked Claude Code to move `get_range_for_difficulty`, `parse_guess`, `check_guess`, and `update_score` from `app.py` into `logic_utils.py`, fix the backwards hint, and update the import in `app.py`. It noticed that the swapped message appeared twice inside `check_guess`, once in the normal int comparison and once in the string-comparison fallback, and it fixed both so the outcome labels and hint messages agreed everywhere. It also pointed out that the string-comparison fallback and the strange behavior in `update_score` were separate bugs and left them alone because I had asked it to only fix the hint direction.

I accepted this because the change was small, readable, and did exactly what I asked without expanding scope. I verified it by reading the diff for all three files, running `pytest` (3 passed), and playing the game. A guess above the secret now says go lower.

### A suggestion I did not accept as written

While fixing the New Game button, Claude Code pointed out that `attempts` is initialized to 1 on the first game but New Game resets it to 0, and it offered to fix that as well. It was right that this is a bug (it is Bug 2 in my log), but I told it not to change it. I wanted each chat session to fix one problem so I could verify one change at a time, and the attempts bug lives in a different part of the code than the reset block. Fixing it properly would also mean moving `attempts += 1` so it only runs after `parse_guess` succeeds, which I had not tested yet. I kept the New Game fix scoped to the `if new_game:` block and left the attempts bug documented for a later pass.

## 3. Debugging and testing your fixes

- After the refactor, running `pytest` failed with `ModuleNotFoundError: No module named 'logic_utils'`. Running `python -m pytest` passed, which told me the code was fine and the real problem was that the project root was not on Python's import path. I added a `pytest.ini` file containing `pythonpath = .` so plain `pytest` works.
- The three starter tests expected `check_guess` to return a plain string, but the function actually returns a tuple of `(outcome, message)`. I updated the tests to unpack the tuple and assert on the outcome.
- I added two regression tests, `test_too_high_hint_says_go_lower` and `test_too_low_hint_says_go_higher`, that check the message text. The starter tests only checked the outcome label, so they would have passed even with the backwards hint. All 5 tests now pass.
- Manual checks in the running app with `python -m streamlit run app.py`: a high guess says go lower, and after winning, New Game resets the score to 0, sets status back to playing, clears the history, and picks a new secret inside the difficulty range.
- The New Game logic lives in Streamlit UI code that depends on `st.session_state`, so I verified it manually in the browser rather than with a unit test.

## 4. What I learned

The AI was fast and accurate when I gave it a narrow, specific task with the files attached, and it was good at flagging nearby problems without fixing them uninvited. But the tests it started with would not have caught the original bug, and the refactor broke the test imports until I understood the path issue. The AI did the typing; I still had to decide what was in scope, read every diff, and confirm the fix in both the tests and the actual game.