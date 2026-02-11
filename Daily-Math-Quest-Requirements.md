# 🧮 Daily Math Quest — Requirements Document

**Project:** Daily Math Quest
**Version:** 1.0
**Author:** You!
**Date:** February 10, 2026
**Type:** Single-page web game (HTML / CSS / JavaScript)

---

## 1. Project Overview

Daily Math Quest is a retro pixel-art styled math puzzle game that runs in a web browser. The player solves randomly generated math problems, earns points, levels up, and tracks their streak of correct answers. The entire game is contained in a single HTML file with no server or database required.

---

## 2. Target Audience

- Age group: Kids ages 8–12
- Skill level: Basic to intermediate math (addition, subtraction, multiplication, division)
- Platform: Any modern web browser on desktop, tablet, or phone

---

## 3. Technology Requirements

### 3.1 Languages Used
- **HTML5** — page structure
- **CSS3** — all styling, animations, and visual effects
- **JavaScript (vanilla)** — game logic, puzzle generation, state management

### 3.2 External Dependencies
- **Google Fonts** — two pixel-art fonts loaded from the internet:
  - `Press Start 2P` — used for titles, scores, and puzzle text
  - `Silkscreen` — used for body text, labels, and hints

### 3.3 Browser Storage
- **localStorage** — used to save the player's progress (score, streak, level) between sessions
- Key name: `mathquest`
- Data format: JSON string

### 3.4 No Server Required
- The game runs entirely in the browser
- No backend, no database, no API calls
- Can be opened directly as a local file or hosted on GitHub Pages

---

## 4. Visual Design Requirements

### 4.1 Theme
- Retro / pixel art aesthetic inspired by classic 8-bit and 16-bit video games

### 4.2 Color Palette

| Name   | Hex Code  | Used For                          |
|--------|-----------|-----------------------------------|
| Background | `#1a1a2e` | Page background                |
| Panel  | `#16213e` | Card and box backgrounds          |
| Border | `#0f3460` | Borders on cards, inputs, boxes   |
| Accent | `#e94560` | Buttons, error states, streak counter |
| Gold   | `#f5c518` | Score numbers, correct answer text, stars |
| Green  | `#00e676` | Correct answer feedback, next button |
| Text   | `#eaeaea` | Main readable text                |
| Muted  | `#7a7a9a` | Hints, labels, secondary text     |

### 4.3 Background Effects
- **Pixel grid overlay** — a subtle 16×16px grid of thin white lines over the entire page, created with CSS gradients on `body::before`
- **Scanline effect** — horizontal lines across the screen (like an old TV) created with CSS repeating gradients on `body::after`

### 4.4 Pixel Corner Effect
- Stat boxes and the puzzle card have small squares cut from their corners to create a pixel/retro look
- Achieved using CSS `::before` and `::after` pseudo-elements with small colored squares positioned at the corners

### 4.5 Typography
- All headings, scores, and puzzle text: `Press Start 2P` (pixelated font)
- All labels, hints, and small text: `Silkscreen`
- No system fonts or generic fonts used

### 4.6 Responsive Design
- Max content width: 500px, centered on page
- On screens smaller than 500px:
  - Title shrinks from 20px to 15px
  - Puzzle question shrinks from 22px to 18px
  - Stat box padding is reduced

---

## 5. Game Layout (UI Components)

The page is made up of these sections, stacked vertically from top to bottom:

### 5.1 Header
- Game title: "🧮 MATH QUEST" in gold pixel font with a drop shadow and glow
- Subtitle: "Solve today's puzzle!" in muted text

### 5.2 Stats Bar
Three stat boxes displayed in a horizontal row:
- **Score** — total points earned (gold number)
- **🔥 Streak** — current streak of correct answers in a row (red number)
- **Level** — current difficulty level, 1 through 5 (gold number)

### 5.3 Difficulty Indicator
- Shows 1 to 5 star icons (★) representing the current level
- Filled stars are gold, empty stars match the border color
- Text label next to stars: Easy, Medium, Hard, Expert, or Master

### 5.4 Puzzle Card
- A bordered card with pixel-cut corners containing:
  - **Puzzle type label** — e.g., "Addition", "Multiplication" (red uppercase text)
  - **Puzzle question** — the math problem displayed large and centered (e.g., "24 + 17")
  - **Hint text** — a small helpful hint below the question (e.g., "Add the numbers!")

### 5.5 Answer Section
- A number input field where the player types their answer
  - Dark background, gold text, centered, pixel font
  - Placeholder text: "???"
  - Glows red when focused
- A "GO!" button next to the input
  - Red background with hover lift effect and active press effect

### 5.6 Feedback Area
- After submitting an answer, this area shows:
  - **Correct:** green "✓ CORRECT! +X PTS" text with a bounce animation, plus a "NEXT PUZZLE →" button
  - **Wrong:** red "✗ NOPE! Answer: X" text with a shake animation, plus a "TRY ANOTHER →" button

### 5.7 Streak Display (bottom of page)
- A row of 10 square dots representing the current streak
  - Empty dots: dark with border
  - Filled dots: gold with a star (★) inside and a glow effect
- Label: "Daily Streak"
- Best streak counter: "Best: X"

---

## 6. Game Logic Requirements

### 6.1 Puzzle Types

There are 5 types of math puzzles. Each type has its own generation rules that scale with the player's level.

#### 6.1.1 Addition
- Generates two random numbers `a` and `b`
- Question format: `a + b`
- Answer: the sum of `a` and `b`
- Number range: 1 to `(10 + level × 15)`
- Hint: "Add the numbers!"

