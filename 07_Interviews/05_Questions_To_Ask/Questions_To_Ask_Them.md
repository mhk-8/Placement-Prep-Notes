
# Questions To Ask Them

> **Why it matters.** "Do you have any questions for us?" is not a formality. It is the last thing
> they hear from you, it signals how seriously you took the conversation, and — genuinely — it is
> the only part of the interview where *you* gather the information you need to choose.
>
> **The rule ⭐⭐⭐:** prepare five, ask two or three, and adapt them to what the interviewer
> actually said. A question that references something from the preceding hour is worth more than
> any pre-written one.

---

## 1. The structure ⭐⭐

```
Have FIVE ready, in three categories:
  2 about THE WORK        — what you would actually do
  2 about THE TEAM/ENGINEERING — how they operate
  1 about GROWTH          — where this leads

Ask 2-3. Watch the interviewer's time; if the round is running over, ask one and say
"I had more but I'm conscious of time."  ⭐ that itself reads well.
```

⚠️ **Never say "no, I don't have any questions."** It reads as disengagement, and it is the single
most avoidable negative signal in the whole process.

---

## 2. The best question to ask ⭐⭐⭐

If you only ask one, ask something like:

> **"What's the hardest problem the team is working on right now?"**

Why it is the strongest:
```
- It is genuinely interesting and the answer tells you what the work is really like
- Engineers enjoy answering it, so it ends the interview on a good note
- It surfaces whether the work is actually hard or mostly maintenance
- It gives you material for a follow-up question, and for a thank-you note
- It cannot be answered from the careers page, so it does not look lazy
```

---

## 3. Questions about the work ⭐⭐

```
□ "What would my first three to six months look like?"
□ "What does a typical week look like for someone in this role?"
□ "What's the hardest problem the team is working on right now?"              ⭐⭐⭐
□ "What's the split between building new things and maintaining existing ones?"
□ "How much of the work is independent versus collaborative?"
□ "What does success look like for someone in this role after a year?"
□ "What's something about this team that surprised you when you joined?"      ⭐ great question
□ "Is there something the team has been wanting to do but hasn't had the bandwidth for?"
```

---

## 4. Questions about engineering practice ⭐⭐

These tell you a great deal about what working there is actually like.

```
□ "What does your code review process look like?"
□ "How do you approach testing? What's the balance between unit, integration and end-to-end?"
□ "How often do you deploy, and what does the release process look like?"
□ "How do you handle on-call? How noisy is it?"                               ⚠️ important
□ "How do you decide when to take on technical debt versus pay it down?"
□ "What's the tech stack, and is there anything you'd change about it?"
□ "How do design decisions get made — RFCs, design docs, discussion?"
□ "What happens when something goes wrong in production? Is there a blameless postmortem culture?"
```

⭐ The on-call and postmortem questions are especially revealing. A team with a healthy answer to
both is usually a healthy team.

---

## 5. Questions specific to YOUR profile ⭐⭐⭐

These are the ones that will make you memorable, because they only make sense coming from you.

### For systems / HPC / GPU roles
```
□ "How much of the performance work is profiling-driven versus based on architectural
   knowledge? I've been doing the former with Nsight and I'm curious how that scales up."   ⭐
□ "Do engineers here work across the stack — kernel to framework — or is it more specialised?"
□ "What hardware generations are you targeting, and how much does the code have to change
   between them?"
□ "How do you decide when a workload is worth moving to a GPU? I've been working on one
   that's a genuinely bad fit and it's made me curious how that call gets made in practice."  ⭐⭐
```

### For compiler / static analysis roles
```
□ "How much of the work is analysis versus transformation?"
□ "How do you handle the soundness-versus-precision trade-off in practice — is there a
   house position, or is it decided per analysis?"                              ⭐
□ "Do you use an existing IR, or is there an internal one?"
□ "How do you test compiler changes? Differential testing, fuzzing, benchmark suites?"
```

