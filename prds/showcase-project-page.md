# PRD — Showcase Project Page (v0)

*Status: **Draft** — emotional intent stage. Visual design TBD (3 concepts to be produced in external design tool, then folded back in). | Created: 2026-05-19*

---

## Why this page exists

A vibe-coder PM has just shipped something with AI. They want a single URL they can paste into Slack, send to their boss, share with peers, and have it instantly communicate: *I built this, here's how, here's the journey, I know what I'm doing.*

This page is the **emotional core of IBUILTTHIS**. It is the first thing we are building because everything else in the product (kickstart, journey tracking, social feed) exists to feed *into* this page. If this page doesn't feel right, nothing downstream matters.

It is also the **first impression** for the secondary audience — peers, bosses, recruiters — who encounter IBUILTTHIS through a shared card rather than the front door. The page has to earn their attention in the first three seconds.

### Default entry hypothesis

The viewer arrives **via a social post from the builder themselves** — a LinkedIn post, an X thread, a Reddit comment, an Instagram story. They are not browsing a Product Hunt–style ranked feed or a Dribbble-style designer gallery. The builder put the link out into the world; the viewer clicked because *they trust or are curious about that specific builder*.

This shapes the entire page: the builder is already implicitly "endorsed" by the share. The page therefore does not need to heavily *sell* the builder — it just needs to tell the project's story well and place the builder honestly inside it. A future profile page will live elsewhere and aggregate a builder's projects (out of scope here).

---

## Who this page serves

Two audiences, both at the same time:

### 1. The Builder (looking at their own card)

A PM or designer who shipped a thing. They are sharing this with a person who matters to them — a former colleague, a manager, a recruiter, a peer they want to impress, or a future self.

**What they need to feel when they see it:**
- **Proud.** "That's mine. That's good. I want to send this."
- **Seen.** "It captured the journey, not just the output. It tells the real story."
- **Confident sharing it.** No hesitation. No mental edit. No "let me clean this up first."

### 2. The Viewer (peer, boss, recruiter, stranger)

Someone who clicked the link. Probably skimming. May not know the builder personally. Has 8 seconds before they bounce.

**What they need to feel when they see it:**
- **Impressed, fast.** "Oh — they actually built something real."
- **Curious.** "I want to know how they got there, what tools they used, what broke."
- **Respect.** Not "cute side project." Closer to "I'd hire this person" or "I should make something too."

---

## Emotional Intent — What the page IS

The page should feel like:

- **A finished thing, not a profile to fill in.** This is a *piece*, not a *template*. The builder isn't presenting raw material; they're presenting a story that has been told.
- **Personal but produced.** Like a great Medium post, a well-shot Loom, a deck a designer made for themselves. Not a LinkedIn entry. Not a Notion page. Not a GitHub repo.
- **Honest about the journey.** Showing the struggle, the pivots, the dead-ends is part of the credibility. The page should make a viewer think *"this is real,"* not *"this is polished marketing."*
- **Builder-flavored.** Each card carries the personality of its maker. Even at v0 with hardcoded data, the three example builders should feel like three distinct humans, not three rows in a database.
- **Shareable as an artifact.** When someone drops the link in Slack, the unfurl alone should make you want to click. The page itself should feel screenshot-worthy.

---

## Emotional Intent — What the page IS NOT

Equally important. The page must not feel like:

- **A portfolio template.** No "Hi I'm [Name], I'm a [Role] passionate about [Things]." That's death.
- **A LinkedIn entry.** No professional polish-talk. No corporate voice.
- **A GitHub README.** No raw tech-flexing. No "stack: Next.js, Postgres, Drizzle." (Tools are part of the story, not the headline.)
- **A Product Hunt launch page.** Not a marketing pitch. Not a CTA-driven landing. The product being showcased is not for sale on this page — the *builder's journey* is.
- **A Notion-page-pretending-to-be-a-product.** Generic, structured, soulless. Sections with headings. Bullet lists. Tables. Death.
- **Filled-in admin work.** It must never read as "I had to put something here." If something is there, it should feel like it had to be.

---

## What the page conveys

These are the **story beats** the page must communicate, not a layout spec. The designer decides how these surface visually.

**Overall length constraint: terse.** This page is not a blog post, a changelog, or a build-log. The entire narrative content of the page should read in well under a minute. Each beat below is **a sentence or two, not a paragraph.** Fragments are fine. Density beats verbosity.

1. **The headline moment.** What was built and a glance-able sense of why it matters. One line.
2. **The product itself.** Something visual — screenshot, demo clip, link, embed. A way for the viewer to see/touch the thing. Visuals do the heavy lifting here, not copy.
3. **The story of the build — compressed.** Four micro-beats, each one short sentence:
   - **Where it started** (the trigger / the spark)
   - **The problem being solved** (what was actually being chased)
   - **For whom** (themselves? a friend? a target user?)
   - **Where it ended up** (the current state of the thing)
   This is the spine of the page and the bit most portfolios skip — but it should be felt as *honest fragments*, not a story arc essay.
