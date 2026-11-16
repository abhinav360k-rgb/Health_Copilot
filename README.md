# Health Copilot

**Offline health self-monitoring for astronauts on the way to Mars.**
NASA Space Apps Challenge 2026 — *Create Health Monitoring Software for Astronauts on Space Missions*

> Prototype decision-support tool. All crew health data is **simulated**. Not a medical device.

Live demo: `https://YOUR-USERNAME.github.io/health-copilot/` ← replace with your link

## The problem

On a Mars transit, a question to Earth and its answer can be more than 20 minutes apart. Astronauts have to notice changes in their own health and decide what to do without a doctor in the loop. Fixed thresholds ("heart rate above 100") miss the slow drifts that matter on a long mission, and an alarm without an explanation or a next step is not useful to someone who has to act alone.

## The solution

Health Copilot runs entirely on the astronaut's device and does three things:

1. **Learns each person's own normal** from 30 pre-flight days, for 13 indicators (HRV, resting heart rate, SpO2, sleep duration and efficiency, reaction time and lapses, mood, fatigue, stress, exercise, skin temperature, body mass).
2. **Detects gradual drift** away from that personal baseline, and explains every flag in plain language ("HRV has averaged 26% below your personal baseline for 3 days").
3. **Says what to do now** using a five-row decision table that anyone can read: nominal → monitor → repeat measurement → self-care action → contact flight surgeon. It also writes a compact Earth report and shows the signal delay.

A daily check-in (a reaction-time test plus mood, fatigue and stress ratings) feeds the same system.

## How it works

- **Baseline:** mean and standard deviation of the 30 pre-flight days, per person, per indicator.
- **Drift detection:** each day's value becomes a z-score against the personal baseline. An exponentially weighted moving average (EWMA, weight 0.25) accumulates small sustained shifts. An indicator is "beyond its limit" when the EWMA passes 1.13 in the harmful direction, which is the standard 3-sigma EWMA control limit (Roberts, 1959).
- **Triage rules** (shown in the app under "How it works" and highlighted on every alert):

| # | When | Then |
|---|------|------|
| 1 | A fixed safety limit is crossed (SpO2 < 92%, resting HR > 100 bpm, skin temperature ≥ 1.5 °C over baseline) | Contact flight surgeon |
| 2 | 3+ indicators beyond personal limits for 3+ days across 2+ body systems, or one strong deviation for 7+ days | Self-care action matched to the pattern, repeat test, Earth report. Escalates to rule 1's action if unchanged after 10 days |
| 3 | 1–2 indicators beyond limits for 3+ days | Repeat measurement |
| 4 | Any indicator beyond its limit for 1–2 days | Monitor |
| 5 | None of the above | Nominal |

- **Offline:** no server, no build step, no dependencies. Check-ins and the last space-weather reading are stored in the browser's local storage.

## Data

**Simulated crew data.** A seeded generator in `index.html` creates a fictional 4-person crew, 30 pre-flight days and a 250-day transit, with day-to-day noise and three injected scenarios:

| Crew member | Scenario | Days |
|---|---|---|
| Chen | Sleep disruption and fatigue; recovers after following the recommendation | 134–148 |
| Okafor | Skipped exercise leading to cardiovascular deconditioning | 150–215 |
| Ramirez | Gradual behavioral health decline | 185–235 |
| Lindqvist | Nominal (control) | — |

**Real NASA data.** The Crew overview reads solar flare (FLR) and solar energetic particle (SEP) events for the last 30 days from **NASA DONKI** (Space Weather Database Of Notifications, Knowledge, Information) through `api.nasa.gov`. We chose DONKI because radiation from solar storms is one of the hazards named in the challenge, the API is open and works from a browser, and a storm warning is something a crew can act on (shelter). The result is cached so it stays visible offline.

**NASA measurements used as parameters.** Cruise radiation dose rate of 1.84 mSv/day from the RAD instrument on Mars Science Laboratory (Zeitlin et al., 2013), and the 600 mSv career limit from NASA-STD-3001.

## Run it

Open `index.html` in any modern browser. That is the whole install.

To deploy: push `index.html` to a GitHub repository and enable GitHub Pages (Settings → Pages → deploy from `main`).

The DONKI panel uses NASA's shared `DEMO_KEY`, which is rate-limited. For reliable use, get a free key at api.nasa.gov and replace `DEMO_KEY` near the top of the second `<script>` block.

## Demo

Press **Play demo**. Day 132: Chen is nominal. Her sleep shortens, HRV falls and reaction-time lapses rise. Health Copilot starts watching on day 137, asks for a repeat measurement on day 139 and recommends a sleep and light schedule change on day 140. It sends an Earth report, and by day 143 the trend is recovering.

## Limitations

- Not a diagnostic device. Thresholds and guidance have not been clinically validated; a flight version would need protocols approved by flight surgeons.
- The simulated data is simpler than real physiology: no sensor dropouts, no day-to-day correlation, no normal adaptation to microgravity (for example, astronauts on the ISS typically sleep about 6 hours, less than on Earth; Barger et al., 2014).
- Recovery in the demo is scripted; it assumes the crew member follows the advice.
- The baseline is fixed at pre-flight values. A real system would re-baseline in flight.
- The reaction test is a 30-second version of a psychomotor vigilance test and depends on the device's input lag.
- Bone/muscle forecasting, voice and hand-steadiness tests were out of scope for the hackathon.

## Sources

Please check each link before relying on it.

- NASA DONKI: https://ccmc.gsfc.nasa.gov/tools/DONKI/ and https://api.nasa.gov/
- Zeitlin, C. et al. (2013). Measurements of energetic particle radiation in transit to Mars on the Mars Science Laboratory. *Science* 340, 1080–1084.
- NASA-STD-3001, Volume 1: Crew Health (space-permissible exposure limit for radiation, 600 mSv).
- Basner, M. & Dinges, D. F. (2011). Maximizing sensitivity of the psychomotor vigilance test (PVT) to sleep loss. *Sleep* 34(5), 581–591. (Lapse = response ≥ 500 ms.)
- Barger, L. K. et al. (2014). Prevalence of sleep deficiency and use of hypnotic drugs in astronauts before, during, and after spaceflight. *Lancet Neurology* 13, 904–912.
- Roberts, S. W. (1959). Control chart tests based on geometric moving averages. *Technometrics* 1, 239–250.
- NASA Human Research Program, "5 Hazards of Human Spaceflight".

## Use of AI

The application code, this README and the submission text were drafted with an AI assistant (Claude, by Anthropic) under the team's direction. The team chose the concept and scope, tested the app, and reviewed the output. No AI-generated images, video or audio are used. No NASA logos or branding are used.
