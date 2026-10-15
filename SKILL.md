---
name: youtube-pockets
description: Finds viral "pockets" (hot topics and winning title formats) on YouTube for the user's own channel, using vidIQ outlier data. Trigger whenever the user says the keyword "youtube-pockets", even if they add a niche in the same message or say nothing else. Also use it when the user asks which topics or title formats are blowing up on YouTube right now in a niche. Asks for the niche first, searches vidIQ for recent long-form English outliers, and reports at least 2 pockets in chat, ranked by viral potential.
---

# youtube-pockets

The user makes YouTube videos for their own channel and wants to know where the momentum is right now. A "pocket" is a spot where recent videos are overperforming: either a **topic** (for example a brand-new model release like "GPT-6 Astra") or a **title format** (for example "How To ___ In 7 Days"). Spotting pockets early lets them make videos that ride demand that already exists.

This skill uses vidIQ, which is the whole point. Do not substitute web search or guesses for the data.

## Step 1: Get the niche

When the user says "youtube-pockets", ask one short question: **"What's the niche?"**

If they already gave the niche in the same message ("youtube-pockets AI automation"), skip the question and go straight to searching. Asking again would waste their time.

## Step 2: Build the keyword set

From the niche, pick the sub-topics that fit it. For a niche like "AI", that might be AI agents, new model releases, AI automation, AI tools for business. Use `vidiq_keyword_research` on the niche to see the related keywords vidIQ suggests, and combine those with your own judgment about what fits. Aim for roughly 3 to 6 keywords so the search covers the niche without drifting off topic.

The vidIQ tools may be deferred. If calling one fails because its schema isn't loaded, load it with ToolSearch (`select:mcp__vidIQ__vidiq_outliers,mcp__vidIQ__vidiq_keyword_research`) and retry.

## Step 3: Search with these filters

Use `vidiq_outliers` with these settings every time. They are fixed because the user chose them, and loosening one quietly would change what the results mean.

| Setting | Value | Why |
|---|---|---|
| `publishedWithin` | `thisMonth` | vidIQ has no exact 4-week filter, so this is the user's chosen stand-in |
| `minOutlierScore` | `2` | Only videos doing at least 2x their channel's normal |
| `minViews` | `50000` | Filters out small flukes |
| `contentType` | `long` | Long-form only, no Shorts |
| `language` | `en` | English titles only |
| `limit` | `50` | Search in batches of 50 |
| `userGoal` | one short sentence, e.g. "find viral topics and title formats for the user's own YouTube channel" | Helps vidIQ |

Run one search per keyword from Step 2.

## Step 4: Collect until you have 10 passing videos

The goal is **10 videos that pass the filters**, deduplicated by video. Because the filters are applied by vidIQ, every returned video already passes, so just count unique ones.

- If the first searches give 10 or more unique videos, stop. More searching only burns the user's vidIQ credits (5 per search).
- If you have fewer than 10, run more searches with the remaining keywords, then with related keywords you haven't tried.
- If you still can't reach 10 after trying the sensible keywords, **do not relax the filters on your own.** Report how many you found, say that's all the niche had under their filters, and offer to loosen one (for example the view minimum). Their rules stay theirs to change.

## Step 5: Find the pockets

Read all the titles. Then look for two kinds of pocket:

- **Topic pockets**: the same subject showing up in several winning videos, such as a product launch, a named tool, or a news event.
- **Title-format pockets**: the same structure reused, such as "I Built ___ In ___" or "X Ways To ___ Using ___". Write the format as a template with blanks.

Report **at least 2 pockets**. Fewer is a failure of analysis, so look again. Often the same video set holds a topic pocket and a format pocket at once, and a pocket can be a topic paired with its best format.

Judge pockets by what the data shows, not by what sounds clever. One video is an anecdote; two or three videos from different channels pointing the same way is a pocket.

## Step 6: Rank by viral potential

Order the pockets so the one with the most viral potential is first. Viral potential is a judgment call, so weigh these signals together and say briefly why you ranked as you did:

- **Outlier scores**: higher means the idea beat the channel's baseline by more.
- **Views per hour or recency**: a pocket still accelerating beats one that peaked weeks ago.
- **Smaller channels winning**: if low-subscriber channels hit the outlier, the idea works without a big audience, which matters for the user's own channel.
- **Spread across channels**: several different creators succeeding is stronger than one channel repeating itself.
- **Room to enter**: a pocket the user can still make a video for beats one that depends on a one-time event that's already over.

Do not rank by video count alone. A pocket with 2 videos at 8x can beat one with 5 videos at 2x.

## Step 7: Reply in chat

Everything goes in the chat. Do not create a file or document. Use this layout for each pocket:

**Pocket 1: [short name]** (rank by viral potential, best first)
- **Topic:** the subject, named specifically (e.g. "GPT-6 Astra")
- **Best title format:** the template with blanks, plus 1 or 2 real titles from the data that show it working
- **Based on:** 2 or 3 videos, each with title, channel, views, outlier score
- **Why it ranks here:** one or two sentences

Open with a one-line summary of the search: the niche, the keywords used, and how many videos passed. Close with a single line only if something needs flagging (for example, fewer than 10 found).

Stop there. The user wants the analysis, not title ideas or a follow-up offer, so do not suggest next steps or hand off to another skill.
