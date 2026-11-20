# Cost Optimization Write-up

I cut the token cost of the ShopAssist Tier-1 agent in both questions without changing the action or the article it picks.

## What I changed

In Question 1, every ticket now makes exactly one LLM call with a short uncached prompt. The prompt has only the instruction, the customer thread and a compact ORDER_SNAPSHOT JSON. There is no help center dump, no padding to reach the cache size, and none of the phrases that make the simulator write a long answer. The original code cost 5,144 points for the four public tickets (about 1,286 per ticket). Now it costs 3,870 for the same four, about 968 per ticket.

In Question 2, I kept the four nodes but only compose calls the LLM. Triage builds a summary of the order facts in code. Retrieve scores every help center article against the order fields and picks the single best one. Compose sends the thread, that one article and the compact snapshot in a short prompt. Audit checks in code that the JSON is valid, the action is one of the four allowed ones and the article id exists, and if not it uses a rule-based answer instead. The original code cost 6,320 points for the four tickets (about 1,580 per ticket). Now it costs 4,275, about 1,069 per ticket.

## Per-call breakdowns

Q1 T-1001 (handle_ticket): input 133, cached 0, output 46, points 1,089.

Q2 T-1004 (compose, physical fulfillment): input 148, cached 0, output 36, points 984.

Q2 T-1003 (compose, escalation): input 146, cached 0, output 39, points 1,023.

I also checked T-1091, a subscription ticket that is not in the public four. It costs 930 points in Q1 and 1,047 in Q2. Every ticket is under the targets of 3,000 for Q1 and 2,500 for Q2.

## Why it got cheaper

Output tokens cost 15 points each, so I kept the prompt short and the answer stays at 36 to 46 tokens. Uncached input costs 3 points per token, but my prompts are only 114 to 172 tokens. The old cached prefix was over 1,000 tokens and still cost about 320 points even at the cached price, so at this prompt size caching does not help. The tradeoff is that I give up prompt caching.