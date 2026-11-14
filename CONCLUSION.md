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

Layer 1 changed the results the most. It is the only layer that changed the number of correct answers.

Bare -> layer 1 (for my colab out of 24): correct 16 -> 18, wrong 4 -> 6, no answer 4 -> 0.

I think that this layer changed what the agent can do, so without it the agent just crashed on alias questions like 21-24q when it got "Unknown region". With layer 1 it saw the error, called list_regions and tried again with the right name, because of that 23rd and 24th question were correct but 21st and 22nd became wrong because the model picked the wrong region. The crash was hiding these mistakes before. Speaking about Layer 3 (grounding) only changed what the agent admits. Correct stayed at 18, but 2 wrong answers turned into no answer (wrong 6 -> 4, no answer 0 ->  2). Layer 2 never fired at all, and its numbers were exactly the same as layer 1

## 2. One failure the full harness did not fix

Pick one question that the full harness (layer 3) still gets wrong or refuses. Give its id
(q01 ... q20). Open its trace in the notebook: at which step did it go wrong, and what did
the model do instead? Name one thing you would add to the harness, and what it would cost
(extra model calls, tokens, time, or a new way to fail).

I chose q18. Under layer 3 it has no answer, the final answer was UNVERIFIED.

The first step was fine, get_series returned the correct numbers for Jambyl 16.1% in 2022 and 10.6% in 2025. The problem was in the calculate step so the numbers are already in %, so the answer in percentage points should be just 16.1 − 10.6 = 5.5 but the model wrote (16.1 − 10.6)/100 and got 0.055. After that it answered 0.55, which is strange because that is not even what calculate returned. The harness rejected it because 0.55 was not in any tool output. Then the model just did the same calculation again and gave the same answer, so in the end it was UNVERIFIED.

I think I would add a better hint for the case when the model repeats the same failed call. For instance, the harness could say that the values are already in percent and percentage points is just the difference. This would cost one or two more model calls and more tokens, but only on questions that already fail. The risk is that this hint can be wrong for other types of questions and lead the model to the wrong formula there.

## 3. Workflow or agent for the monthly ranking?

Dala Insight wants to publish, every month, "which region is highest and which is lowest on one
indicator in one year". Would you ship the fixed workflow or the agent? Use the accuracy, the
number of model calls, the tokens and the time you measured in Part 1. Then say in one sentence
what kind of question would make you choose the other one.

For me I would ship the fixed workflow. A monthly ranking is always the same type of question (one indicator, one year, highest and lowest), so it fits the workflow good I think.

In Part 1, on the 15 fixed-shape questions, the workflow got 13/15 correct with 0 wrong and 2 no answer. The agent got only 9/15, with 2 wrong and 4 no answer. The workflow was also much cheaper: 15 model calls vs 26, 3852 tokens vs 19835 (about 5 times less), and 15.4 seconds vs 47.1. Another important thing is that the workflow had 0 wrong answers. For something that gets published every month, I think it’s better to get no answer than a wrong one. The workflow only failed on q21 and q22, because of old region names (Nur-Sultan, Chimkent). In a ranking question the user doesn’t type a region name, so this problem probably won’t come up.
I would choose the agent if the question doesn’t fit a fixed template, for example comparing several indicators or regions in one question.

---

I wrote this file myself, without any AI tool.
