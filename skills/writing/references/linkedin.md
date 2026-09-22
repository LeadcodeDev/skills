# Platform: LinkedIn

A post is not a short article. An article earns trust by showing its sources; a post has no sources to show, no links the reader will click, and no second chance after the third line. It earns trust by being visibly the account of someone who was there.

The register is spoken French, first person, closer to a good Reddit post than to a press release: you are telling people in your field what happened to you, not announcing a result to an audience.

---

## The arc

Seven beats, in this order. They are slots to fill, not headings to write.

Five carry the post on their own: 2, 3, 5, 6, 7. **Beats 1 and 4 are conditional**, and the condition is factual rather than stylistic — you write them when the raw material contains something real to put in them, and you leave them out when it does not. A manufactured admission and an invented triggering event are worse than their absence, because both are exactly the kind of specific a reader remembers, repeats, and eventually checks.

1. **L'aveu** *(conditional)* — situate yourself in time and in mood, and admit something. Not a hook engineered to stop the scroll: an actual admission, which stops it better.

   **Do not open on "Petite confession…", and do not reach for its variants** — "Confession de…", "Aveu de…", "Petit aveu…". That phrasing belonged to one post, occasionally, and it was the author's. Repeated, it stops being a voice and becomes a format, which is the one thing this beat cannot afford. The slot asks for an admission, not for a word announcing that one is coming.

   Treat that as a hard constraint rather than a preference. An earlier version of this file quoted the phrase as a harmless "sample of register"; the next draft written against it opened on *"Confession d'un lendemain de chantier"*. A caveat in prose does not survive contact with a concrete example — the example wins.

   Openings that work are structurally unalike, and that is the point:

   - the admission first, flat, no preamble: *"J'ai vérifié le travail de onze agents et pas une ligne du mien."*
   - the situation, with the admission landing after it: *"Deux jours de chantier. Ce qui a failli tout casser n'était pas dans le code qu'on auditait."*
   - the bare fact, left to do the work: *"Mon script avait un mode d'échec silencieux. Je l'ai découvert le dernier jour."*

   These are not templates either. The test is portability: if the opening could be lifted onto someone else's post without changing a word, it is a formula, not an aveu.

   With nothing genuine to admit, drop the beat and open on the finding instead. An admission you had to reach for reads as false modesty, and it costs more than a plain opening would have.
2. **Le constat** — what was genuinely wrong, stated flatly, followed immediately by the circumstance that makes it fair rather than damning. The excuse is not softening, it is accuracy: most bad states have ordinary causes.

   **The circumstance is a reason, not a second description.** *"La première version se contentait d'émettre un payload, sans filet"* restates the flaw in kinder words and leaves the first version looking careless. *"La première version était minimaliste, visait à compléter un manque en se contentant d'émettre un payload, sans filet"* says what it was **for**, and the same flaw stops reading as negligence. Give the intent, the scope it was built to cover, or the constraint you were under at the time. If the sentence could be deleted without losing a fact, it was not a circumstance.
3. **La conséquence** — one concrete thing that suffered, named specifically. A module nobody found, a velocity you lost, a contributor who gave up. One. A list of consequences reads as a complaint.

   **Join the damage to its cause in one breath.** Left as two paragraphs, the consequence states a fact and the event that produced it arrives afterwards as an orphan, so the reader assembles the causality alone. A colon does the work:

   > Je m'en suis rendu compte en regardant les données : mon service métier était éteint pile au moment où FerrisKey avait envoyé l'événement de mise à jour.

   And cut the negative restatement that habit adds on the end. *"Je m'en suis rendu compte en regardant les données, pas grâce à une alerte"* pays a clause to say what the first half already implies.
4. **Le déclic** *(conditional)* — an *external event* that forced the issue. An issue opened by a stranger, a question in a review, a migration that would not run, a CI job that went red: **the shape is what qualifies, not the list**. Anything that reached you from outside and made the problem impossible to keep postponing belongs here. Never an epiphany, never "j'ai réalisé que" — an epiphany is unverifiable and reads as narrative furniture, while an event is a fact, and that is what makes the post a story rather than an announcement.

   **Vague temporal markers do the same damage as an epiphany.** "Un jour", "récemment", "il y a quelque temps" demote a dated fact to an anecdote, and they are exactly where an invented event hides, because they excuse the writer from ever saying when. Lead with the event instead: *"Mon backend métier était éteint quand FerrisKey a envoyé la mise à jour d'une identité, un changement de prénom et de nom"* carries the same facts as the version opening on "Un jour", and the reader believes it. Give the date when it decides something, say nothing when it does not, and never gesture at time in between.

   **Never supply the event.** It is a specific like any other, it is usually the most quotable sentence in the post, and an invented one therefore does the most damage. The arc offering a slot is not a licence to fill it, and flagging the invention afterwards does not repair anything: the user reads a finished post and approves its shape, not the provenance of each beat.

   So when the raw material carries no such event, there are two honest moves and inventing is neither. **Omit the beat** — that is the default, and it costs the post less than people expect. Or, when the piece is visibly weaker without it, **ask the user** and leave `[DÉCLIC : l'événement extérieur, à me donner]` in the draft while you wait, writing the rest meanwhile. A marker cannot be pasted by accident; a smoothed-over gap can.
