---
title: "The Trusted Digital Civilization, Chapter 5: Trusted Ratings"
description: "A two-way 0-10 rating system for AI tools, products and services, weighted by real use and backed by verified buyers, with a local SLM that knows its user without exposing them."
date: 2026-11-14
---

**THE TRUSTED DIGITAL CIVILIZATION**

**Chapter 5**

# Trusted Ratings: When Both Sides Are Accountable

*A two-way, 0–10 closed-loop rating system for AI tools, products and services, and a local SLM that knows its user without exposing them.*

*By Simon Nguyen • Systems Architect*

Ratings were supposed to help us choose. Today they often do the opposite. Reviews can be bought, a five-star score appears minutes after delivery, and paid placement decides what we see first. Most people have learned to read ratings with suspicion.

AI tools have the same problem, in a new form. Users rate the AI, but the AI never rates the interaction back. And an AI that simply agrees with everyone will collect high scores from people who wanted to hear yes, which teaches the next model to agree even more.

In earlier chapters, I described User Trust Data (UTD) and a local Small Language Model (SLM) that works for its user. This chapter adds the missing piece: a rating system where *both* sides are accountable, the score means something, and the user stays in control of what is known about them.

> **CORE IDEA**  
> Replace one-way star ratings with a two-way 0–10 trust score, weighted by how much real interaction stands behind it. It works today on its own, and becomes much stronger once UTD can prove that a rating came from a real, verified person.

## 1. Why 0 to 10, and Why Two-Way

A five-star scale starts at one, which still implies some value. A 0–10 scale has a true zero, and that zero carries two meanings: either the interaction was a complete failure, or the other side is probably not a real person at all. The lowest score doubles as a bot and abuse detector.

The score also flows both ways. The user rates the AI, and the AI rates the interaction with the user. Neither side is passive. As AI becomes a daily companion, trust has to work the way it does between people: earned, and checked from both directions.

## 2. Three Bands: Act Only at the Edges

The scale is not treated as a straight line. It has three bands, and only the outer two need action:

| Band | Meaning | Action |
|---|---|---|
| 0–3  Critical low | Bot, abuse, anger, or total failure | Investigate and flag; end the session early to save compute |
| 4–8  Normal | Real people in ordinary use: the loop corrects itself | Hands off. No intervention needed |
| 9–10  Exceptional | Real engagement and real value delivered | Study what went right and reward it |

Like a well-tuned control loop, the system corrects at the boundaries and trusts the stable range in between. The same scale applies to both sides, and a large mismatch between them (the user gives 9, the AI sees signs of a bot) is itself a useful signal.

## 3. A Score Must Be Earned: Weighted Trust

A raw number alone is not enough. Every score is weighted by how much real interaction stands behind it:

**Weighted Trust = Score × Confidence × AI Credibility × Session Depth**

| Situation | Score | Confidence | Weighted trust |
|---|---|---|---|
| Two questions, then a quick exit | 10 | 0.30 | 3.0 |
| Medium session, consistent | 7 | 0.80 | 5.6 |
| Full, deep session | 10 | 0.95 | 9.5 |

- **A premature 10 is held, not trusted.** After only a question or two, the AI can simply ask: “Thanks for the 10, but we have only just started. Are you stepping away?” The score counts fully only once the session has real depth.
- **The AI must earn its own credibility.** An AI that never pushes back, never admits uncertainty, and always agrees should have its high scores discounted automatically. Flattery is not quality.
- **Meaning comes from the aggregate.** One score says little; patterns across many sessions are what the system learns from.

## 4. It Works Today, and UTD Makes It Stronger

This system does not have to wait for new hardware. It can start with behavior alone: session depth, consistency, and the zero score already catch most bots and careless ratings. A determined faker can still get through, so the design grows in steps:

| Stage | What backs the rating | What it stops |
|---|---|---|
| Today | Behavior: depth, consistency, timing | Most bots and drive-by ratings |
| Device trust | A genuine phone or laptop (secure element) | Mass fake accounts and rating farms |
| Person trust (UTD) | A verified, real person | Paid and fabricated reviews at the source |

## 5. Beyond AI: Products and Services

The same design fixes the weakest parts of product and service reviews:

- **Verified buyers only.** With UTD, only someone who actually bought and used the product can rate it. The rating proves a real purchase without revealing who made it.
- **Weighted by real use.** A rating after six months of use counts more than one written on the day of delivery. Durability finally shows up in the score.
- **Two-way for services.** For a ride, a repair, or a medical visit, both the provider and the customer can rate the transaction, so good customers and good providers both become visible.

## 6. Separating Quality from Everything Else

Many low ratings have nothing to do with the product. A late delivery, a damaged box, a rude seller, or a price that dropped the next week all pull down a product's score, while its real quality stays the same. The tool should help in two ways.

**Help the user rate.** Instead of one number, the SLM asks a few short questions and records each part separately:

