# Finding the Foundry — Attendee Lab Guide ⚒️🌞

**Welcome, solar pioneer!** ☀️ You're a loan officer at **Highveld Renewables Finance**. Your job today: build **WattWorth ⚡**, an AI **agent** that decides whether to approve renewable-energy loans — fast, consistently, with a clear reason — and then **watch it think** with tracing.

You need **zero** prior Foundry experience. Every prompt below is copy-paste. If you're stuck for more than 2 minutes, raise your hand. 🙋

⏱️ You have ~40 minutes. Work as a team. Have fun.

---

## Before you start (2 min)

1. Your team has its own **Azure AI Foundry project**. Find your project URL and login on your team card.
2. Open the project in [https://ai.azure.com](https://ai.azure.com) and sign in.
3. Your **gpt-5-mini** model is **already deployed** and your project is **already connected to Application Insights** for tracing — no setup needed.
4. Download these two files (links from the facilitator):
   - `lending-policy.md` — you'll add this as a knowledge tool later.
   - `applicants.md` — your test customers.

> 🇿🇦 Everything is in South African Rand (R) and based on real-world SA context: load-shedding, SARS solar incentives, and National Credit Act affordability checks.

---

## 🔥 Spark One — Light the furnace (≈7 min)

**Goal: create your WattWorth agent and get your first reply.**

1. In your Foundry project, create a new agent. Click **Build** in the top right, then click **New agent** and select **Build and agent**.
2. Name the agent **WattWorth** and create it.
3. Your model **gpt-5-mini** is already deployed for you — make sure it's selected as the agent's model.
4. On the right, at the bottom of the screen, type the following in the chat box and send:

   ```
   In one sentence, what is a renewable-energy home loan?
   ```

✅ **Checkpoint:** Your agent replied. The furnace is lit. 🔥

---

## 🏦 Spark Two — Make it a banker (≈11 min)

**Goal: give the agent instructions so it returns a structured verdict.**

1. In your agent's setup panel, find the **Instructions** box.
2. Paste this:

   ```
   You are WattWorth, an affordability and risk analyst for Highveld Renewables Finance,
   a South African lender for renewable-energy home loans (solar PV, inverters, batteries,
   solar geysers, heat pumps).

   For each applicant you receive (as JSON), assess affordability and risk and reply with
   VALID JSON ONLY — no text before or after — using exactly this shape:

   {
     "applicant_id": "string",
     "verdict": "Green | Amber | Red",
     "max_affordable_instalment_zar": 0,
     "risk_flags": ["string"],
     "positive_factors": ["string"],
     "reason": "one short plain-language sentence for a loan officer"
   }

   Verdicts: Green = approve, Amber = refer for human review, Red = decline.
   All amounts are in South African Rand (ZAR). Be concise and consistent.
   ```

3. **Save** the agent.
4. Start a fresh chat with the agent, paste **applicant A001** (from `applicants.md`) and send. You should get JSON back.
5. Try **A002** and **A005**. Notice the agent is *guessing* the thresholds — it doesn't know your real lending rules yet. That's what Spark Three fixes.

✅ **Checkpoint:** WattWorth returns structured JSON verdicts. It talks like a banker — but it's improvising the rules.

> 💡 If it adds chit-chat around the JSON, add this line to the Instructions: *"Output must be parseable JSON. Do not include markdown code fences."*

---

## ☀️ Spark Three — Give it knowledge (≈12 min)

**Goal: attach your real lending policy as a knowledge tool so WattWorth stops guessing.**

1. In the agent setup, select **Tools** → **Upload files**.
2. **Upload** `lending-policy.md` and let the agent finish indexing it. Accept default settings.
3. Add this line to the **end** of the agent's **Instructions** so it leans on the policy:

   ```
   Apply ONLY the rules in the attached Highveld Renewables Finance lending policy.
   Use its exact thresholds (minimum income, affordability %, DTI %, credit tiers) and
   its exact risk-flag wording. If a rule is not in the policy, do not invent one.
   ```

4. **Save** the agent.
5. Start a fresh chat and re-run **A002** and **A005**. Compare to your Spark Two answers — the verdicts and flags should now match the policy's exact thresholds and wording.
6. Run the **full pack A001–A007** and record each verdict for the showcase.

✅ **Checkpoint:** WattWorth cites real policy thresholds and uses the policy's exact flag names. **You're fully charged.** ⚡

> 🔍 Sanity check a few: A004 should be declined (ineligible purpose), A006 should be declined (over the R350,000 limit), A001 should be approved.

---

## 🔍 Spark Four — Watch it think (≈8 min)

**Goal: use tracing to see what your agent actually did under the hood.**

Your project is already connected to **Application Insights**, so every agent run is traced.

1. With WattWorth, run applicant **A003** in a fresh chat.
2. Open **Traces** from the top menu (under **WattWorth**).
   - If your run isn't there yet, wait ~30–60 seconds and refresh — traces can lag.
3. Open the latest trace for your run and find:
   - the **knowledge / file_search tool call** — proof the agent read your policy,
   - the **model call** with its **token usage**,
   - the end-to-end **latency** (duration of the run).
4. Note **one number** — tokens *or* latency — to share at the showcase.

> 💡 Quicker view: in the agent's **Playground** pane you can also open a run's **Traces** (top right, left of Metrics) to see the same tool call without leaving the chat.

✅ **Checkpoint:** You can see WattWorth's steps — tool call, tokens, latency. You're not just trusting the answer; you can *prove* what the agent did. 🔍

---

## 🏁 Showcase (when facilitators call time)

Be ready to share:
- Your **verdicts for A001–A007**.
- One thing that **surprised** you about adding knowledge (before vs. after).
- **One number from tracing** (tokens or latency) and what the trace showed the agent doing.
- One thing you'd add before letting this near a real customer.

---

## ⭐ Stretch goals (if you finish early)

Pick any — they make WattWorth stronger:

1. **Customer letter.** Ask WattWorth to also draft a polite, plain-language outcome message for a declined applicant (responsible, non-judgmental tone).
2. **Edge case hunting.** Invent a tricky applicant (e.g. high income but a recent default) and see if WattWorth flags it correctly — then check the **trace** to see how it reasoned.
3. **📊 Build a tracing view.** In Application Insights, open **Logs** and write a simple query to compare token usage or latency across your runs.
4. **🧰 Add a second tool.** Give the agent another knowledge file or action and watch the new tool call appear in the trace.

---

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| No model when creating the agent | gpt-5-mini is pre-deployed — pick it from the model list. If it's missing, deploy **gpt-5-mini** under **Models + endpoints**. |
| Reply isn't JSON | Add *"Output valid JSON only, no markdown fences."* to the agent's Instructions. |
| Knowledge seems ignored | Confirm `lending-policy.md` finished **indexing** and is attached to the **agent**, then start a **fresh chat** and re-send the applicant. |
| Verdicts disagree with the policy | Re-read the policy's thresholds; make sure the knowledge file actually indexed. |
| No traces showing | Wait ~30–60 seconds and refresh — traces lag. Confirm with a facilitator that the project is connected to Application Insights. |
| Totally stuck | 🙋 Raise your hand — a facilitator will get you moving. |

---

## What you just learned

In ~40 minutes you built an **agent**, shaped its behaviour with **instructions**, enforced a **structured output contract**, gave it a **knowledge tool** so it follows real business rules, and used **tracing** to see exactly what it did — the core loop of building *and operating* useful AI agents on Azure AI Foundry. That's the Foundry, found. ⚒️
