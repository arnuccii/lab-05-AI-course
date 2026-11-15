# Lab 05 — Conclusion

<!--
Read this before you write.

This file is graded by your instructor, by hand, and it is worth 6 of the 15 points.

- Type it yourself. Do not use a chatbot, an AI assistant, an AI autocomplete or a "rewrite my
  text" tool for any part of it, not even for grammar. English that is plain and a little
  clumsy but yours is better than polished text that is not.
- Use your own numbers. Every number you quote must be one your own notebook printed. Copy
  the number exactly as it appeared. A number your notebook did not print is marked as wrong.
- Answer in your own words, about 80-200 words per question. A short answer that uses your
  numbers beats a long answer that could have been written by anyone.
- Delete these comment lines if you like. Keep the three headings and the last line.
-->

## 1. Which layer changed your results the most?

Give the numbers before -> after for that layer (correct / wrong / no answer, out of 20) and say
whether it changed what the agent *can do* or only what it *admits*. If no layer changed the
number of correct answers, say so and explain what the layers did change instead.

Layer 1 changed my results the most. Before Layer 1 (in the bare run), the agent got 16/24 correct, 4 wrong, and 4 no answer. With Layer 1, the score went to 18/24 correct, 6 wrong, and 0 no answer. This layer changed what the agent can actually do, because sending the tool error text back let the model call list_regions and fix the region names on q23 and q24. (Layer 2 changed nothing at all, and Layer 3 only changed what the agent admits by turning 2 wrong answers into UNVERIFIED, changing the score to 18/24 correct, 4 wrong, and 2 no answer.)

## 2. One failure the full harness did not fix

Pick one question that the full harness (layer 3) still gets wrong or refuses. Give its id
(q01 ... q20). Open its trace in the notebook: at which step did it go wrong, and what did
the model do instead? Name one thing you would add to the harness, and what it would cost
(extra model calls, tokens, time, or a new way to fail).

One question that Layer 3 still did not solve is q18, where it ended with UNVERIFIED (no answer). In the trace, step 1 worked fine (get_series returned 16.1 for 2022 and 10.6 for 2025), but in step 2 the model called calculate with (16.1 - 10.6)/100 and got 0.055, and then in step 3 it wrote ANSWER: 0.55 percentage points from its head. Layer 3 rejected 0.55 because it was not in any tool output, so the model repeated the exact same wrong calculate call, hit the repeat limit, and failed. I would add a check in the harness that blocks repeating the exact same tool call and tells the model to write a new formula instead. This would cost extra tokens and seconds for an extra model step, and a new way to fail is that the model could try another wrong formula and run out of the 5-step budget.

## 3. Workflow or agent for the monthly ranking?

Dala Insight wants to publish, every month, "which region is highest and which is lowest on one
indicator in one year". Would you ship the fixed workflow or the agent? Use the accuracy, the
number of model calls, the tokens and the time you measured in Part 1. Then say in one sentence
what kind of question would make you choose the other one.

I would ship the fixed workflow for this task. In Part 1, the workflow was more accurate on the 15 fixed questions (13/15 correct and 0 wrong, compared to 9/15 correct and 2 wrong for the bare agent), and it was much cheaper and faster: 15 model calls instead of 26, 3,852 tokens instead of 19,835, and 16.0 seconds instead of 50.8 seconds. I would choose the agent instead if the questions are open-ended and need multiple steps, such as calculating percent growth or percentage-point changes across two different years.

---

I wrote this file myself, without any AI tool.
