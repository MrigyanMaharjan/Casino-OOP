🎰 Royal Casino

A console-based casino simulation game written in C++.
The project demonstrates object-oriented programming, random number generation, file handling, user input, inheritance, and basic game logic through several casino-style games.

📌 Project Overview

Royal Casino is a command-line casino simulation where players begin with 100 chips and can use their balance to play different games.

The player can:

Enter the casino floor
Play multiple casino games
Bet chips and receive winnings
View a persistent high-score leaderboard
Save their current balance
Continue a previously saved game
Exit the casino safely

The project is designed to demonstrate practical applications of C++ programming concepts in a single interactive application.

🎮 Available Games
1. 🎰 Slot Machine

The slot machine generates three random symbols.

Possible symbols include:

Cherry
Bell
BAR
Diamond
7

Different combinations produce different payouts.

Result	Payout
Three 7s	50×
Three Diamonds	20×
Three BARs	15×
Three Bells	8×
Three Cherries	5×
Two matching symbols	2×
No match	0×
2. 🪨 Rock Paper Scissors

The player competes against the computer.

The computer randomly selects:

Rock
Paper
Scissors

Winning gives the player 2× their bet, while a tie returns the original bet.

3. 🃏 Blackjack

A simplified version of Blackjack where the player competes against the dealer.

Features include:

Random card generation
Ace handling
Hit
Stand
Double Down
Blackjack detection
Dealer automatically draws until reaching 17
Bust detection
Push/tie handling

A natural Blackjack pays 2.5× the original bet in this implementation.

4. 🎡 Roulette

The roulette wheel generates a number between 0 and 36.

Players can bet on:

A single number
Red
Black
Even
Odd
Low (1–18)
High (19–36)

Example payouts:

Bet	Payout
Single Number	36×
Red	2×
Black	2×
Even	2×
Odd	2×
Low	2×
High	2×

Zero is treated as green.

5. 💥 Crash

In Crash, the multiplier starts at 1.00× and increases by 0.10× each round.

The player must decide when to cash out before the randomly generated crash point.

For example:

Current multiplier: 1.50x
Cash out? (Y/N): N

Current multiplier: 1.60x
Cash out? (Y/N): Y

You cashed out at 1.60x!


If the multiplier reaches the crash point before the player cashes out, the player loses the bet.

🖥️ Main Menu

The program provides the following main menu:

==========================================
              ROYAL CASINO
==========================================

 Player Balance : 100 chips

------------------------------------------
 1. Enter Casino Floor
 2. View High Scores
 3. Exit Casino
==========================================
 Choice:


The casino floor provides access to all five games.

🚀 How to Run
Requirements

You need:

A C++ compiler
C++11 or newer
A terminal/command prompt
Operating system such as Windows, macOS, or Linux

The program uses:

<iostream>
<fstream>
<cstdlib>
<ctime>
<thread>
<chrono>
<cstring>
💻 Compile Using g++

Save the source code as:

main.cpp


Then open a terminal in the project directory.

Windows / Linux / macOS

Run:

g++ main.cpp -o casino


Then run the program.

Windows
casino.exe

Linux/macOS
./casino

🧑‍💻 Running With C++17

For a modern compiler, you can also use:

g++ -std=c++17 main.cpp -o casino


Then:

./casino


On Windows:

casino.exe

🔄 Program Flow

The general program flow is:

Start Program
      |
      v
Enter Age
      |
      v
Age >= 18?
   /       \
 No         Yes
 |           |
Exit       Enter Name
             |
             v
      Check Saved Score
             |
             v
       Main Menu
        /   |   \
       /    |    \
 Casino  Scores  Exit
 Floor
   |
   v
Select Game
   |
   v
Place Bet
   |
   v
Play Game
   |
   v
Update Balance
   |
   v
Return to Casino Floor

🏗️ Object-Oriented Programming

One of the main goals of this project is to demonstrate Object-Oriented Programming (OOP).

Player Class

The Player class stores information about the player.

class Player {
    string name;
    float chips;
};


It provides functions such as:

