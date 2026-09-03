# PH Mobile Number Sanitizing (Fixed +63 Prefix Pattern)

Reusable pattern for any project that collects a Philippine mobile number: show a fixed,
non-editable `+63` badge next to the input, and normalize whatever the user types or pastes down
to the bare 10-digit local number (`9XXXXXXXXX`). Originated in the Home by Bami e-commerce
project (checkout guest phone + track-order lookup/recovery phone fields) — canonical source at
`02 - Projects/Active/E-Commerce/frontend/src/app/core/utils/ph-phone.util.ts`.

---

## The Problem

Most PH phone contacts, browser autofill, and copy-paste habits save numbers with the country
code already attached — `+63 917 123 4567`, `639171234567`, or with a leading trunk `0` —
`0917 123 4567`. If the input field already shows a fixed `+63` badge (a common, good UX pattern
so the user only ever types the local part), naively concatenating that badge with a pasted
number that *also* carries `+63`/`63` produces a broken double-prefixed number
(`+63639171234567`) and a confusing validation error right before checkout — a classic
abandoned-order trigger.

A formatter that only strips a **leading `0`** (a common first attempt) fixes the `09XXXXXXXXX`
case but not the `+639XXXXXXXXX` / `639XXXXXXXXX` paste case — the country code prefix quietly
survives and gets truncated instead of stripped, corrupting the number.

---

## The Fix

Normalize by stripping *any* of the equivalent leading forms — `+63`, `63`, or `0` — in one place,
then format for display. Two small pure functions, no framework dependency:

```typescript
/**
 * Normalizes a Philippine mobile number down to its bare 10-digit local form, starting with 9.
 * Strips a leading +63/63 country code or a leading 0 trunk prefix — needed because contact
 * apps and browser autofill usually save numbers with the country code, and pasting that into
 * a field that already shows a fixed "+63" badge would otherwise double up the prefix.
 * Examples:
 *  "+63 917 123 4567" -> "9171234567"
 *  "0917-123-4567"    -> "9171234567"
 *  "639171234567"     -> "9171234567"
 *  "9171234567"       -> "9171234567"
 */
export function sanitizePHMobileNumber(input: string): string {
  if (!input) return '';
  const digitsOnly = input.replace(/\D/g, '');
  return digitsOnly.replace(/^(?:63|0)?(9\d{0,9})$/, '$1');
}

/** Reformats a mobile-number input live as "9XX XXX XXXX", after normalizing away any leading
 *  +63/63/0 the user typed or pasted — the fixed +63 badge next to the input already covers
 *  that part. */
export function formatPHMobileAsTyped(raw: string): string {
  const digits = sanitizePHMobileNumber(raw).slice(0, 10);
  return [digits.slice(0, 3), digits.slice(3, 6), digits.slice(6, 10)].filter(Boolean).join(' ');
}
```

**Why the regex is anchored (`^...$`)**: it only rewrites the string when the *whole* thing is a
recognizable PH mobile shape. A garbage or mid-typing value (e.g. a lone `"0"`, or `"8171234567"`
which doesn't start with 9) fails to match and passes through unchanged — normalization never
mangles input it doesn't understand; a separate validity check is still responsible for rejecting
bad numbers.

**Known limitation**: `sanitizePHMobileNumber` requires the leading-9 local number to be present
somewhere the regex can anchor to. A double-mistake paste (e.g. `6363917...`) or a number missing
the leading 9 won't normalize and will surface as a validation error instead — acceptable, since
that input was never going to be a valid PH mobile number anyway.

---

## The UI Pattern

Pair the sanitizer with a fixed, non-editable `+63` badge so the user only ever types/pastes the
local part — this is what makes stripping a redundant `63`/`+63` from pasted input necessary in
the first place (without the fixed badge, you'd want to *keep* the country code instead).

```html
<label for="phone">Mobile Number</label>
<div class="flex items-stretch gap-2">
  <span class="phone-prefix-badge">+63</span>
  <input
    id="phone"
    [ngModel]="guestPhone"
    (input)="onPhoneInput($event)"
    name="guestPhone"
    type="tel"
    inputmode="numeric"
    maxlength="12"
    placeholder="912 345 6789"
    autocomplete="tel"
    class="flex-1 min-w-0" />
</div>
```

```typescript
onPhoneInput(event: Event): void {
  this.guestPhone = formatPHMobileAsTyped((event.target as HTMLInputElement).value);
}
```

`maxlength="12"` accounts for the two inserted spaces in `"9XX XXX XXXX"` (10 digits + 2 spaces).
Submit the sanitized/formatted local value as-is (e.g. `"917 123 4567"`) and let the backend do
its own normalization on receipt (e.g. a `PhoneNumberUtil.toE164Philippines`-style utility that
accepts `09XXXXXXXXX`, `9XXXXXXXXX`, `639XXXXXXXXX`, and `+639XXXXXXXXX` with optional
spaces/dashes) — don't re-attach `+63` on the frontend before sending; that just reintroduces the
same double-prefix risk one step later.

---

## Reuse Checklist

- [ ] One shared util file (not copy-pasted per field/page) — "fix once, fix both/all."
- [ ] Fixed, non-editable country-code badge next to the input, not part of the editable value.
- [ ] Sanitizer strips `+63`, `63`, **and** `0` leading forms — not just `0`.
- [ ] `maxlength` on the input accounts for inserted spacing characters.
- [ ] Backend validation/normalization is the source of truth; frontend formatting is cosmetic
      and must not change what gets submitted in a way the backend wouldn't already accept.
- [ ] Test all four shapes at minimum: `+63 9XX XXX XXXX`, `09XXXXXXXXX`, `639XXXXXXXXX` (no
      symbols), and the bare local `9XXXXXXXXX`.
