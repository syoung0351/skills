---
name: endomap-scoring
description: >
  Conduct an endometriosis symptom assessment and generate an interactive HTML
  artifact showing the user's symptom burden mapped onto a human body figure
  (the "endomap" model). Always use this skill when a user asks to track
  endometriosis or endo symptoms, assess period pain, log a pain diary, create
  a symptom body map, or understand their endometriosis symptom patterns across
  pelvic pain, bleeding, bloating, fatigue, or mental wellbeing. Produces a
  single self-contained HTML artifact with 8 symptom dimensions, 32 slider
  questions, and a live SVG body visualization that updates in real time.
---

# Endomap: Endometriosis Symptom Scoring

Endomap is a self-report symptom tracking tool for people living with endometriosis. It maps 8 measurable symptom dimensions onto regions of a human body figure, turning subjective scores into a clear visual snapshot of symptom burden across body systems.

## CRITICAL: Read the Template First

Before writing any HTML, read `templates/endomap-viewer.html`. Use that file as the **literal starting point** for the artifact. Do not generate HTML from scratch. The template contains the correct layout, CSS variables, font imports, SVG body figure, and JavaScript scoring engine. Output it nearly verbatim as the artifact — do not restructure it.

**Fixed (never modify):**
- CSS custom properties (`--anthropic-*` variables)
- `.container` flex layout (sidebar + main area)
- `.sidebar` width and styling
- Poppins and Lora font imports
- `.btn`, `.control-section` classes
- Reset button behavior
- Medical disclaimer text

**Variable (already correctly filled in the template):**
- SVG body figure paths and region IDs
- 8 `<details>` sections with range sliders
- JavaScript scoring engine (`DIMS`, `recalc`, `scoreColor`)
- Title ("Endomap") and subtitle
- Dimension summary cards and overall score

## The Endomap Body Model

Endometriosis affects many body systems simultaneously. Each region of the endomap figure represents a distinct symptom dimension:

| Region | Dimension | What It Captures |
|--------|-----------|------------------|
| Head | Mental & Emotional Wellbeing | Anxiety, depression, brain fog, emotional impact on quality of life |
| Shoulders / Upper chest | Shoulder & Chest Pain | Diaphragmatic endo, right shoulder tip pain, chest tightness during cycle |
| Upper abdomen | Bloating & Nausea | "Endo belly", nausea, GI cramping, bowel irregularity |
| Lower abdomen / Pelvis | Pelvic Pain | Dysmenorrhea, chronic pelvic pain, dyspareunia, ovulation pain |
| Uterine overlay (mid-pelvis) | Bleeding | Heavy flow, clot passage, prolonged bleeding, spotting |
| Flanking bowel/bladder zones | Bowel & Bladder | Dyschezia, dysuria, urgency, IBS-like symptoms |
| Legs | Back & Leg Pain | Lower back, sciatic-type radiating pain, hip pain, numbness/tingling |
| Torso overlay | Fatigue & Sleep | Exhaustion unrelieved by rest, insomnia, energy depletion |

Color intensity encodes severity: a barely visible tint for minimal symptoms, deep saturated color for severe. The fatigue dimension applies a subtle golden wash across the entire torso at high scores.

## The 32 Assessment Questions

All questions use a 0–10 scale (0 = not present / not at all, 10 = worst possible / constant).

### Dimension 1: Pelvic Pain
1. Average pelvic or lower abdominal pain in the past 30 days
2. Pain during or after sex (dyspareunia)
3. How often does pelvic pain interfere with daily activities
4. Ovulation pain (mid-cycle cramping or pain)

### Dimension 2: Bowel & Bladder Symptoms
5. Pain or cramping during bowel movements (dyschezia)
6. Urgency, frequency, or pain when urinating (dysuria)
7. Diarrhea or constipation episodes — especially around your period
8. How much do bowel or bladder symptoms disrupt your daily life