### For ML / ML-infrastructure roles
```
□ "Where's the bottleneck in your training pipeline right now — data, compute, or
   communication?"                                                              ⭐⭐
□ "How do you evaluate models before they ship? Is there an offline/online gap you fight with?"
□ "How much of the ML work is modelling versus data and infrastructure?"        ⭐ the honest
   answer to this tells you what the job really is
□ "Do you build on top of the frameworks, or do you write custom kernels when you need to?"
```

### For any role, given your background
```
□ "I came into CS from mechanical engineering, so I've had to learn a field from scratch once
   already. How does the team onboard someone into an unfamiliar codebase or domain?"   ⭐
   — turns your unusual background into a natural, non-defensive question
```

---

## 6. Questions about growth ⭐

```
□ "What does the path from here look like for someone who wants to stay technical?"
□ "Is there mentorship, formal or informal?"
□ "How do people move between teams if their interests change?"
□ "Does the team publish, present, or contribute to open source?"
□ "What's the most common reason people leave this team?"    ⭐⭐ brave and very informative
```

---

## 7. Questions to ask an HR interviewer (different from technical) ⭐

```
□ "How would you describe the culture in a way that would actually surprise an outsider?"
□ "What kind of person does well here, and what kind of person struggles?"      ⭐
□ "What does the onboarding process look like for a fresh graduate?"
□ "How is performance reviewed, and how often?"
□ "What's the team composition — how many people, what seniority mix?"
```

---

## 8. What NOT to ask ⚠️⭐⭐⭐

```
IN ROUND ONE, NEVER:
  ✗ Salary, bonus, stock, or any compensation question
  ✗ Leave policy, working hours, work-from-home policy
  ✗ "How did I do?"  — puts them in an awkward position
  ✗ "When will I hear back?" as your ONLY question (fine as an afterthought at the very end)
  ✗ "What does your company do?"  — you should know. This is the worst possible question ⚠️

EVER:
  ✗ Anything answered on the first page of their careers site or their Wikipedia page
  ✗ "Is there a lot of pressure here?" — reads as pre-emptive complaining
  ✗ A question that is really a statement about how good you are
  ✗ More than three questions when the interviewer is clearly out of time
```

⭐ **Compensation and logistics are appropriate at the OFFER stage**, with HR, not with a technical
interviewer in round one. Asking then is normal and expected; asking now is not.

---

## 9. Adapting in the moment ⭐⭐⭐

The strongest questions come from the interview itself. Listen for openings:

```
They said: "We've been migrating a lot of our pipeline recently."
  → "You mentioned a migration — what's driving it, and what's been the hardest part?"

They said: "The team is quite small."
  → "With a small team, how do you decide what not to do?"

They asked you a question about distributed systems you did not expect.
  → "I noticed you asked about distributed systems — is that a big part of this role?
     I'd like to understand where the emphasis actually is."   ⭐ turns a stumble into interest

They mentioned a specific technology.
  → "You mentioned <X> — was that a deliberate choice over <Y>, and how has it worked out?"
```

⭐ **Take one note during the interview specifically for this.** One word is enough to trigger a
question later, and referencing something they said proves you were listening.

---

## 10. Before each interview ⭐⭐

```
□ Read their engineering blog. Find ONE recent post and have a question about it.
□ Look up your interviewer on LinkedIn if you were given their name — a question about
  their own path is legitimate and often well received
□ Write your five questions in a notebook and have it visible.  ⭐ Looking at a prepared
  list is a POSITIVE signal, not a negative one — it shows preparation
□ Pick which two you will ask, and which one you will drop if time is short
```

---

## 11. The close ⭐

After your questions:

> "Thank you — that was helpful. This sounds like the kind of work I want to be doing, and I'd be
> glad to go further in the process."

Brief, warm, and states interest without being effusive. Then stop.

⚠️ Do **not** ask "do you have any concerns about my candidacy?" It sounds like a technique and it
puts the interviewer on the spot. (Some advice recommends it; in campus interviews it lands badly
more often than it helps.)

---

## Recall questions

1. How many questions do you prepare, and how many do you ask?
2. What is the single strongest question, and why?
3. Name two questions that are specific to your background and would not work for anyone else.
4. What must you never ask in round one, and when does it become appropriate?
5. Why is reading from a prepared list a positive signal?
6. What should you do during the interview to generate a better question?