5. **Ce qu'on a changé** — the `👉` list, **and the sentence that leads into it**. The beat is named after that sentence, and it is the half that goes missing: the body says "the list", so a generator emits the list cold and the reader falls from the damage straight into bullets with nothing bridging them.

   The lead-in does connective work rather than announcing a list. *"J'ai changé cinq choses :"* clears the bar. *"Voici les améliorations apportées :"* is a heading wearing a sentence's clothes, and it adds nothing the bullets do not already say. The strongest form points the fix back at the consequence you just described, which is what stops the list reading as a changelog:

   > Ma table users n'a jamais vu passer ce changement. Je m'en suis rendu compte après coup, sans la moindre alerte.
   >
   > Depuis, plus rien ne repose sur le fait que l'autre bout réponde :
   >
   > 👉 Retry automatique quand l'envoi échoue

   **Name the work, not the release.** *"La v2 change trois choses d'un coup :"* makes a version number the subject and turns the post into a product note. *"L'un des derniers chantiers que j'ai menés change trois axes d'un coup :"* keeps you as the agent, which is the whole basis of the post's credibility. No "la v2", no "la nouvelle version", no release label carrying the sentence.

   Then one line per change, no sub-bullets, five items at the outside.
6. **La leçon** — one sentence, standing alone. If it needs two, it is not yet a lesson.
7. **L'invitation** — an open question to people who have done the same thing. Genuinely open: something you do not already know the answer to.

   **Ask about their situation, not about the topic.** *"Vous gérez ça comment, vous, la synchro ?"* gestures at the subject and doubles the pronoun for a familiarity nobody asked for. *"Comment gérez-vous la synchro de votre service IT lorsque le backend en face n'est pas toujours là pour la recevoir ?"* puts the reader's own system in the question, which is what makes answering feel possible.

   This is the one beat where the register tightens. The rest of the post is spoken French; the closing question is addressed to professionals about their work, so drop the oral tics — the doubled `vous`, the trailing `comment`, the inverted clause — and ask it straight.

Beat 4 is the one worth fighting for. It is what separates a story from a changelog with feelings, so when an external event does exist in the raw material, find it and use it rather than settling for the five load-bearing beats. Making it conditional is there to stop it being invented, not to make it optional in spirit.

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
- **Absolutes are claims, and usually false ones.** "complète", "totale", "tous", "jamais", "100 %" cost nothing to write and are almost never true of a first pass. Write the lesser claim, which is both accurate and more informative: *"Traçabilité améliorée de chaque webhook (succès comme échecs)"* beats *"Traçabilité complète de chaque webhook"*, because the parenthesis says what is actually covered while the superlative says only that you are pleased. A reader who has shipped the same feature knows "complète" is not true and stops trusting the rest.

---

## What the coverage list becomes here

The blog's five coverage items do not survive a 2000-character post. Two of them do, and they are already in the arc:

- **The limits** — beat 2's excuse, and any "pour qui ça ne vaut pas le coup" near the end.
- **The next step** — beat 7's invitation.

**Prerequisites, the developed counter-argument, and the open-questions section are dropped on purpose.** Do not reintroduce them. A post that stops to establish what the reader must already know has spent its opening on the wrong thing.

---

## Form

- **Full clauses, with a verb in each.** Verbless fragments dropped in for punch are advertising rhythm, not spoken French: *"Personne pour le recevoir."*, *"Cinquante-cinq PR au total."*, *"Aucune alerte. Rien."* Someone telling you what happened says *"Personne n'était présent pour le recevoir."* The fragment reads as written to be read; the clause reads as said. This is the tic that survives every other rule here, because it feels like tightening.
- **Zero dashes.** No `—`, no `–`, no `-` used as punctuation. Comma, colon, period, or a line break. This is absolute, quoted material included, because a post quotes nothing.
- **One to three lines per paragraph**, separated by a blank line. The white space is doing real work: it is what makes the post readable in a feed on a phone.
- **`👉` for the change list, and nowhere else.** This is a deliberate exception to the no-emoji rule the blog follows. No other emoji appears in the post, and never one inside a sentence.
- **The `👉` items are contiguous.** No blank line between them, whatever the one-to-three-line rule says above: that rule governs paragraphs, and the list is one block. Spacing the items out makes three changes look like three announcements and doubles the scroll the post costs.
- **Do not spend in the opening a specific a later beat needs.** *"c'est chez moi, sur mon propre backend métier, que je m'en suis rendu compte"* names the failing system in the second line, so the paragraph that reveals it was switched off lands on something the reader already knows. Keep the opening general, *"dans le cadre de l'un de mes projets"*, and let the specific arrive once, where it does work. "Chez moi" also frames professional work as tinkering, which is rarely what you mean.
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
