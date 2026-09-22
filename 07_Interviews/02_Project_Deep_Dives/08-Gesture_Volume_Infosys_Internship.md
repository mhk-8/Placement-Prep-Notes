
# Experience: Gesture Volume — INFOSYS Springboard Virtual Internship

> **Oct 2025 – Dec 2025** · **Track:** ML/CV (appears on the ML and master resumes)
> **Priority: ⭐ — handle honestly, do not oversell**

**The honest framing you must get right ⚠️⭐⭐⭐**

This is a **virtual internship** — a structured self-paced programme, not an industry placement
with a team, a codebase and a manager. Interviewers know what Infosys Springboard is. Describing it
as "industry experience" is the fastest way to lose credibility in an otherwise strong interview.

```
❌ "I interned at Infosys, where I worked on a computer vision product."
✅ "It was Infosys Springboard's virtual internship — a self-directed project programme rather
    than a placement on a team. I built a gesture-based volume controller. It's the smallest
    thing on my resume, but it's the one piece where I had to make something work end-to-end
    for a user rather than for a grader."
```

⭐ The honest version is *more* persuasive, because it tells the interviewer you can calibrate your
own claims — and the "end-to-end for a user" framing gives the project a genuine, defensible angle.

---

## 1. The 20-second version

> "A non-contact volume controller — MediaPipe hand-landmark detection mapped to system audio
> through the Windows Core Audio API, with a Streamlit dashboard."

## 2. The 60-second version

> "The system reads the webcam, detects 21 hand landmarks per frame with MediaPipe, and maps the
> hand pose to a system volume level through Pycaw, which wraps the Windows Core Audio API. There's
> a Streamlit dashboard showing the detection and the current level live.
>
> I built two recognition modes: one maps the distance between thumb and index finger to a
> continuous volume, and the other is a discrete finger-count classifier. The one real problem
> worth mentioning is that raw pixel distance between two landmarks changes as you move toward or
> away from the camera — the same gesture gives a different reading at different distances. I
> normalised using the 3-D landmark coordinates against a reference hand dimension, which makes the
> mapping scale-invariant, so the gesture means the same thing regardless of how far you are from
> the camera.
>
> It's a small project, but it's the one where latency mattered — if there's perceptible lag
> between the gesture and the volume change, the whole thing feels broken regardless of how
> accurate the detection is."

---

## 3. The technical content ⭐

| Component | What it does |
|---|---|
| **MediaPipe Hands** | Palm detector + landmark model; outputs **21 3-D landmarks** per hand with `(x, y, z)`, where `x,y` are normalised to the image and `z` is depth relative to the wrist |
| **OpenCV** | Frame capture, colour conversion (⚠️ BGR ↔ RGB — MediaPipe expects RGB), drawing overlays |
| **Pycaw** | Python wrapper over the Windows Core Audio API; sets the master volume scalar |
| **Streamlit** | Live dashboard for the video feed and current level |

**The scale-invariance problem and fix ⭐⭐** — this is the one genuinely interesting technical point:
```
PROBLEM : pixel distance(thumb_tip, index_tip) shrinks as the hand moves away from the camera,
          so the same physical gesture maps to a different volume.

FIX     : normalise by a reference length that scales the same way — e.g. the wrist-to-middle-
          knuckle distance, or use the 3-D coordinates so depth is accounted for.
          ratio = d(thumb, index) / d(reference)   → scale-invariant

Then map the ratio to [0, 100] volume, with clamping at both ends.
```

**Smoothing ⭐:** raw per-frame landmark estimates jitter. An exponential moving average
(`v ← αv_new + (1−α)v_prev`) or a small median filter over recent frames removes the flicker. If
you did this, say so; if you did not, it is a good "what would you improve" answer.

---

## 4. Anticipated follow-ups ⭐

<details><summary>"How does MediaPipe hand detection actually work?"</summary>

A two-stage pipeline: a **palm detector** (a single-shot detector, since palms are more rigid and
easier to bound than full hands) locates the hand and produces a crop, then a **landmark regression
model** predicts 21 keypoints on that crop. Across frames it tracks rather than re-detecting, which
is what makes it real-time on CPU. Be honest that you used it as a library — the interesting part
is knowing *why* it is two-stage.
</details>

<details><summary>"What was the latency, and what dominated it?"</summary>

Give the structure even if you do not have exact figures: frame capture, then inference, then the
audio API call, then the UI update. On a webcam pipeline the capture rate (typically 30 fps = 33 ms
per frame) usually sets the floor, and MediaPipe's landmark model is fast enough on CPU to fit
inside that. ⚠️ Do not invent a millisecond number.
</details>

<details><summary>"How would you make this robust?"</summary>

Temporal smoothing of the landmarks; a confidence threshold so low-quality detections are ignored
rather than acted on; hysteresis on the finger-count classifier so it doesn't flicker between
states at the boundary; handling multiple hands or none; and testing under varied lighting and
backgrounds, which is where these systems actually break.
</details>

<details><summary>"Why Streamlit?"</summary>

Speed of building a visual interface with no front-end work — appropriate for a demo. For anything
real it is the wrong choice: Streamlit re-runs the whole script on interaction, which fights a
continuous video loop. A small web front-end over a WebSocket, or just an OpenCV window, would be
better. ⭐ Knowing why your own tool choice was expedient rather than correct is a good signal.
</details>

<details><summary>"What did you actually learn?"</summary>

The honest and useful answer: that for an interactive system, **perceived** latency and stability
matter more than model accuracy. A detector that is 2% more accurate but adds 100 ms of lag makes
the product worse. That is a genuinely different lesson from anything my coursework taught, and it
is why the project is worth mentioning despite its size.
</details>

---

## 5. Limitations to state proactively

```
- Windows-only, because Pycaw wraps the Windows Core Audio API.
- Single hand, controlled lighting, close range; no evaluation under varied conditions.
- No quantitative evaluation at all — no measured accuracy, no measured latency. ⭐
- MediaPipe does the hard part; my contribution is the mapping, normalisation and integration.
- Virtual internship, not an industry placement.
```

⭐ That third point — no quantitative evaluation — is worth volunteering, because it contrasts
nicely with your IR project, where you did statistical significance testing. It shows you know the
difference between a demo and an evaluated system.

---

## 6. Where this fits in your narrative ⭐⭐

Use this project for exactly two purposes:

1. **It is the "experience" line on your ML resume.** When asked about work experience, this is
   what you have — present it accurately and move quickly to your projects, which are stronger.
2. **It is your only user-facing, latency-sensitive, end-to-end system.** Every other project is
   evaluated by a metric. This one is evaluated by whether it feels right. That is a genuinely
   different skill and worth one sentence in a behavioural answer about shipping something.

⚠️ See `../04_HR_and_Behavioral/04-Difficult_Questions.md` for how to handle "you have no industry
internship" — which is the real question this project is adjacent to, and which needs a different
answer than this project can carry alone.

---

## 7. The 30-second refresh

```
□ MediaPipe 21 landmarks → thumb-index distance / finger count → Pycaw → system volume
□ Streamlit dashboard; OpenCV capture (⚠️ BGR↔RGB)
□ The one real technical point: SCALE INVARIANCE via 3-D normalisation against a reference length
□ Latency, not accuracy, is what makes it feel right
□ Frame it as a virtual internship, honestly; it is your smallest item
□ Limitation to volunteer: no quantitative evaluation
```
