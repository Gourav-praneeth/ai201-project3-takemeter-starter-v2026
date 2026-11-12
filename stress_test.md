# Stress test of the label definitions

**How this was done:** I gave Claude the four definitions (`analysis`, `opinion`,
`experience`, `logistics`) and asked for eight board-game comments that sit on the
boundaries, then labelled them with only the written definitions and the decision
rule. The posts are synthetic, not from the dataset.

**Limit of this test:** Claude drafted both the definitions and the test posts, so
it probably writes boundaries it already understands. It is a check for holes, not
proof that the labels are reliable. The real check is the cold relabel in
`criteria.md` (criterion 4).

| # | Synthetic post | Boundary | My label | Clean? |
|---|---|---|---|---|
| 1 | "Azul is overrated. I've logged 30 games and whoever grabbed the first-player marker won 22 of them." | analysis / opinion | analysis | Yes. If the 22/30 were false the claim would go. |
| 2 | "Ticket to Ride is the perfect gateway game, everyone I've introduced it to loved it." | experience / opinion | opinion | Yes, after tightening (see below). It ends on a verdict. |
| 3 | "Pretty sure Gloomhaven has a printed errata for that Spellweaver card, check the FAQ on the publisher's site." | analysis / logistics | logistics | Yes. A checkable fact, but it is a pointer, not an argued claim. |
| 4 | "People here are too negative about Kickstarter. Every campaign I've backed shipped." | opinion / experience | opinion | Yes. "Every one I've backed" is a story that could be false, but the claim stands without it. |
| 5 | "Played Root last night. Marquise won on turn 5 because nobody attacked. Fun night." | experience / opinion | experience | Yes. It narrates and "fun night" is not an argued verdict. |
| 6 | "Wingspan has 170 bird cards in the base game, so the replay value is huge." | analysis / opinion | analysis | Borderline. The number is checkable and the claim leans on it, but "huge" is a judgement. I kept analysis, since changing 170 to 20 would force a retraction. |
| 7 | "Is Brass: Birmingham worth $70? Everyone raves but I'm skeptical." | logistics / opinion | logistics | Yes, after tightening. It is a question first. |
| 8 | "Rulebook says draw 5 cards, my group's always drawn 7 and it's way better." | analysis / experience / opinion | opinion | Borderline. A rule is named, but the claim ("way better") survives if the rule were misquoted. |

## What I tightened

- **Experience vs opinion:** `experience` now says "without arguing a verdict".
  Post 2 was the gap: a story that exists to praise something.
- **Tie-break added:** when a post fits two labels, label by what the post is *for*.
  A verdict is `opinion`, a question or pointer is `logistics`, and narration with
  no verdict is `experience`. This settled posts 2 and 7.
- **Analysis test made explicit** as the delete-the-detail rule in the README. It
  settled 1, 4 and 8. Post 6 is still weak and I expect a few like it in the 200.
  I'll flag them as hard cases instead of loosening the rule.
