# Docs and README style

This document specifies the register and section structure for READMEs and other docs prose
in pipe23 projects.

## Register

- Every sentence states a fact: what a thing is, what it does, how to run it, what it needs.
  A sentence whose only job is emphasis, significance, or drama is cut.
- No aphoristic closers, no "not X but Y" reversals, no "deliberately", "simply", "literally",
  or "quietly", no restating a virtue a second time, no colon-flourishes, no personification of
  software.
- Target register: a good man page or Debian README.
- The subject of a sentence is the artifact, stating its function. Agentless passive ("is used
  to") is acceptable. Naming the user of a thing when the interface does not care who uses it
  is narration and is cut. An actor is the subject only when the actor is the fact — topology
  ("only the consumer backend talks to the verifier") or a responsibility boundary ("handling
  the rejection is the page's responsibility").
- No scene-setting clauses ("Before any of this...", "When a user needs X, ..."). A sentence
  starts at the fact.
- Headings are the noun a reader would grep for, never a step, an imperative, or an abstract
  "The X" phrase. No "What's not here" sections.
- No implementation vocabulary in behavior documentation. Document the observable contract,
  not the mechanism behind it.
- One fact per sentence. Multiple behaviors are not chained into one sentence with semicolons.
- Name the things a section covers rather than counting or summarizing them: list the actual
  routes, keys, or files, not a count or a paraphrase of them. No coined abstractions in place
  of the plain fact.
- A flat noun phrase is preferred over an em-dash appositive. An em-dash triad ("— X, Y, or
  Z") is redundant when the adjacent rendered content (a table, a list, a code block) already
  lists the same items. No vacuous example blocks.
- Terms come from the ecosystem itself (submodule, subclass, package, raises), not from
  architecture vocabulary ("surface", "hangs off", "contract").
- Design rationale has one home. It lives in the project's spec, not in a README or reference
  page.

## README skeleton

Section order: title, intro paragraph, badges, Installation, Usage, Configuration,
Documentation, Development, Status, License.

- **Title.** The heading text equals the repository or directory name.
- **Intro.** No "Overview" heading — the paragraph follows the title directly. The first
  sentence is a complete definition of what the project is. Safety-relevant facts (auth,
  network exposure, data handled) get one or two further sentences. While the project is
  experimental, the paragraph's final sentence is the stability hedge, "It is experimental and
  unstable."
- **Badges.** CI, docs, package, and license badges, placed below the intro paragraph.
- **Installation.** Command-first: the install command with no surrounding narration.
- **Usage.** Fenced examples showing real, captured command or code output, not invented
  output. The fenced-line budget for the section is about 25 lines total; one compound
  configuration key may take a longer single line. Error and reason-code vocabulary is
  enumerated on the docs site, not in the README; an example transcript may show one instance
  of a code without listing the rest.
- **Configuration.** The configuration keys the project reads, each with its default and its
  effect. A project with no configuration omits this section.
- **Documentation.** One line pointing at the full documentation URL.
- **Development.** The commands to set up the environment, run tests, lint, and type-check.
- **Status.** See below.
- **License.** The license identifier.

## Example-directory READMEs

A README inside an example directory (a demo, a compose stack, a worked sample built on the
parent project) is a delta on the skeleton, not a separate template.

- No Installation section — the example runs from the parent project checkout.
- A Routes section (or the equivalent surface for the example's kind: CLI subcommands,
  message topics) replaces the parent project's API reference material.
- The Documentation section becomes a pointer to the parent project's documentation, not to a
  documentation build of the example itself.

## Status section

- The first line is the blunt verdict sentence, "You should not rely on this code."
- The lines that follow are a bulleted list of verified, project-level limitations, stated as
  terse facts (platform coverage, missing rate limiting, in-process state, and similar).
- The intro paragraph's stability hedge (see README skeleton, above) stays in place alongside
  this section.
- The section sits between Development and License.
- At maturity, the section is deleted wholesale, along with the intro's stability hedge. It is
  never softened in place.
