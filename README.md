Intl.NumberFormat Unified API Proposal
======================================

This proposal adds **measurement units**, **compact decimal notation**, and other localized number formatting features to Intl.NumberFormat.

[TC39 Stage 2 Proposal](https://docs.google.com/presentation/d/1_1D15PWniTlbLu1BOU9aDf5H87Ecq85i0CuLl5KA4DE/edit?usp=sharing)

[TC39 Stage 3 Proposal](https://docs.google.com/presentation/d/1BljVP4ENAqJt3CLXKapq_39Gr5RG6f1h9SQ_tsP7V8Y/edit?usp=sharing)

[View Spec](https://tc39.github.io/proposal-unified-intl-numberformat/)

## Background / Motivation

There are many requests for adding number-formatting-related features to ECMA-402 (the JavaScript Intl standard library). These include:

- [Expose narrow currency symbol](https://github.com/tc39/ecma402/issues/200) -- Section I
- [Clarification on behavior of SetNumberFormatDigitOptions](https://github.com/tc39/ecma402/issues/244) -- Section I
- [Add measure unit formatting](https://github.com/tc39/ecma402/issues/32) -- Section II
- [Add compact decimal notation](https://github.com/tc39/ecma402/issues/37) -- Section III
- [Add scientific notation](https://github.com/tc39/ecma402/issues/164) -- Section III
- [Add currency accounting format](https://github.com/tc39/ecma402/issues/186) -- Section IV
- [Add option to force sign](https://github.com/tc39/ecma402/issues/163) -- Section IV

These features are important to both end users and to Google.  Since most of these features require carrying along large amounts of locale data for proper i18n support, exposing these features via a JavaScript API reduces bandwidth and lowers the barrier to entry for i18n best practices.

Rather than complicate `Intl` with more constructors with heavilly overlapping functionality, this proposal is to restructure the spec of `Intl.NumberFormat` to make it more easilly support additional features in a "unified" way.

Additional background: [prior discussion](https://github.com/tc39/ecma402/issues/215)

## I. Units

Units of measurement can be formatted as follows:

```javascript
(9.81).toLocaleString("en-US", {
    style: "unit",
    unit: "meter-per-second-squared",
    unitDisplay: "short"
});
// ==> "9.81 m/s²"
```

The syntax was discussed in #3.

- `style` receives the string value "unit"
- `unit` receives a string core unit identifier, defined in [UTS #35, Part 2, Section 6](http://unicode.org/reports/tr35/tr35-general.html#Unit_Elements).  See also the [full list of unit identifiers](https://unicode.org/repos/cldr/tags/latest/common/validity/unit.xml).
- `unitDisplay`, named after the corresponding setting for currencies, `currencyDisplay`, takes either "narrow", "short", or "long".

## II. Scientific and Compact Notation

Scientific and compact notation are represented by the new option `notation` and can be formatted as follows:

```javascript
(987654321).toLocaleString("en-US", {
    notation: "scientific"
});
// ==> 9.877E8

(987654321).toLocaleString("en-US", {
    notation: "engineering"
});
// ==> 987.7E6

(987654321).toLocaleString("en-US", {
    notation: "compact",
    compactDisplay: "long"
});
// ==> 987.7 million
```

The syntax was discussed in #5.

- `notation` takes either "scientific", "engineering", "compact", or "plain"
- `compactDisplay`, used only when `notation` is "compact", takes either "short" or "long"

Rounding-related settings (min/max integer/fraction digits) are applied after the number is scaled according to the chosen notation.

When `notation` is "compact" and there are no user-specified rounding options, a special compact notation rounding strategy is used: round to the nearest integer, but always keep 2 significant digits.  For example, 123.4K rounds to 123K, and 1.234K rounds to 1.2K.  The user can determine that the compact rounding strategy is being used in the *resolvedOptions* if `notation` is "compact" *and* none of the rounding settings are present.

Notation styles are allowed to be combined with other options:

```javascript
(299792458).toLocaleString("en-US", {
    notation: "scientific",
    minimumFractionDigits: 2,
    maximumFractionDigits: 2,
    style: "unit",
    unit: "speed-meter-per-second"
});
// ==> 3.00E8 m/s
```

## III. Sign Display

The sign can be displayed on positive numbers:

```javascript
(55).toLocaleString("en-US", {
    signDisplay: "always"
});
// ==> +55
```

Currency accounting sign display is also supported via a new option. In many locales, accounting format means to wrap the number with parentheses instead of
appending a minus sign.  For example:

```javascript
(-100).toLocaleString("bn", {
    style: "currency",
    currency: "EUR",
    currencySign: "accounting"
});
// ==> (১০০.০০€)
```

The syntax was discussed in #6:

- `signDisplay`: "auto" (default), "always", "never", "except-zero"
  - See examples below.
- `currencySign`: "standard" (default), "accounting"
  - "accounting" enables the accounting format on currency values, as shown in the example above.  "standard", the default value, disables accounting formats.

Examples of signDisplay choices with Latin digits:

signDisplay | -1  | -0  | 0   | 1
----------- | --- | --- | --- | ---
auto        | -1  | -0  | 0   | 1
always      | -1  | -0  | +0  | +1
never       | 1   | 0   | 0   | 1
except-zero | -1  | -0  | 0   | +1

Examples of signDisplay choices with Latin digits when `currencySign` is "accounting":

signDisplay | -1     | -0     | 0      | 1
----------- | ------ | ------ | ------ | ------
auto        | (1.00) | (0.00) | 0.00   | 1.00
always      | (1.00) | (0.00) | +0.00  | +1.00
never       | 1.00   | 0.00   | 0.00   | 1.00
except-zero | (1.00) | (0.00) | 0.00   | +1.00

As usual, this may be combined with other options.

```javascript
(0.55).toLocaleString("en-US", {
    style: "percent",
    signDisplay: "except-zero"
});
// ==> +55%
```

## IV. Spec Cleanup

Certain sections of the spec have been refactored with the following objectives:

- Fix https://github.com/tc39/ecma402/issues/238 (currency long name has dependency on plural form, and the currency long name pattern has dependency on currencyWidth).
- Move pattern resolution out of the constructor to keep all internal fields of NumberFormat locale-agnostic, making it easier to reason about behavior in the format method.
- Fix https://github.com/tc39/proposal-unified-intl-numberformat/issues/2, to allow minimumIntegerDigits to control integer digit count when significant digits are used.

In addition, one missing option is added to the existing `currencyDisplay` setting: "narrow-symbol", which uses the CLDR narrow-format symbol:

```javascript
(100).toLocaleString("en-CA", {
    style: "currency",
    currency: "USD",
    currencyDisplay: "narrow-symbol"
});
// ==> "$100" (rather than "US$100")
```

