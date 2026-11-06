# Lab 6 Bug Report

Each bug was injected into `app/routers/artworks.py` one at a time, `pytest` was run, and the change was undone before the next one. Every bug was caught, so no extra tests were needed.

| Bug | Test(s) that failed |
| --- | --- |
| 1. `POST` returns `200` instead of `201` | `test_create_artwork` (fails on the status assertion). The `created_artwork` fixture also asserts `201`, so every test that uses it errors too: `test_create_artwork_is_saved`, `test_update_artwork`, `test_delete_artwork`, `test_create_duplicate`, `test_create_duplicate_ignores_case`, `test_update_same_title_and_artist_is_allowed` |
| 2. Duplicate check is case-sensitive | `test_create_duplicate_ignores_case` |
| 3. An artwork counts as a duplicate of itself | `test_update_same_title_and_artist_is_allowed` |
| 4. `DELETE` doesn't delete | `test_delete_artwork` (the follow-up `GET` returns `200` instead of `404`) |
| 5. Artist filter only matches the start of a name | `test_filter_by_artist[lowercase-partial]`, `test_filter_by_artist[da-vinci]`, `test_filter_by_artist[middle-of-name]` |

## Notes

- Bug 1 is caught by one real test failure plus six fixture errors. The fixture's own `assert response.status_code == 201` is doing extra work here.
- Bug 4 is only caught because `test_delete_artwork` does a second `GET` after the `DELETE`. Checking just the `204` would have missed it.
- Bug 5 is only caught by the partial-match cases (`monet`, `da vinci`, `vinci`). The `exact` case (`Claude Monet`) still passes under the bug, which is why the partial cases matter.
- After the last bug was undone, `pytest` passes again: 45 passed.