### Dimension 3: Bleeding
9. Menstrual flow intensity (0 = very light, 10 = flooding / extremely heavy)
10. Duration of heavy bleeding days (0 = 1 day or less, 10 = 7+ days)
11. Spotting or bleeding between periods
12. Passage of large blood clots

### Dimension 4: Bloating & Nausea
13. Severity of abdominal bloating ("endo belly")
14. Nausea during or around your period
15. Stomach cramping unrelated to menstrual periods
16. How much does bloating or nausea affect eating or daily comfort

### Dimension 5: Back & Leg Pain
17. Lower back pain or sacral / hip pain
18. Radiating leg pain, numbness, or tingling (sciatic-type)
19. Hip stiffness or pain with movement
20. How much does back or leg pain limit your mobility

### Dimension 6: Shoulder & Chest Pain
21. Right shoulder tip pain — especially around your period
22. Chest pain or tightness during menstruation
23. Pain that worsens with deep breathing during your cycle
24. How much does shoulder or chest pain worry or affect you

### Dimension 7: Fatigue & Sleep
25. Overall fatigue level that isn't relieved by rest
26. Difficulty falling or staying asleep (insomnia)
27. How often does fatigue stop you from doing things you want to do
28. Your energy compared to what feels normal for you (0 = full energy, 10 = no energy at all)

### Dimension 8: Mental & Emotional Wellbeing
29. Anxiety related to your condition
30. Low mood or feelings of depression
31. Brain fog or difficulty concentrating
32. Overall emotional impact of your condition on your quality of life

## Scoring Algorithm

**Per-dimension average**: sum of that dimension's 4 slider values ÷ 4.

**Color intensity**: `alpha = 0.06 + (score / 10) * 0.84`
- Score 0 → barely visible tint (alpha ≈ 0.06)
- Score 10 → full deep saturation (alpha ≈ 0.90)
- Fatigue overlay is capped lower: `alpha = (score / 10) * 0.32`

**Severity bands**:
- 0.0–1.9: Minimal
- 2.0–3.9: Mild
- 4.0–5.9: Moderate
- 6.0–7.9: Significant
- 8.0–10.0: Severe

**Overall burden score**: sum of all 8 dimension averages (0–80).
- 0–15: Low overall burden
- 16–30: Mild–moderate burden
- 31–50: Moderate–significant burden
- 51–65: High burden
- 66–80: Very high burden

## SVG Body Region Color Scheme

| Dimension | Region ID(s) | Base RGB |
|-----------|-------------|----------|
| Pelvic Pain | `region-pelvic` | 201, 74, 0 (deep orange-red) |
| Bowel & Bladder | `region-bowel-l`, `region-bowel-r` | 179, 90, 0 (amber-brown) |
| Bleeding | `region-bleeding` | 168, 16, 48 (crimson) |
| Bloating & Nausea | `region-gi` | 45, 122, 45 (forest green) |
| Back & Leg Pain | `region-leg-l`, `region-leg-r` | 26, 95, 168 (dark blue) |
| Shoulder & Chest | `region-shoulder-l`, `region-shoulder-r`, `region-chest` | 106, 31, 168 (deep violet) |
| Fatigue & Sleep | `region-fatigue` | 138, 122, 0 (dark gold, torso overlay) |
| Mental & Emotional | `region-mental`, `region-mental-neck` | 26, 106, 138 (deep teal) |

## Output

Read `templates/endomap-viewer.html` and output it as a self-contained HTML artifact. The artifact works immediately in the claude.ai artifact viewer with no modifications needed.

After the user has interacted with the assessment and shares their scores, offer a brief (3–5 sentence) supportive interpretation of their symptom pattern. Use validating, non-judgmental language that acknowledges the complexity and whole-body nature of endometriosis. **Always close with:** *"This tool is for self-tracking and symptom awareness only. Please consult a qualified healthcare provider for evaluation and care."*

Do not ask the user questions conversationally before presenting the artifact. Output the artifact immediately.