#### 6.1.2 Subtraction
- Generates two random numbers where `a` is always greater than or equal to `b` (so the answer is never negative)
- Question format: `a - b`
- Answer: `a - b`
- Number range: `b` is 1 to `(10 + level × 15)`, `a` is `b` to `(10 + level × 15 + b)`
- Hint: "Subtract!"

#### 6.1.3 Multiplication
- Generates two random numbers `a` and `b`
- Question format: `a × b`
- Answer: `a × b`
- Number range: 2 to `min(3 + level × 2, 12)` — capped at 12 so tables stay reasonable
- Hint: "Multiply!"

#### 6.1.4 Mystery Number
- Generates a hidden answer and a second number `b`, then shows their sum
- Question format: `? + b = sum`
- Answer: the missing number
- Number range: 1 to `(5 + level × 8)`
- Hint: "Find the missing number!"

#### 6.1.5 Division
- Generates a divisor `b` and an answer, then multiplies them to get `a` (so it always divides evenly)
- Question format: `a ÷ b`
- Answer: `a / b`
- Divisor range: 2 to `min(2 + level, 10)`
- Answer range: 1 to `(10 + level × 3)`
- Hint: "Divide evenly!"

### 6.2 Puzzle Selection Rules
- At **Level 1**, the player gets the first 3 puzzle types (Addition, Subtraction, Multiplication) plus up to 1 more based on level
- At **Level 2+**, all 5 puzzle types are available
- Puzzle type is chosen randomly from the available types each round

### 6.3 Scoring System
- Points earned per correct answer: `10 × current level`
  - Level 1 = 10 points, Level 2 = 20 points, etc.
- No points deducted for wrong answers

### 6.4 Streak System
- The streak counter goes up by 1 for each correct answer in a row
- A wrong answer resets the streak to 0
- The best (highest ever) streak is tracked separately
- The streak bar visually shows up to 10 dots; dots fill in gold as the streak grows

### 6.5 Leveling System
- The player starts at Level 1
- Every 5 puzzles solved correctly, the level goes up by 1
- Maximum level: 5
- Higher levels increase the range of numbers in puzzles (making them harder)
- Difficulty labels: Level 1 = Easy, Level 2 = Medium, Level 3 = Hard, Level 4 = Expert, Level 5 = Master

### 6.6 Answer Validation
- The player's input is parsed as an integer
- If the input is empty or not a number, the input border flashes red briefly and nothing happens
- If the answer matches the correct answer exactly, it counts as correct
- If it doesn't match, it counts as wrong
- Once an answer is submitted, the input and button are disabled until the player clicks "Next Puzzle" or "Try Another"

---

## 7. Animation & Effects Requirements

### 7.1 Page Load Animations
- All sections animate in with a **drop-in** effect (slide down + fade in)
- Each section has a staggered delay:
  - Header: 0s
  - Stats bar: 0.1s
  - Puzzle card: 0.2s
  - Answer section: 0.3s
  - Streak display: 0.4s

### 7.2 Correct Answer Effects
- Feedback text does a **bounce** animation (jumps up then settles)
- **Particle burst**: 6 random emoji particles (⭐, ✨, 🎉, 💥, 🔥) spawn at random positions on screen and float upward while fading out over 1 second

### 7.3 Wrong Answer Effects
- Feedback text does a **shake** animation (rapid left-right movement)

### 7.4 Button Hover Effects
- "GO!" button: lifts up 2px with a shadow underneath on hover, presses down on click
- "Next Puzzle" button: same lift effect in green

### 7.5 Input Focus Effect
- When the answer input is focused, its border turns red and a red glow appears around it

---

## 8. Data Persistence Requirements

### 8.1 What Gets Saved
The following values are saved to `localStorage` under the key `mathquest` as a JSON string:
- `score` (number) — total points
- `streak` (number) — current correct streak
- `bestStreak` (number) — highest streak ever achieved
- `level` (number) — current difficulty level (1–5)
- `puzzlesSolved` (number) — total puzzles answered correctly

### 8.2 When Data is Saved
- After every answer (correct or wrong)

### 8.3 When Data is Loaded
- On page load, before the first puzzle is generated
- If no saved data exists, the game starts fresh with all values at 0 (level starts at 1)

---

## 9. Keyboard & Input Requirements

- The answer input accepts **numbers only** (HTML `type="number"`)
- Pressing the **Enter** key submits the answer (same as clicking "GO!")
- After submitting, the input is disabled until the next puzzle
- The input auto-focuses when a new puzzle loads
- Autocomplete is disabled on the input

---

## 10. File Structure

The entire game is a **single HTML file** containing:
- `<style>` block with all CSS (no external stylesheet)
- HTML body with the page structure
- `<script>` block with all JavaScript (no external script files)

No build tools, no frameworks, no npm packages needed.

---

## 11. Future Ideas (Nice to Have)

These are ideas for future versions — not required for v1.0:
- Sound effects (correct/wrong buzzer, level-up chime)
- A timer mode (solve as many as you can in 60 seconds)
- Different themes the player can choose (space, underwater, jungle)
- A "daily challenge" that's the same puzzle for everyone each day
- Leaderboard to compare scores with friends
- More puzzle types (fractions, exponents, order of operations)
- Achievements / badges for milestones (e.g., "Solve 100 puzzles")

---

*This document describes everything needed to build Daily Math Quest v1.0 from scratch.*
