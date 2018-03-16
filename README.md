Intl.NumberFormat Unified API Proposal
======================================

## Motivation

There are many requests for adding number-formatting-related features to ECMA 402.  A few of them include:

- tc39/ecma402#200
- tc39/ecma402#186
- tc39/ecma402#164
- tc39/ecma402#163
- tc39/ecma402#95
- tc39/ecma402#91
- tc39/ecma402#37
- tc39/ecma402#32

Rather than complicate `Intl` with more subclasses with heavilly overlapping functionality, this proposal is to restructure the spec of `Intl.NumberFormat` to make it more easilly support additional features in a "unified" way.

Background: tc39/ecma402#215