getChips()
addChips()
removeChips()
setChips()


This allows the player's balance to be controlled through class methods rather than directly accessing the variables.

CasinoMachine Base Class

The CasinoMachine class provides common functionality for casino games.

It contains:

float multiplier;
float insertedChips;


It also provides the common betting function:

AskChips()


This reduces repeated code between the different casino games.

Derived Game Classes

The casino games inherit functionality from CasinoMachine.

The project contains:

CasinoMachine
     |
     +---- SlotMachine
     |
     +---- RockPaperScissors
     |
     +---- Blackjack
     |
     +---- Roulette
     |
     +---- Crash


This demonstrates inheritance and allows different games to share common casino functionality.

🧩 C++ Concepts Demonstrated

This project demonstrates several important C++ concepts.

Classes and Objects

Classes are used to represent:

Players
Casino machines
Individual games

Objects are created for each game on the casino floor.

Encapsulation

Player information such as the chip balance is kept inside the Player class.

The balance is modified using methods such as:

player.addChips(amount);
player.removeChips(amount);


This provides controlled access to the player's data.

Inheritance

The different casino games inherit from:

CasinoMachine


This allows common variables and functionality to be reused.

Constructors

Each game has a constructor that receives a reference to the player.

Example:

SlotMachine(Player& player):player(player)


This allows every game to interact with the same player's balance.

References

The games use:

Player& player;


This means the game works with the original Player object rather than creating a separate copy.

Therefore, winnings and losses are reflected across the entire casino.

Random Number Generation

Random numbers are used throughout the games.

The program initializes the random number generator using:

srand(time(0));


Random values are then generated using:

rand()


Examples include:

Slot machine symbols
Computer choices in Rock Paper Scissors
Blackjack cards
Roulette numbers
Crash points
💾 File Handling

The program uses a file called:

scores.txt


This file is used to store player scores.

Example:

Alex 250
John 180
Sarah 125
Mike 90


The program uses:

ifstream


to read saved scores and:

ofstream


to write scores.

💽 Save System

When the player selects:

3. Exit Casino


their name and current balance are saved to scores.txt.

If the player returns later and enters the same name, the program checks whether a saved score exists.

The player can then choose whether to continue the saved game.

🏆 High Score System

The program stores scores and sorts them from highest to lowest.

The sorting algorithm compares each player's score:

if(scores[j] > scores[i])


and swaps the scores when necessary.

This produces a leaderboard such as:

========== HIGH SCORES ==========

1. Alex       500 chips
2. Sarah      350 chips
3. John       220 chips
4. Mike       100 chips

=================================

🎨 Console Interface

The program uses ANSI escape codes to add color to the console.

For example:

🟢 Green — winning messages
🔴 Red — losing messages
🔵 Cyan — main title
🟡 Yellow — casino floor
⚪ Reset — normal console text

The program also includes cross-platform handling for Windows ANSI color support.

🖥️ Cross-Platform Support

The project includes conditional compilation:

#ifdef _WIN32
#include <windows.h>
#endif


This allows Windows-specific functionality to be used only when compiling on Windows.

The program also uses ANSI escape sequences for:

Console colors
Clearing the screen

This allows the program to work on modern:

Windows terminals
Linux terminals
macOS terminals
🔐 Age Verification

Before entering the casino, the player must enter their age.

if(age < 18){
    cout << "Access Denied!";
}


Players below 18 are prevented from entering the casino.

This is implemented as part of the application's simulated casino access system.

🧮 Game Logic

Each game uses different algorithms and rules.

Slot Machine

Random values are generated from 0–99 and mapped to symbols using probability ranges.

Example:

0–39   → Cherry
40–64  → Bell
65–84  → BAR
85–94  → Diamond
95–99  → 7


This gives each symbol a different probability.

Blackjack

Cards are randomly generated from 1–13.

Special handling is implemented for:

1  → Ace
11 → Jack
12 → Queen
13 → King


Aces initially count as 11 but are converted to 1 when necessary to prevent a bust.

The adjustAce() function handles this behavior.

Roulette

The wheel is represented using:

rand()%37