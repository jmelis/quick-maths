# Quick maths

A single-file, dependency-free arithmetic game for kids. Open `index.html` in a browser, or use the published page: https://jmelis.github.io/quick-maths/

Two games, picked with a switch at the top of the setup screen:

- **Classic**: a 2-minute countdown. Pick a range for the first number and one for the second, plus any mix of +, − and ×. Score = right answers.
- **Drills**: pick a number, how far to go up (1 to max) and + or ×, then race the clock: the timer counts up and the aim is the shortest time. Questions you get wrong on the first try are added to the end.

Records are kept in the browser (`localStorage`), per game setup, with a Records screen to replay or delete them. Nothing leaves the device.

Pushing to `main` publishes the page to GitHub Pages (`.github/workflows/pages.yml`).
