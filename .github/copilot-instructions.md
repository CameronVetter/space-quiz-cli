# Copilot instructions

## Project shape

- This is a dependency-free, browser-only quiz. The complete application lives in `index.html`; keep the app runnable by opening that file directly without a server.
- `index.html` contains three layers in one document:
  - Inline CSS defines the responsive visual system, component states, animations, dark-mode variables, and reduced-motion behavior.
  - Semantic HTML provides the quiz shell, progress bar, answer fieldset, live feedback, and results view.
  - An inline JavaScript IIFE owns quiz state and interaction logic.
- The JavaScript flow is intentionally small and stateful:
  - `questions` is the only quiz data source. Each item has `question`, `options`, a zero-based `answer`, and explanatory `fact`.
  - `renderQuestion()` updates the question, progress UI, and dynamically creates answer buttons.
  - `chooseAnswer()` guards against repeat answers, updates the score, disables choices, marks correct/wrong states, and reveals the next action.
  - `showResults()` selects the reaction/message and switches from `#quiz-view` to `#results-view`; `animateFinalScore()` handles the results count-up.
  - `resetQuiz()` restores the initial state without reloading the page.

## Commands and validation

- There is no package manifest, build script, test suite, or lint configuration.
- Run the app locally by opening `index.html` (for example: `xdg-open index.html`).
- Validate the inline JavaScript syntax with:

  ```sh
  node -e "const fs=require('fs'); const html=fs.readFileSync('index.html','utf8'); const scripts=[...html.matchAll(/<script>([\s\S]*?)<\/script>/g)]; if(!scripts.length) throw new Error('No inline script found'); for(const s of scripts) new Function(s[1]); console.log('Inline JavaScript syntax passed');"
  ```

## Conventions to preserve

- Keep all application code in `index.html` unless the project is deliberately converted to a build-based structure. Do not add runtime dependencies for quiz functionality.
- Add or edit quiz content in the `questions` array rather than hardcoding question-specific behavior elsewhere. Keep answer indexes zero-based and keep four options per question unless the UI and keyboard shortcut behavior are updated together.
- Use the existing DOM references and render/state functions instead of adding separate event systems. Answer buttons are generated at render time and use `data-index` for selection.
- Preserve accessibility behavior: use semantic headings and fieldsets, keep feedback in the existing `role="status"` live region, maintain visible `:focus-visible` states, focus the first answer on each question, focus the next action after answering, and focus the results container when the quiz ends.
- Preserve keyboard behavior: Tab/Enter/Space must work through native buttons, and number keys `1` through `4` select answers only while a question is unanswered.
- Keep visual colors and surfaces expressed through the existing CSS custom properties. Any new state should work in both the default theme and `@media (prefers-color-scheme: dark)`.
- Respect `@media (prefers-reduced-motion: reduce)`. New animations should have a reduced-motion-safe behavior; the results score already switches to an immediate value for users who request reduced motion.
- Keep user-visible feedback explicit: correct answers use the green state, incorrect selections use the red shake state, and the correct answer is revealed after a wrong selection.
