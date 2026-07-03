# Promotion Copy - 20260703

**Site:** Caffeine Half-Life Calculator
**URL:** https://Max179.github.io/site-20260703-caffeine-half-life-calculator/

---

## Reddit (r/coffee, r/sleep, r/productivity)

**Title:** I built a free caffeine half-life calculator so you know when it's safe to sleep after coffee

**Body:**

Hey everyone,

I kept wrecking my sleep with afternoon coffee without realizing how much caffeine was still in my system at bedtime, so I built a simple free tool:

https://Max179.github.io/site-20260703-caffeine-half-life-calculator/

You enter:
- What you drank (coffee, espresso, energy drink, tea) and the mg
- The time you had it
- Your planned bedtime
- Your metabolism (fast / average / slow, ~3 / 5 / 7 h half-life)

It tells you how many mg are left at bedtime, whether that's likely to disrupt sleep, and the exact clock time caffeine drops below the sleep-safe 50 mg threshold.

Useful for anyone who works late, studies, or does shift work. No signup, mobile-friendly.

Would love feedback — anything you'd add? (Considering a "reverse mode": enter bedtime, get the latest safe coffee time.)

---

## X (Twitter)

Drink coffee at 4pm and wonder why you can't sleep? Built a free Caffeine Half-Life Calculator — see how many mg are left at your bedtime and the exact time it's safe to sleep.

https://Max179.github.io/site-20260703-caffeine-half-life-calculator/

#coffee #sleep #caffeine #productivity

---

## Indie Hackers

**Title:** Day 1 of building a micro-site a day — today: a caffeine half-life calculator

I'm trying an experiment: ship one lightweight, genuinely useful web tool every day and grow them toward AdSense. No frameworks, no build step — just HTML/CSS/JS deployed to GitHub Pages.

Today's site solves a problem I personally hit: drinking afternoon coffee and then lying awake wondering how much caffeine is still in my system.

https://Max179.github.io/site-20260703-caffeine-half-life-calculator/

It uses the standard exponential decay model (dose x 0.5^(elapsed / half-life)) with a 3 / 5 / 7-hour half-life toggle for fast / average / slow metabolizers, and shows the exact time caffeine drops below the sleep-safe 50 mg threshold.

Tech: vanilla JS, shared SEO / analytics / ads modules I reuse across sites, JSON-LD structured data for Google. The template system took a day to set up; now each site is mostly content plus one calculate() function.

Tomorrow: another small pain point. Open to ideas.

---

## Hacker News (Show HN)

**Title:** Show HN: Caffeine Half-Life Calculator – when is it safe to sleep after coffee

Hi HN,

I built a small, dependency-free tool that estimates how much caffeine is still in your body at bedtime and when it drops below the level that tends to disrupt sleep (~50 mg).

https://Max179.github.io/site-20260703-caffeine-half-life-calculator/

It uses the standard first-order decay model: remaining = dose x 0.5^(elapsed / half-life), with a half-life of ~3 / 5 / 7 hours depending on whether you're a fast / average / slow metabolizer (CYP1A2 genetics). The 5-hour average is the commonly cited population mean.

The motivation: people know "don't drink coffee late" but not where their personal cutoff is. A 95 mg coffee at 4pm leaves ~40 mg at 11pm for an average metabolizer — borderline. For a slow metabolizer it's ~60 mg — likely to hurt sleep.

Pure HTML/CSS/JS on GitHub Pages. Feedback welcome, especially on the half-life ranges and the 50 mg sleep threshold — I pulled those from sleep-research summaries and would appreciate sharper citations.
