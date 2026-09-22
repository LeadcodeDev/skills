# Platform: LinkedIn

A post is not a short article. An article earns trust by showing its sources; a post has no sources to show, no links the reader will click, and no second chance after the third line. It earns trust by being visibly the account of someone who was there.

The register is spoken French, first person, closer to a good Reddit post than to a press release: you are telling people in your field what happened to you, not announcing a result to an audience.

---

## The arc

Seven beats, in this order. They are slots to fill, not headings to write, and the post contains all seven.

1. **L'aveu** — situate yourself in time and in mood, and admit something. Not a hook engineered to stop the scroll: an actual admission, which stops it better. *"Petite confession (en retard d'une journée) d'un dev un dimanche soir"*
2. **Le constat** — what was genuinely wrong, stated flatly, followed immediately by the circumstance that makes it fair rather than damning. The excuse is not softening, it is accuracy: most bad states have ordinary causes.
3. **La conséquence** — one concrete thing that suffered, named specifically. A module nobody found, a velocity you lost, a contributor who gave up. One. A list of consequences reads as a complaint.
4. **Le déclic** — an *external event* that forced the issue: an issue opened by a stranger, a question in a review, a migration that would not run. Never an epiphany, never "j'ai réalisé que". An epiphany is unverifiable and reads as narrative furniture; an event is a fact and it is what makes the post a story rather than an announcement.

   **If the notes contain no such event, ask the user for one. Never supply it.** The déclic is a specific like any other, and the arc requiring the slot is not a licence to fill it — a plausible triggering event is still an invented one, and it is the single most quotable sentence in the post. Flagging the invention afterwards does not repair it: the user reads a finished post and approves the shape, not the provenance of each beat. Ask, and write the rest while you wait.

   Leave the beat in the draft as a bracketed marker, `[DÉCLIC : l'événement extérieur, à me donner]`, rather than closing the gap or omitting the beat. A marker cannot be pasted by accident; a missing beat is invisible, and a smoothed-over one is a fabrication.
5. **Ce qu'on a changé** — the `👉` list. One line per change, no sub-bullets, five items at the outside.
6. **La leçon** — one sentence, standing alone. If it needs two, it is not yet a lesson.
7. **L'invitation** — an open question to people who have done the same thing. Genuinely open: something you do not already know the answer to.

Beat 4 is the one that goes missing. A post without it is a changelog with feelings.

---

## The critique has exactly one permitted target: you

Never a person, never a company, never a technology, never a past maintainer. Not softened — absent.

This rule is hard to follow because **the violation usually arrives inside the raw material.** Notes written for yourself are full of "ça nous saoulait", "la conso mémoire était délirante", "c'était l'enfer à versionner". Relaying those faithfully feels like accuracy. It is not: it publishes a judgement you made in private, about someone else's work, to an audience that includes the people who wrote it.

So convert. The subject of the sentence moves from the tool to you, and what was a verdict becomes a fit:

| In the notes | In the post |
|---|---|
| "la conso mémoire de la JVM était délirante" | "sur nos nœuds, l'empreinte mémoire ne passait plus" |
| "la config XML c'était l'enfer à versionner" | "on n'arrivait pas à versionner la config proprement, avec nos habitudes" |
| "Keycloak est trop lourd" | "on avait besoin de quelque chose de plus petit que ce qu'on utilisait" |
| "ça nous saoulait depuis des mois" | "ça faisait des mois qu'on repoussait le sujet" |

The pattern: state the constraint *you* had, not the defect *it* had. A tool that does not fit your constraints has not failed, and saying so costs the post nothing — the reader who uses that tool keeps reading instead of defending it.

**Your own past work is the one thing you may criticise freely**, and doing so is what buys the post its credibility. "l'UI ne me convainquait pas moi-même" is the sentence that makes the rest believable.

---

## Specifics, with no way to check them

The core rule does not relax here, it tightens. On the blog a reader can follow a link. On LinkedIn there is nothing to follow, so every number in the post rests entirely on your word.

Two consequences:

- **An order of magnitude beats a false precision.** "l'image est passée de 700 Mo à une trentaine" is honest and memorable. "réduction de 95,7 %" invites a question you cannot answer.
- **Say when you did not measure.** "Je n'ai pas fait de mesure propre, donc je ne vais pas sortir un pourcentage" is a strong sentence, not a weak one. It tells the reader which of your other numbers to trust.

---

## What the coverage list becomes here

The blog's five coverage items do not survive a 2000-character post. Two of them do, and they are already in the arc:

- **The limits** — beat 2's excuse, and any "pour qui ça ne vaut pas le coup" near the end.
- **The next step** — beat 7's invitation.

**Prerequisites, the developed counter-argument, and the open-questions section are dropped on purpose.** Do not reintroduce them. A post that stops to establish what the reader must already know has spent its opening on the wrong thing.

---

## Form

- **Zero dashes.** No `—`, no `–`, no `-` used as punctuation. Comma, colon, period, or a line break. This is absolute, quoted material included, because a post quotes nothing.
- **One to three lines per paragraph**, separated by a blank line. The white space is doing real work: it is what makes the post readable in a feed on a phone.
- **`👉` for the change list, and nowhere else.** This is a deliberate exception to the no-emoji rule the blog follows. No other emoji appears in the post, and never one inside a sentence.
- **No markdown.** LinkedIn renders none of it. No `**bold**`, no `#` headings, no `[text](url)`. A URL is pasted bare or left out.
- **Quotation marks around jargon** you are borrowing rather than endorsing: `un style "brutaliste"`, `les "realm settings"`.
- **`qu'on` is correct here.** The blog's `que l'on` rule is a written-prose rule; applying it to a post makes the voice stiff. Spoken register is the point.

---

## Output contract

The post is the whole output. It ends on the invitation, and **nothing follows it**:

- no hashtags
- no commentary about the choices made, in any language
- no "voici le post", no closing offer to adjust it
- no signature, no call to action beyond the invitation itself

Anything you need to tell the user about the post — a number they should check, a claim you could not verify, a sentence that speaks for them and deserves a re-read — goes in the conversation **before** the post, not appended to it. What sits after the last line is what gets pasted by accident.

Show the post in the conversation. Write a file only if the user asks, and ask where it goes rather than choosing a path.
