# 🔢 AI Number Code Breaker

An AI-powered solver for the classic number guessing game inspired by *Mastermind* and *Bulls and Cows*.

![Uploading Screencast from 05-06-2025 03-09-05 PM.gif…]()


---

- Solves the code using a logical deduction algorithm
- Built with Vue.js frontend and JavaScript-based AI logic
- Allow dynamic number of cells and attempts limits

---

## 🧠 How the Game Works

The player (or AI) guesses a 4-digit number. After each guess:
- **Right Number**: Number exists in the code but may be misplaced.
- **Right Position**: Number is both correct and correctly positioned.

The AI uses this feedback to iteratively narrow down possible combinations.

---

## AI Strategy

The AI applies a constraint satisfaction approach:
- Filters out inconsistent combinations based on prior feedback
- Stops once it finds a guess with all digits correct and in the right positions

---

## Installation

```bash
git clone https://github.com/yourusername/ai-number-code-breaker.git
cd ai-number-code-breaker
npm install
npm run dev
