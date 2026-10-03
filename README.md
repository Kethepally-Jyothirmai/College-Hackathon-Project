# CineBook - Movie Ticket Booking System

A BookMyShow-style booking app with one extra idea: it plans your whole day so you can watch the **maximum number of complete movies with no overlapping shows**.

**Live demo:** https://claude.ai/artifact/KU7wzw346innseuJcpdrko
**Run locally:** open `cinebook.html` in any modern browser (no install, no backend).

## Problem statement

A cinema has several shows scheduled throughout the day. Each show has a start time and an end time. Select the maximum number of complete shows that a customer can watch without overlapping shows.

- **Input:** a list of shows, each with `start` and `end`.
- **Output:** the largest set of shows where no two overlap.
- **Goal:** maximise the number of shows (not the hours watched).

## Algorithm: greedy activity selection

```
function maxShows(shows, gap):
    sort shows by end time        # ties: earlier start first
    lastEnd = -infinity
    chosen = []
    for show in shows:
        if show.start >= lastEnd + gap:
            chosen.append(show)
            lastEnd = show.end
    return chosen
```

| Measure | Value |
|---|---|
| Time | O(n log n) - the sort dominates, then one linear pass |
| Extra space | O(n) |
| Brute force (for comparison) | O(2^n) - checks every subset |

**Why it is optimal (exchange argument):** take any optimal schedule. If its first show is not the one that finishes earliest, swap it for the earliest-finishing show. That show ends no later, so every other show still fits and the count does not drop. Repeating this for each position shows the greedy answer is as good as any optimal one.

**Gap between shows:** requiring `start >= lastEnd + gap` is the same as lengthening every show's end by `gap`, so greedy stays optimal.

### Dry run

Shows sorted by end time:

| ID | Show | Time | Last kept end | Decision |
|---|---|---|---|---|
| A | Pixel Pals | 9:15 - 10:50 | - | Keep |
| B | Chai & Chaos | 9:30 - 11:35 | 10:50 | Skip |
| C | Midnight Express | 10:00 - 11:50 | 10:50 | Skip |
| D | Iron Fist 7 | 11:00 - 1:20 PM | 10:50 | Keep |
| E | Rangoli Days | 11:30 - 1:45 PM | 1:20 PM | Skip |
| F | Kalki Chronicles | 1:30 - 4:00 PM | 1:20 PM | Keep |
| G | Chai & Chaos | 4:30 - 6:35 PM | 4:00 PM | Keep |

Result: 4 movies (A, D, F, G).

## Features

- Login page (demo only, any valid email and a password of 4+ characters, or guest)
- BookMyShow-style home: poster grid, search, genre filter, movie detail card with rating and showtimes
- **Day planner** (the extra feature): pick a date, see every show, tap times to select; clashing shows grey out; a timeline bar shows the day
- **Auto-pick maximum** using the greedy algorithm
- **Algorithm visualizer:** animated walk-through showing each keep or skip
- **Must-watch pin:** the best day that still includes one favourite movie
- Free-time window, gap between shows, and presets (Max movies, Relaxed day, Night owl)
- **Strategy comparison chart:** earliest finish vs earliest start vs shortest movie, computed on the same shows
- Booking: seat map, snacks, promo code `HACK20` (20% off), QR-style tickets, booking history
- Dark mode and responsive layout

### Must-watch pin: how it works

For each show of the pinned movie, run the greedy algorithm on the free time before it and the free time after it, then keep the show that gives the largest total. The result is optimal among plans that contain one show of the pinned movie (when repeats are allowed). The "don't repeat the same movie" option is a heuristic: that constraint makes the general problem harder (it relates to the job interval selection problem), so only the plain version is proven optimal.

## Tech stack

HTML, CSS and vanilla JavaScript in a single file (`cinebook.html`). No frameworks and no backend. Show data is sample data embedded in the file.

## Limitations

- Prototype with sample data; login and payment are simulated.
- No seat locking or real theatre feeds.
- Travel time between theatres is approximated by the gap setting.

## Future scope

- Weighted selection (maximise ratings or enjoyment) with dynamic programming
- Real travel times between theatres
- Live theatre data, seat locking and a payment gateway
- Group planning for friends with different free windows

## Repository contents

| File | Purpose |
|---|---|
| `cinebook.html` | The complete working app |
| `README.md` | This documentation |
| `CineBook_Pitch.pptx` | Presentation slides |
