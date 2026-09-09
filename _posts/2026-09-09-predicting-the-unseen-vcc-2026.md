---
layout: post
title: "Predicting the Unseen: An Update from the 2026 Virtual Cell Challenge"
date: 2026-09-09
author: "Yash Raj"
categories: [AI, virtual-cell, CRISPRi, single-cell]
---

Three weeks into the Arc Institute Virtual Cell Challenge 2026, the leaderboard reads: Team 42, rank 117 of 780. The task is simple to say and hard to do: silence one gene in a cell and predict what the whole cell does next, in cell lines nobody has ever seen perturbed. This note is about what our number means, the first study we can share, and the road to November.

Team 42 is [Linhui Xie](https://www.linkedin.com/in/linhui-xie/), [Yurui Chen](https://www.linkedin.com/in/matcyr96/), Yuxuan Hu, and [me](https://www.linkedin.com/in/yashraj59/). What we are building stays with us until the race ends. What we have learned along the way is worth sharing now.

## The ask, in numbers

Teams receive three mystery cell lines. For each one they get 18,400 unperturbed control cells and nothing else. No examples of how these cells respond to any perturbation, in any dataset, anywhere. From that, they must predict the full expression profile for 300 gene knockdowns in each cell line. Multiplied out, one complete entry is 6.7 billion predicted numbers.

![A multiplication cascade: one entry times 900 questions times 400 simulated cells times 18,533 genes equals 6.7 billion predicted numbers, built from 55,200 control cells and zero perturbed examples](/fig/vcc-2026-team42/fig1_the_ask.png)
*One complete entry, multiplied out. The zero at the bottom is the design choice that defines this year's edition.*

## What changed since 2025

Both editions use a 300-gene panel. In 2025 everything happened inside one cell line, H1 stem cells: 150 knockdown responses were given as training, 50 were predicted for the leaderboard, and 100 decided the final. In 2026 the scored cell lines come with zero measured responses, the questions multiply to 900, and the final moves to three further cell lines nobody has seen at all. The same knockdown measured in two different cell lines only barely agrees, so the transferable signal is thin, and finding it is the whole game. The two leaderboards are not comparable, and that is the point of the redesign.

![Dot census of the two editions: 2025 shows 150 filled answer dots, 50 leaderboard rings and 100 final rings in one cell line; 2026 shows 900 open rings across three cell lines with zero filled, plus a dashed strip for the final round's three additional cell lines](/fig/vcc-2026-team42/fig6_editions.png)
*Every dot is one gene in the panel. In 2025 half the panel came with answers. In 2026 none of it does, and there is three times as much of it.*

## Where we stand

Every entry is scored between two anchors. Zero means no better than giving one average answer for every question. One means matching a rerun of the real experiment. We score 0.129, and no team we can see is anywhere close to one. Studies published last year found that deep learning models on this problem often fail to beat simple linear baselines, and the leaderboard agrees. The whole field, the largest models included, is still far from the center. That is not a reason to stay out. It is the reason the problem deserves the effort.

![A target diagram where the bullseye represents matching a rerun of the experiment and the outer edge represents predicting one average answer, with Team 42 near the outer rings](/fig/vcc-2026-team42/fig2_target.png)
*The center is a rerun of the experiment. The outer edge is one average answer for everything. The whole field, us included, is still near the edge.*

## Naming the mystery cells

Before any modeling, we profiled the 55,200 control cells. The three contexts are three different cell lines, not three batches of one: their cells never mix in a joint embedding, and between contexts expression correlates at 0.75 to 0.84 while a context agrees with itself at 0.9999. We matched each context against 2,098 reference profiles from three independent panels and named all three lines, then verified the calls with checks that never use gene identity, such as chromosome-arm copy number patterns and hallmark gene deletions. One line is confirmed by six independent checks. For another, the lineage is certain but the exact line has weaker support, and we say so.

![UMAP of 55,200 control cells forming three non-overlapping clusters, a pseudobulk correlation matrix between contexts, and a marker heatmap assigning each context a distinct lineage](/fig/vcc-2026-team42/fig3_context_identity.png)
*Three non-overlapping populations, three distinct lineages.*

![Identification of the three contexts against three independent reference panels, with karyotype and deletion-fingerprint checks](/fig/vcc-2026-team42/fig4_identification.png)
*Each call stress-tested with checks that ignore gene identity. The matrix records where a check identifies the line and where it is only consistent.*

Two warnings for other teams. The gene panel is pre-filtered, so ordinary quality signals such as ribosomal genes are missing. And the gene names follow an older convention, so joining to a modern reference can silently drop features.

## What we hold onto, and what is next

Three habits the challenge rewards, in one breath: predictions are populations, so thousands of identical cells score worse than predicting nothing; only the real evaluation counts, so every conclusion we keep is one the leaderboard confirmed; and characterize before you model, because the naming study changed how we read everything after it.

The public leaderboard runs through October 22. Then the three final cell lines are released, entries close November 5, and the official ranking comes from those alone. We will write the post-mortem either way.

![Timeline from the August 20 launch through today, to the October 22 leaderboard close and the November 5 final entry deadline](/fig/vcc-2026-team42/fig5_road.png)
*The solid part of the road is behind us. Everything that decides the official ranking is still ahead.*
