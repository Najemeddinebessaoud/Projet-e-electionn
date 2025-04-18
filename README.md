# Election Management System in C

This is a simple console-based application written in C that simulates a basic election system. It allows creating elections, adding candidates, voting, changing votes, and displaying election results.

## Features

- Create an election for a specific year
- Add candidates and assign their initial vote
- Change a candidate’s vote
- Count votes and determine the winner
- Display all candidates and their status (elected or not)
- Display the list of voters for a given candidate

## How it works

Each election is identified by its year and contains a list of candidates. Each candidate has a name and a vote (which is the name of another candidate). After each vote or change of vote, the program recalculates the number of votes each candidate has received and updates the election result.

## Compile and Run

To compile the program:

```bash
gcc -o election election.c