4. **The tools and skills used.** AI tools, frameworks, services — surfaced as part of the journey, not as a tech-stack badge wall. A brief, human mention ("built with Claude + Cursor, hit a wall with X so switched to Y") is the target, not a logo grid.
5. **What worked and what failed.** A short honest cut. One or two lines is enough. This is the credibility lever — but credibility comes from candour, not length.
6. **Where it stands today.** Shipped / live / abandoned / iterating. One tag-like signal, not a paragraph.
7. **Who built it — name-tag treatment, not a profile.** A small builder card on the side of the page: photo, name, one-line sub-tag (role / vibe / what they're into). The viewer arrived from this person's social post, so they know who they came for — the page just needs to honestly place the builder next to the work, not introduce them. The full profile lives on a future profile page; this is a signature, not a bio.

---

## v0 Scope

- **One page type:** the project showcase page.
- **Three hardcoded example projects** — handpicked so the three together demonstrate range (different builders, different products, different tones). They are the proof that the page works for varied humans, not just one archetype.
- **No auth, no DB, no submission flow, no editor.** Data lives in code or static files.
- **No gallery/index page yet.** Each project page is its own URL, linked directly. The gallery is the *next* PRD.

## Out of scope for this PRD

- Gallery / discovery surface (next PRD)
- Submission / editor flow for a builder to create their own card
- Auth, accounts, profiles
- Auto-generation of cards from GitHub/AI tool activity
- Sharing mechanics beyond a plain URL (no embed, no OG-image generation yet)
- Comments, reactions, social actions
- Search, filtering, tags
- Mobile-specific design refinements (the page should *work* on mobile but mobile-first design is its own pass)

---

## Product decisions (resolved 2026-05-19)

The open questions from the first draft were walked through and decided. Recording them here so the design phase isn't free to relitigate:

1. **How long is the story? → Very short.** Sentence-or-two beats, not paragraphs. The narrative spine is start → problem → for whom → endpoint. No blog-post / no changelog. This is a strong constraint, not a hint.
2. **How prominent is the builder? → Present, not promoted.** Default entry is a social share from the builder themselves — they're already implicitly endorsed by the share. The builder appears as a **name-tag-style card to the side**: photo, name, one-line sub-tag. No top-of-page hero "Hi I'm…" treatment. The full profile (aggregating a builder's projects) is a separate future page, out of scope here.
3. **Is there a default tone? → No.** Tone is set by the builder, not the platform. The three example projects must read like three distinct humans. The platform's job is to get out of the way of their voice.
4. **What does "shareable" mean for v0? → Just a clean URL.** Default OG unfurl is fine. **No custom OG image generation for v0.** Reconsider once we have a submission flow and real cards.

### Considered model & rejected

We considered making the builder more dominant — a deeply personal, builder-first read (closer to "this is *my* showcase, here are *my* learnings") — under a hypothesis where the page works like a creator's personal landing. Rejected for v0 because:

- The default entry is a social share, which already foregrounds the builder.
- Making the builder dominant on every project page would compete with the future profile page.
- A vibe-coder PM is likely to have only a handful of projects — the *project* is the story-worthy unit, not the person. The profile aggregates; the project tells.

GitHub-analogy that shaped this: when you share yourself with someone, you might send your profile URL — but each repo still has to stand on its own as a story. We're optimising the repo page first.

---

## Acceptance criteria

The page is done for v0 when:

- **The social-share test.** This is the primary test, since the default entry is a builder sharing the link on LinkedIn / X / Slack / Reddit. The link's default unfurl is enough to make a peer click. On arrival, the viewer immediately gets that *this specific builder* made *this specific thing* — no confusion about whose project this is.
- **The 8-second test.** A viewer landing cold on the page knows within 8 seconds what was built, by whom, and gets a clear emotional read of the builder.
- **The pride test.** A real PM or designer, shown the page with their own project on it, would send it to someone. Not "tweak first and then send" — send.
- **The voice test.** The three example pages feel like three different people, not three slots in the same template.
- **The "not a portfolio template" test.** A person seeing the page does not think "oh, another portfolio." They think "what is this?" in a good way.
- **The story test.** The journey is the spine of the page, not a section at the bottom labeled "About this project."
- **The terseness test.** No beat reads as a paragraph. The whole narrative content of the page is consumable in under a minute. If a copy block runs long, it's wrong.

---

## Design phase

Three visual concepts will be produced in an external design tool, then brought back into this PRD as a `## Design concepts` section before the architect stage begins. Each concept should be a genuinely different *interpretation* of the emotional intent above — not three variations of the same layout.

**Hard constraints all three concepts must respect** (these are decided, not up for redesign):

- Copy is terse — sentence-fragments throughout, no paragraph blocks.
- The builder lives as a name-tag-style element on the side, not a top-of-page hero.
- Default entry is a social share — design for someone arriving cold from a link, not for someone browsing a feed.
- No custom OG image work in v0 — only the page itself matters.

Range we want the three concepts to explore *within* those constraints:

- **Visual-first / deck-style** — image + caption rhythm, minimal copy, the build speaks for itself.
- **Story-strip** — terse vertical narrative, four-beat story spine doing the work, almost like an interactive caption track.
- **Something more opinionated** — a layout that feels native to IBUILTTHIS specifically, not borrowed from Medium / Dribbble / Product Hunt / GitHub. Designer's wildcard.

Once a direction is chosen (or hybridised), the chosen concept gets pinned into this PRD and the PRD is signed off → architect stage.

---

## References / inspiration (to be filled in)

*Empty for now — the three design concepts will be the first entries here.*