| Part | Products | Services |
|---|---|---|
| Quality / outcome | Does it work, last, and match the description? | Was the job done right? Is it still right months later? |
| Delivery / timing | Arrived on time and undamaged? | On time? Finished when promised? |
| Seller / provider | Support, returns, honesty | Communication and respect |
| Value | Worth the price paid? | Final price matched the quote? |

If a user gives a product a 2 because of a late delivery, the SLM simply asks whether the problem was the product or the shipping, and files the score in the right place. It never pushes the user to change a score; the user always has the final word, and the original rating is kept.

**The tool's own quality rating.** The AI can also show its own independent quality assessment, built from evidence rather than opinion: verified-buyer quality scores, return and defect rates, warranty claims, recalls, and how well the product matches its specifications. This rating is always shown *separately* from user ratings, with the reasons behind it, and it never replaces them. The AI cannot hold or test the product itself, so its rating is evidence to weigh, not a verdict, and like any AI score it must earn credibility over time.

Services are harder. Quality depends on the person, the day, and the job, and the outcome may not be known for months. The tool helps most by rating concrete facts (on time, quote matched, problem fixed) and by following up later, for example asking three months after a repair whether it is still working.

## 7. Shopping on Ratings from Real Buyers

A local SLM is well placed to help with shopping. It already holds the user's purchase history, sizes, trusted brands, and budget, all kept on the device. That history makes two things possible that today's review systems cannot do reliably.

**Users rate what they actually bought.** Because the SLM knows each purchase, it can invite the user to rate it at the right time: after a few weeks of real use, not on the day of delivery. Only items in the user's own purchase history can be rated. A product the user never bought cannot receive their rating, which removes most fake and purchased reviews at the source.

**Recommendations use only ratings from real buyers.** When the SLM compares products, it counts only ratings that carry a verified-purchase proof from a real person. Bot ratings, unverified reviews, and drive-by scores are left out, and the remaining ratings are weighted by depth of use, as in Section 3.

Proof of purchase does not require giving up privacy. The UTD gate can turn a purchase record into an anonymous token that proves *“a real, verified person bought and used this product”* without revealing who that person is or what else they have bought. Before Secure Glass is available, the same proof can come from a device-signed receipt, at the device trust level.

- **Free or discounted items are labeled.** A rating for a product received free or in exchange for a review is marked as such and weighted lower.
- **Returns still count.** A product that was bought and returned can still be rated; a return is often the most useful signal of all.

Commission is the remaining risk. If sellers pay the AI a referral fee, the AI gains a hidden reason to recommend whoever pays most. Four rules keep it honest:

- **Rank first, look up commission later.** Products are ranked by the user's own criteria before any referral fee is known, so the fee cannot change the answer.
- **Always disclose.** “This seller pays a referral fee” is shown every time.
- **Share the benefit.** Part of any commission can return to the buyer as a rebate.
- **The user can switch it off.** A simple rule such as “never show sponsored results” is enforced by the UTD gate.

**The AI works for the user. If anyone pays it, the user must know, and the payment must not change the answer.**

## 8. An SLM That Knows You, and Answers Faster

A good assistant adapts to the person in front of it. The local SLM can keep a small profile of the user's experience level and use it to answer at the right depth: skipping basics for an expert, adding steps for a beginner. That means shorter answers, fewer clarifying questions, fewer tokens, and better routing, with simple questions answered locally and expert questions sent straight to the cloud model.

- **Per topic, not per person.** Someone can be an expert in chip design and a beginner in optics. One overall level would mislead the model.
- **Local and visible.** The profile stays on the device, and the user can see and correct it. Only a hint such as “expert level” passes through the UTD gate, never the profile itself.
- **Separate from trust.** Experience level decides *how* to answer; the trust score decides *how much to believe* the interaction.

## 9. Guardrails: No Hidden Score

Any system that rates people can slide into a hidden score that quietly follows them and affects the prices or service they receive. That would defeat the purpose of this series. The design therefore keeps firm limits:

- **Rate the transaction, not the person.** Scores describe what happened in an interaction, such as a no-show, abuse, or fraud, not someone's worth.
- **Visible and disputable.** Users can see every score about them, with a full audit trail and a way to dispute it.
- **Not shared without consent.** A score earned with one service does not travel to another unless the user allows it through the UTD gate.
- **Fair across cultures.** People rate differently in different countries, so scores are compared within comparable groups rather than against one global average.
- **The user owns the data.** Ratings given and received belong to the user and move with them.

## Conclusion

Trust has always worked in two directions between people. Ratings should too. A two-way 0–10 score, weighted by real interaction, can start working today; UTD then turns it into proof that a real, verified person stands behind each rating. Combined with a local SLM that knows its user without exposing them, the result is an AI that answers faster, recommends honestly, and earns its scores instead of collecting them.

> **OPEN USE**  
> The Human-AI Closed-Loop Feedback System described in this chapter, including two-way 0–10 trust scoring, the three-band structure, and the weighted trust formula, is released freely to the AI community. Any implementation requires attribution to Simon Nguyen as the inventor. Implementations using Secure Glass or Tiny Box remain covered by my separate patent-pending filing.
