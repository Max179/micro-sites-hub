# Sleep Cycle Calculator — Promotion Copy (2026-07-04)

Live site: https://Max179.github.io/site-20260704-sleep-cycle-calculator

## Reddit (r/sleep)

**Title:** I built a free sleep cycle calculator that tells you the best time to go to bed

**Body:**
I kept waking up groggy even after 8 hours of sleep and finally figured out why — my alarm was going off in the middle of a deep-sleep cycle. So I built a simple, ad-light tool that counts in 90-minute cycles (the natural length of a full sleep cycle) and tells you either:

- The best bedtimes if you need to wake up at a specific time
- The best wake-up times if you're going to sleep right now

Each option shows how many cycles and total sleep you'll get. It adds a 15-minute buffer for the time it takes to fall asleep. No signup, no tracking, works on mobile.

Try it here: https://Max179.github.io/site-20260704-sleep-cycle-calculator

Curious if anyone else here uses cycle-based sleep timing — does it actually work for you?

---

## X / Twitter

Waking up groggy after 8 hours of sleep? Your alarm probably fired mid-cycle. Built a tiny free tool that picks bedtimes & wake times based on 90-minute sleep cycles. No signup. Works on mobile. 🌙💤

https://Max179.github.io/site-20260704-sleep-cycle-calculator

#sleep #sleephygiene #productivity #buildinpublic

---

## Indie Hackers

**Title:** Day 2 of "ship one micro-site a day" — a sleep cycle calculator

**Body:**
Yesterday I shipped a reading-time calculator. Today I'm back with another tiny tool: a sleep cycle calculator.

The problem: people wake up tired even after a full night's sleep because their alarm interrupts a deep-sleep phase. Sleep runs in ~90-minute cycles, and waking at the boundary between two cycles feels way better than waking mid-cycle.

What it does:
- Mode 1: enter the time you want to wake up → get 4 suggested bedtimes
- Mode 2: tap calculate → get 4 suggested wake-up times from right now
- Adds a 15-min buffer for falling asleep
- Highlights the option closest to the recommended 7.5h

Tech: plain HTML/CSS/JS, deployed on GitHub Pages. No framework, no backend, ~1KB of logic. Building for AdSense monetization once traffic grows.

Live: https://Max179.github.io/site-20260704-sleep-cycle-calculator

What's your "waking up tired" hack? Anything I should add to the calculator?

---

## Hacker News (Show HN)

**Title:** Show HN: Sleep Cycle Calculator – Bedtimes and wake times in 90-min cycles

**Body:**
Hi HN, I built a minimal sleep cycle calculator: https://Max179.github.io/site-20260704-sleep-cycle-calculator

It does one thing — given a target wake time (or "now"), it returns 4 bedtimes/wake times that land on the boundary between two 90-minute sleep cycles, so you wake up at the lightest possible moment. Adds a 15-minute falling-asleep buffer.

Why: most existing calculators are buried in ads or force a 30-second scroll. This is one screen, no signup, works offline-ish (everything client-side, ~3KB of JS).

The science is approximate — sleep cycle length varies between 80–110 minutes, and falling-asleep time varies per person. The 90/15 defaults are the most commonly cited averages from the National Sleep Foundation.

Feedback welcome, especially from anyone who's actually tracked their cycles with an EEG or wearable.
