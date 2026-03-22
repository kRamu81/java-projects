# Guess the Number — Java

A beginner-friendly console game where the computer picks a random number between 1 and 100, and the player tries to guess it. After each guess the game gives a hot/cold hint until the player finds the number.

---

## How the Game Works

```
Computer picks a random number (1–100)
  └── Loop (unlimited attempts)
        ├── Player enters a guess
        ├── |guess - target| > 10  →  print "Cold"
        ├── |guess - target| <= 10 →  print "Hot"
        └── guess == target        →  print "Correct!" → end game
```

---

## Rules

| Condition | Output |
|---|---|
| Guess is more than 10 away from the number | `Cold` |
| Guess is within ±10 of the number | `Hot` |
| Guess exactly matches the number | Correct — game over |
| Wrong guess | Loop continues (unlimited attempts) |

---

## Concepts Used

| Concept | Where it appears |
|---|---|
| **User output** | `System.out.println()` for hints and result |
| **User input** | `Scanner` to read the player's guess |
| **Conditional statements** | `if / else if / else` to evaluate hot, cold, or correct |
| **Random number generation** | `Math.random()` or `Random` class for the target number |
| **Loops** | `while` loop for unlimited attempts |

---

## Step-by-Step Build Plan

### Step 1 — Get a random number
Generate a random integer between 1 and 100 and store it as the target.

```java
import java.util.Random;
Random rand = new Random();
int target = rand.nextInt(100) + 1;  // 1 to 100 inclusive
```

### Step 2 — Compare the guess
Read one guess from the player and apply the hot/cold/correct logic.

```java
Scanner scanner = new Scanner(System.in);
System.out.print("Enter your guess: ");
int guess = Integer.parseInt(scanner.nextLine());

if (guess == target) {
    System.out.println("Correct!");
} else if (Math.abs(guess - target) <= 10) {
    System.out.println("Hot");
} else {
    System.out.println("Cold");
}
```

`Math.abs()` handles both over-guesses and under-guesses with a single condition.

### Step 3 — Add the loop
Wrap the guess-and-compare logic in a `while` loop that only exits when the player is correct.

```java
while (true) {
    System.out.print("Enter your guess: ");
    int guess = Integer.parseInt(scanner.nextLine());

    if (guess == target) {
        System.out.println("Correct! The number was " + target);
        break;
    } else if (Math.abs(guess - target) <= 10) {
        System.out.println("Hot");
    } else {
        System.out.println("Cold");
    }
}
```

---

## Full Code

```java
import java.util.Random;
import java.util.Scanner;

public class GuessTheNumber {

    public static void main(String[] args) {
        Random rand = new Random();
        int target = rand.nextInt(100) + 1;

        Scanner scanner = new Scanner(System.in);
        System.out.println("Welcome! Guess the number between 1 and 100.");

        while (true) {
            System.out.print("Enter your guess: ");
            int guess = Integer.parseInt(scanner.nextLine());

            if (guess == target) {
                System.out.println("Correct! The number was " + target);
                break;
            } else if (Math.abs(guess - target) <= 10) {
                System.out.println("Hot");
            } else {
                System.out.println("Cold");
            }
        }

        scanner.close();
    }
}
```

---

## Application Flow

```
Start
  └── Generate random number (1–100)
  └── Print welcome message
  └── while (true)
        └── Read guess from player
              ├── guess == target   →  "Correct!" → break → End
              ├── |diff| <= 10      →  "Hot"      → loop again
              └── |diff| > 10       →  "Cold"     → loop again
```

---

## Key Code Patterns

### Random number in range
```java
rand.nextInt(100) + 1
// nextInt(100) gives 0–99, adding 1 shifts range to 1–100
```

### Distance check with Math.abs()
```java
Math.abs(guess - target) <= 10
// works for both over-guesses and under-guesses in one expression
```

### Loop exit with break
```java
while (true) {
    // ...
    if (guess == target) { break; }
}
// Only exits on a correct guess — unlimited attempts otherwise
```

---

## Possible Enhancements

- **Attempt counter** — track how many guesses the player took and display it at the end.
- **Limited attempts** — give the player a fixed number of tries (e.g. 10) and end the game if they run out.
- **Closer hints** — add a "Very Hot" band for within ±5, and "Freezing" for more than 50 away.
- **Play again** — after a correct guess, ask if the player wants a new game without restarting the program.
- **Input validation** — wrap `Integer.parseInt()` in a `try/catch` so a non-numeric entry doesn't crash the game.

---

## Running the Project

```bash
# Compile
javac GuessTheNumber.java

# Run
java GuessTheNumber
```

Requires Java 8 or later.
