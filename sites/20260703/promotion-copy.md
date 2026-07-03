# Promotion Copy - 2026-07-03

**Site:** Caffeine Half-Life Calculator
**URL:** https://Max179.github.io/site-20260703-caffeine-half-life-calculator
**Repo:** https://github.com/Max179/site-20260703-caffeine-half-life-calculator

---

## 1. Reddit (r/coffee, r/sleep, r/productivity)

**Title:** I built a free caffeine half-life calculator so I could finally figure out when to stop drinking coffee

**Body:**

I love coffee, but for years I'd drink a cup at 3pm, lie awake at 11pm, and wonder why. The "don't drink coffee after 2pm" rule is everywhere, but it's way too blunt - a 95mg drip coffee and a 200mg pre-workout are very different things, and my metabolism isn't yours.

So I built a small free tool that does the actual math: you enter what you drank (or pick from common drinks), the time you finished it, and it tells you the earliest moment your caffeine drops below the ~25mg level that research links to sleep disruption.

The science it's based on:
- Caffeine has an average half-life of ~5 hours (faster in smokers, slower on oral contraceptives)
- It follows exponential decay: C(t) = C0 * (0.5)^(t / halfLife)
- ~25mg at bedtime is roughly where sleep quality starts dropping for most people

It shows you the exact safe-to-sleep time and how much caffeine is still in you right now. No signup, no ads in your face, works on mobile.

Try it: https://Max179.github.io/site-20260703-caffeine-half-life-calculator

Curious what time you'd need to cut off coffee to sleep at 10pm? For a standard 8oz drip it's around 12:30pm. What's your usual last cup and bedtime?

---

## 2. X (Twitter) - under 280 chars

Ever wonder if that 3pm coffee will wreck your sleep? Built a free Caffeine Half-Life Calculator - enter your drink + time, get the earliest moment you can sleep without caffeine interfering. ☕💤
https://Max179.github.io/site-20260703-caffeine-half-life-calculator

#caffeine #sleep #coffee #productivity #buildinpublic

---

## 3. Indie Hackers

**Title:** Day 1 of shipping a micro-site a day for AdSense - today: a caffeine half-life calculator

**Body:**

I'm trying an experiment: ship one small, genuinely useful web tool every day, each as its own site, and see if AdSense can make them pay for themselves.

Today's is a Caffeine Half-Life Calculator. The pain point is personal - I drink coffee late and never know if it'll trash my sleep. Existing advice ("no coffee after 2pm") ignores dose and individual metabolism. So the tool takes your caffeine amount, the time you drank it, your half-life (5h average, 3h if you smoke, longer on oral contraceptives), and a sleep-safe threshold, then computes the exact time caffeine falls below sleep-disrupting levels.

Stack: plain HTML/CSS/JS, no build step, deployed on GitHub Pages. Each site shares a small SEO/analytics/ads module so I can reuse infrastructure across all 30+ planned sites. The formula is just exponential decay solved for the threshold.

Live: https://Max179.github.io/site-20260703-caffeine-half-life-calculator

What single-purpose tools do you wish existed? Trying to collect pain points for the next few days.

---

## 4. Hacker News (Show HN)

**Title:** Show HN: Caffeine Half-Life Calculator – when can you sleep after coffee

**Body:**

Hi HN, I built a small tool that answers a question I kept googling poorly: if I drink X mg of caffeine at time Y, when is it safe to sleep?

It uses caffeine's exponential decay (average 5h half-life, adjustable for smokers ~3h or oral contraceptives ~7-10h) and solves C(t) = C0 * (0.5)^(t/halfLife) for the sleep-safe threshold (~25mg). You pick a drink or enter a custom mg amount, set the time you finished, and it returns the earliest bedtime that shouldn't be disrupted, plus how much caffeine is still active in you right now.

Nothing fancy - static HTML/JS on GitHub Pages, no tracking beyond GA, no signup. The interesting bit is that the common "no coffee after 2pm" heuristic is wildly off for a 200mg pre-workout vs a 30mg green tea, and most people don't realize caffeine's half-life varies ~3x between individuals.

https://Max179.github.io/site-20260703-caffeine-half-life-calculator

Source: https://github.com/Max179/site-20260703-caffeine-half-life-calculator

Feedback welcome, especially on the threshold default (25mg) and whether to add a "wake up at" reverse mode.
