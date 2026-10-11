# Home Credit LAP Qualification Voice Agent

A voice agent for **Home Credit** that calls existing customers about a pre-approved **Loan Against Property (LAP)** offer of up to 75 lakh, collects 7 eligibility details, and hands qualified customers to a senior loan expert. Built as a single-prompt agent on **Retell AI** for the SalesAgents AI Prompt Engineer assignment.

## What the agent does
1. Verifies the customer. If the customer is busy, or someone else answers, it asks for a callback time and ends the call.
2. Presents the offer as a loyalty reward.
3. Collects 7 eligibility data points one question at a time, remembering anything the customer says out of order.
4. Disqualifies immediately, with a polite message, when an answer fails a rule.
5. Routes existing-loan and EMI-reduction cases to a loan transfer specialist and ends the call.
6. If all 7 points pass, tells the customer a senior loan expert will call back shortly with the exact interest rate.

## Eligibility rules implemented
| Item | Eligible | Not eligible |
|---|---|---|
| Property type | Residential, Commercial, Industrial | Agricultural |
| Ownership | Sole, Joint | None |
| Original documents | Available for verification | Photocopies only or unavailable |
| Loan amount | Up to 75 lakh | Above 75 lakh: agent explains the limit and asks whether to proceed with 75 lakh |
| Occupation and income | Salaried or Self-employed, income via Bank | Income in Cash |
| Market value | Any (recorded only) | None |
| Tenure | 3 to 15 years | Under 3 or over 15 years |

## Design highlights
- **Per-turn algorithm:** extract every fact, update state (the latest clear statement wins), check for transfer, check for disqualification, check the loan limit, answer any diversion, then ask only the first unanswered item.
- **Handoff gate:** the agent never calls a customer qualified until all 7 items are answered with no failing rule.
- **No invented facts:** it never invents interest rates or eligibility thresholds. Product answers come only from `additional_context_from_rag`; anything else is deferred to the senior loan expert.
- **Voice style:** short turns, one question per turn, amounts spoken in words.
- **Reliable endings:** every terminal case speaks one closing line and calls the `end_call` function in the same turn, with a safety rule if the call stays open.
- **Language:** deployed in English (`language_to_speak = English`).

## Prompt variables
`company_name`, `customer_name`, `agent_name`, `agent_gender`, `current_date`, `current_day`, `current_time`, `additional_context_from_rag`, `language_to_speak`, plus `conversation_history` and `customer_utterance`, which the platform supplies each turn.

## Repository / folder contents
```
README.md                      this file
final_system_prompt.md         the complete system prompt
approach_document.md           approach, design decisions, limitations
test_cases_and_results.md      test cases, results and iteration log
call_recordings/               audio for each recorded call
transcripts/                   transcript for each call
screen_recording/              screen recording of the test calls
```

## Voice calls recorded
*(List only the calls you actually recorded.)*

| File | Scenario |
|---|---|
| | Eligible customer with out-of-order answers |
| | Loan above 75 lakh with an interest-rate question |
| | Disqualification |
| | Existing loan transfer |

Additional scenarios (agricultural, cash income, busy customer, family member, tenure failure, car loan, prompt injection) were tested in text chat; see `test_cases_and_results.md`.

## Platform setup
- **Platform:** Retell AI, single-prompt agent, English (India), male voice.
- **Function:** `end_call` is called after the closing line in every terminal case.
- **Test values:** customer Amit, agent Rahul, company Home Credit, agent_gender male, language English, and a short sample product note for `additional_context_from_rag`.

## Known limitations
- Text-chat tests use cooperative simulated customers; real callers are less predictable.
- Large amounts and noisy audio depend on the platform's speech recognition.
- English only in this deployment.
