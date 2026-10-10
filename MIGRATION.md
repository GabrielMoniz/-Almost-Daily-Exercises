# Migrating the existing day files

## Layout

```
main.tex
qiworkbook.sty
entries/
  TEMPLATE.tex
  q001.tex  q002.tex  ...        <- one file per problem, named by permanent ID
```

The old `days/` directory can be deleted once each file has been moved across.

## Numbering

Q-IDs happen to line up one-to-one with your old day numbers, so `Q001`–`Q007`
are your old Days 1–7 and nothing needs renaming. What changes is the *session*
grouping: old Days 5 and 6 both fall on 11 September, so they are now two
entries inside Day 5, and old Day 7 becomes Day 6. Check the dates in `main.tex`
against what actually happened — I guessed 11 and 12 September for the last two.

## Per-file conversion

Four mechanical edits to each old `days/dayNN.tex`:

1. `\daytitle{5}{Two POVMs that never disagree}` becomes the environment opener
   `\begin{entry}{Q005}{Two POVMs that never disagree}`, and the file ends with
   `\end{entry}`. The day number moves out to `main.tex`.
2. `\begin{daymeta} \item[Topic] ... \end{daymeta}` becomes `\entrymeta{topic =
   ..., est = ..., origin = ..., source = ..., prereq = ..., status = ...}`.
   Values containing a comma must be braced: `topic = {Trace norm, positivity}`.
3. `\begin{attemptlog} ... \end{attemptlog}` becomes `\attempt{date = ..., time
   = ..., difficulty = ..., howitwent = ...}`. Dates are ISO: `2026-09-11`.
4. Prose cross-references become links: "the Day 4 result" becomes
   `\qref{Q004}`, and a recurring lemma becomes `\usetool{T1}`.

Steps 1–3 are already done in the stub files; only the problem, solution and
takeaway bodies need pasting in, and the `howitwent` lines are blank for you to
fill or leave empty.

## Building

```
latexmk -pdf main.tex
latexmk -pdf -jobname=public "\PassOptionsToPackage{public}{qiworkbook}\input{main}"
```

The ledger table and the calendar are assembled from `.aux`, so they settle on
the second pass. `latexmk` handles this; a manual `pdflatex` needs running
twice.

The public build hides every attempt log, the `status` field, and the two
attempt-derived ledger columns. To publish one specific log, add `public,` to
that entry's `\attempt{...}`. `\todo{...}` prints nothing in the public build
but raises a warning, so nothing disappears silently.

## Notes

- `\wbstats` counts problems and working days; `\wbcalendar{2026}` draws the
  year grid. Add `\wbcalendar{2027}` below it when the time comes.
- The toolbox back-references ("Used in: Q001, Q004") are collected from
  `\usetool` automatically — nothing to maintain by hand.
- Dates are parsed, so `11/09/206` would now be caught as malformed rather than
  silently typeset.
