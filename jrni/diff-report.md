# Timezone data diff report

Installed gem: tzinfo 0.3.62  
Vendored data: tzinfo-data 1.2026.3 (IANA tzdata 2026c)  
Identifiers examined: 597

> **Superseded in part by the 1.2026.5 refresh (IANA tzdata 2026e).** Everything
> below -- the figures, the per-zone sections and the full enumeration -- is the
> 1.2026.3 sweep, and none of it was recomputed; only the three rows named here were
> re-annotated. Three identifiers recorded `identical` have since been corrected and
> now ship as definitions: `America/Bogota`, `America/Inuvik` and
> `America/Winnipeg`. Their rows in the full enumeration are marked
> `identical (superseded)`, but they are still absent from "Corrected zones
> (emitted)" and "Does the correction still change anything today?", and
> `America/Rainy_River` and `Canada/Central` -- which follow `America/Winnipeg` as
> aliases -- are absent from "Aliases of corrected zones". The cross-check under
> "Summary" no longer holds either: all three were classified `identical`, so they
> fall outside `differs + kind flip` and the identity cannot be restored by
> recounting.

Every identifier in the installed gem index is listed below. The point of the
full enumeration is to show the sweep was exhaustive, rather than limited to
the zones named in the original bug report.

## How a zone is judged

Two definitions are compared on their **resolved offsets**, not on the `:oN`
symbols they use and not on transition instants alone. A zone can keep every
transition instant unchanged and still change behaviour by pointing those
instants at renumbered offsets, and an epoch-only or line-oriented diff would
call that identical.

Behaviour is compared over the window 1970-01-01 to 
2070-01-01, evaluated at every instant where either
dataset changes offset. Because offsets are piecewise constant between
transitions, that is an exact comparison of the window rather than a sample.

The lower bound is 1970 because IANA states that timestamps before then are
best-effort and not guaranteed correct. Zones that differ only below it are
recorded as `differs before epoch only` and are NOT shipped: re-editing a
19th century local-mean-time value does nothing for appointment booking.

The window decides which zones are **classified** as changed. It does not limit
what the emitted files then do: a definition replaced for a 2024 rule change
carries the whole of its zone's history with it, including any part of that
history upstream has re-edited below 1970. Those changes ship whether or not
they were the reason for shipping, so they are enumerated below under
"Changes below the comparison window" rather than left to be discovered.

The upper bound exists because both dialects pre-generate recurring future
transitions but stop at different years (the installed gem in 2073, the
vendored release in 2076). Without a shared ceiling, every daylight-saving
zone on earth would look changed purely because one side ran out first.

## Summary

| Verdict | Count |
| --- | --- |
| identical | 305 |
| alias | 245 |
| differs before epoch only (not shipped) | 18 |
| differs | 24 |
| kind flip | 5 |
| **emitted** | 23 |
| **excluded by policy** | 6 |
| **aliases of corrected zones** | 11 |
| **all affected identifiers** | 34 |

Cross-check: emitted (23) + excluded (6) = differs + kind flip (29). Asserted by the generator.

## Sanity check against the recorded baseline

These are the magnitudes this sweep is known to produce. They are reported
against, never enforced -- a difference here is a finding to investigate, not a
number to make the generator hit. The planning estimates that used to sit in
this column were lower; why, and what the extra zones are, is written up in
`README.md` under "Why the sweep finds more than the plan expected".

| Quantity | Baseline | Actual | Match |
| --- | --- | --- | --- |
| identifiers | 597 | 597 | yes |
| differing | 29 | 29 | yes |
| emitted | 23 | 23 | yes |
| aliases | 11 | 11 | yes |
| all_affected | 34 | 34 | yes |

### Coverage of the zones the original plan named

The plan named these zones as needing correction. This is checked against the
emitted set on every run rather than asserted in prose, because a sweep that
quietly lost one of them would otherwise still claim it had not.

- `America/Vancouver` -- present in the emitted set
- `America/Edmonton` -- present in the emitted set
- `America/Asuncion` -- present in the emitted set
- `Asia/Almaty` -- present in the emitted set
- `Asia/Qostanay` -- present in the emitted set
- `America/Scoresbysund` -- present in the emitted set
- `Antarctica/Casey` -- present in the emitted set
- `Antarctica/Vostok` -- present in the emitted set

All 8 are present, so nothing the plan expected was lost.
The counts above are larger than the plan estimated, not different in kind.

## Reviewed and held: abstract zones that upstream turned into aliases

The timezone database has been converting its abstract, rule-only zones into
aliases of a representative real city. That is a change of meaning, not a
correction, and it was the one part of this output that could have made a
currently correct booking wrong.

It was raised for review and a decision was taken: **hold every one of them.**
They are excluded by policy below, and no definition file is written for them.
This is a deliberate hold pending a separate decision about what these
identifiers ought to mean, not a judgement that the newer data is wrong.

| Identifier | Would become an alias for | Consequence that was avoided |
| --- | --- | --- |
| `Asia/Choibalsan` | `Asia/Ulaanbaatar` | at 1983-04-14 it would change from +10 (+10:00) to +09 (+09:00) |
| `EET` | `Europe/Athens` | at 1975-04-25 it would change from EET (+02:00) to EEST (+03:00) |
| `MET` | `Europe/Brussels` | at 1970-01-15 it would change from MET (+01:00) to CET (+01:00) |
| `WET` | `Europe/Lisbon` | at 1970-01-15 it would change from WET (+00:00) to CET (+01:00) |

The sharpest case is `WET`. Today it means "western European time, with the
European daylight-saving rules". As an alias it would inherit a real country's
history, and Portugal spent the 1970s an hour ahead of western European time,
so stored bookings in that era would have moved by an hour.

## Corrected zones (emitted)

| Identifier | Was | Now | Probe instant | Expected offset |
| --- | --- | --- | --- | --- |
| `America/Asuncion` | -04 (-04:00) | -03 (-03:00) | 2025-04-06 00:00:00 UTC | -10800 |
| `America/Bahia_Banderas` | PST (-08:00) | MST (-07:00) | 1970-01-01 04:00:00 UTC | -25200 |
| `America/Cancun` | EST (-05:00) | CST (-06:00) | 1981-12-24 00:00:00 UTC | -21600 |
| `America/Edmonton` | MST (-07:00) | CST (-06:00) | 2026-11-15 00:00:00 UTC | -21600 |
| `America/Hermosillo` | PST (-08:00) | MST (-07:00) | 1970-01-01 04:00:00 UTC | -25200 |
| `America/Mazatlan` | PST (-08:00) | MST (-07:00) | 1970-01-01 04:00:00 UTC | -25200 |
| `America/Merida` | EST (-05:00) | CST (-06:00) | 1981-12-24 00:00:00 UTC | -21600 |
| `America/Nuuk` | -02 (-02:00) | -02 (-02:00) -- same total offset: what changed is the daylight-saving flag (standard -10800s plus daylight 3600s becomes standard -7200s plus daylight 0s), so a test of this zone has to assert on the period rather than the offset alone | 2023-04-09 00:00:00 UTC | -7200 |
| `America/Scoresbysund` | +00 (+00:00) | -01 (-01:00) | 2024-04-14 00:00:00 UTC | -3600 |
| `America/Tijuana` | PST (-08:00) | PDT (-07:00) | 1970-05-10 00:00:00 UTC | -25200 |
| `America/Vancouver` | PST (-08:00) | MST (-07:00) | 2026-11-15 00:00:00 UTC | -25200 |
| `Antarctica/Casey` | +11 (+11:00) | +08 (+08:00) | 2021-03-27 00:00:00 UTC | 28800 |
| `Antarctica/Vostok` | +06 (+06:00), via alias for Asia/Urumqi | +07 (+07:00) | 1970-01-15 00:00:00 UTC | 25200 |
| `Asia/Almaty` | +06 (+06:00) | +05 (+05:00) | 2024-03-14 00:00:00 UTC | 18000 |
| `Asia/Gaza` | EEST (+03:00) | EET (+02:00) | 2024-04-16 00:00:00 UTC | 7200 |
| `Asia/Hebron` | EEST (+03:00) | EET (+02:00) | 2024-04-16 00:00:00 UTC | 7200 |
| `Asia/Manila` | PST (+08:00) | PDT (+09:00) | 1977-04-10 00:00:00 UTC | 32400 |
| `Asia/Qostanay` | +06 (+06:00) | +05 (+05:00) | 2024-03-14 00:00:00 UTC | 18000 |
| `Asia/Tehran` | +04 (+04:00) | +0330 (+03:30) | 1978-11-24 00:00:00 UTC | 12600 |
| `Atlantic/Azores` | +00 (+00:00) | -01 (-01:00) | 1977-04-10 00:00:00 UTC | -3600 |
| `Atlantic/Madeira` | WEST (+01:00) | WET (+00:00) | 1977-04-10 00:00:00 UTC | 0 |
| `Europe/Chisinau` | EEST (+03:00) | EET (+02:00) | 2022-03-27 00:30:00 UTC | 7200 |
| `Europe/Lisbon` | WEST (+01:00) | WET (+00:00) | 1978-04-02 00:30:00 UTC | 0 |

## Does the correction still change anything today?

The probe above is anchored on the EARLIEST divergence, which for several zones
is a 1970s or 80s re-edit. On its own that says nothing about whether the
correction still matters now -- `Antarctica/Casey` is three hours wrong today
and its earliest divergence is decades old. So a second probe is derived inside
the present-day window and carried in the generated contract beside the first.

The window is 2026-01-01 to 2027-01-01: the calendar year of the IANA tzdata
release the vendored extract records. It is anchored on the data rather than on
the clock so that re-running the generator over the same extract reproduces the
same files, and so that refreshing the extract moves the window forward without
anyone having to remember to.

| Identifier | Present-day probe | Expected offset | Daylight saving | Offset alone proves it |
| --- | --- | --- | --- | --- |
| `America/Asuncion` | 2026-04-05 00:00:00 UTC | -10800 | no | yes |
| `America/Bahia_Banderas` | none -- the two datasets agree throughout the window | -- | -- | -- |
| `America/Cancun` | none -- the two datasets agree throughout the window | -- | -- | -- |
| `America/Edmonton` | 2026-11-15 00:00:00 UTC | -21600 | no | yes |
| `America/Hermosillo` | none -- the two datasets agree throughout the window | -- | -- | -- |
| `America/Mazatlan` | none -- the two datasets agree throughout the window | -- | -- | -- |
| `America/Merida` | none -- the two datasets agree throughout the window | -- | -- | -- |
| `America/Nuuk` | none -- the two datasets agree throughout the window | -- | -- | -- |
| `America/Scoresbysund` | 2026-01-15 00:00:00 UTC | -7200 | no | yes |
| `America/Tijuana` | none -- the two datasets agree throughout the window | -- | -- | -- |
| `America/Vancouver` | 2026-11-15 00:00:00 UTC | -25200 | no | yes |
| `Antarctica/Casey` | 2026-01-15 00:00:00 UTC | 28800 | no | yes |
| `Antarctica/Vostok` | 2026-01-15 00:00:00 UTC | 18000 | no | yes |
| `Asia/Almaty` | 2026-01-15 00:00:00 UTC | 18000 | no | yes |
| `Asia/Gaza` | none -- the two datasets agree throughout the window | -- | -- | -- |
| `Asia/Hebron` | none -- the two datasets agree throughout the window | -- | -- | -- |
| `Asia/Manila` | none -- the two datasets agree throughout the window | -- | -- | -- |
| `Asia/Qostanay` | 2026-01-15 00:00:00 UTC | 18000 | no | yes |
| `Asia/Tehran` | none -- the two datasets agree throughout the window | -- | -- | -- |
| `Atlantic/Azores` | none -- the two datasets agree throughout the window | -- | -- | -- |
| `Atlantic/Madeira` | none -- the two datasets agree throughout the window | -- | -- | -- |
| `Europe/Chisinau` | 2026-03-29 00:30:00 UTC | 7200 | no | yes |
| `Europe/Lisbon` | none -- the two datasets agree throughout the window | -- | -- | -- |

## Changes below the comparison window

The comparison window starts at 1970-01-01, but the definition files
shipped for the zones above replace their entire history, not just the part
inside the window. These zones also behave differently BEFORE the epoch. None of
it is the reason they are shipped; all of it ships with them, so it is
enumerated here rather than left implicit in the window prose above.

| Identifier | First pre-1970 difference | Largest shift | Seen at | Was | Now |
| --- | --- | --- | --- | --- | --- |
| `America/Bahia_Banderas` | 1927-06-10 | 1h 00m | 1927-06-10 | MST (-07:00) | CST (-06:00) |
| `America/Hermosillo` | 1927-06-10 | 1h 00m | 1927-06-10 | MST (-07:00) | CST (-06:00) |
| `America/Mazatlan` | 1927-06-10 | 1h 00m | 1927-06-10 | MST (-07:00) | CST (-06:00) |
| `America/Tijuana` | 1927-06-10 | 1h 00m | 1927-06-10 | PST (-08:00) | MST (-07:00) |
| `Antarctica/Vostok` | 1927-12-31 | 6h 00m | 1927-12-31 | +06 (+06:00) | -00 (+00:00) |
| `Asia/Manila` | 1844-12-31 | 24h 00m | 1844-12-31 | LMT (+08:04) | LMT (-15:56) |
| `Atlantic/Azores` | 1917-03-01 | 1h 00m | 1917-03-01 | -01 (-01:00) | -02 (-02:00) |
| `Atlantic/Madeira` | 1917-03-01 | 1h 00m | 1917-03-01 | +00 (+00:00) | -01 (-01:00) |
| `Europe/Lisbon` | 1917-02-28 | 1h 00m | 1917-02-28 | WEST (+01:00) | WET (+00:00) |

These are mostly local-mean-time and early-20th-century re-edits, which is
exactly the material IANA calls best-effort. They are listed so that the
"enumerated impact surface" of this change covers everything the payload
alters, not only the part the window selected on.

## Excluded by policy

Every zone here was found to differ inside the comparison window and is then
deliberately NOT shipped. The reason is printed against each one, because an
exclusion a reviewer cannot see is indistinguishable from a bug -- and because
the reasons are not all the same. Some are held back because the newer data is
worse for us; others because the newer data means something different.

- `Africa/Casablanca` -- differs, excluded by policy.
  Vendored data is LESS correct than the bundled data for instants after 2026-09-20: it omits the Ramadan-window rule still observed in Morocco, so shipping it would introduce a one-hour regression.
- `Africa/El_Aaiun` -- differs, excluded by policy.
  Vendored data is LESS correct than the bundled data for instants after 2026-09-20: it omits the Ramadan-window rule still observed in Morocco, so shipping it would introduce a one-hour regression.
- `Asia/Choibalsan` -- kind flip, excluded by policy.
  Upstream converted this from a standalone rule-only zone into an alias of a representative city. That is a change of meaning rather than a correction: the identifier stops naming a set of rules and starts naming a place, so it inherits that place-specific history, and stored records that the rule-only definition placed correctly can move by an hour. See the per-zone table in the diff report for what this one would have become and where its offset would have changed. Reviewed and held pending a separate decision, not because the data is wrong.
- `EET` -- kind flip, excluded by policy.
  Upstream converted this from a standalone rule-only zone into an alias of a representative city. That is a change of meaning rather than a correction: the identifier stops naming a set of rules and starts naming a place, so it inherits that place-specific history, and stored records that the rule-only definition placed correctly can move by an hour. See the per-zone table in the diff report for what this one would have become and where its offset would have changed. Reviewed and held pending a separate decision, not because the data is wrong.
- `MET` -- kind flip, excluded by policy.
  Upstream converted this from a standalone rule-only zone into an alias of a representative city. That is a change of meaning rather than a correction: the identifier stops naming a set of rules and starts naming a place, so it inherits that place-specific history, and stored records that the rule-only definition placed correctly can move by an hour. See the per-zone table in the diff report for what this one would have become and where its offset would have changed. Reviewed and held pending a separate decision, not because the data is wrong.
- `WET` -- kind flip, excluded by policy.
  Upstream converted this from a standalone rule-only zone into an alias of a representative city. That is a change of meaning rather than a correction: the identifier stops naming a set of rules and starts naming a place, so it inherits that place-specific history, and stored records that the rule-only definition placed correctly can move by an hour. See the per-zone table in the diff report for what this one would have become and where its offset would have changed. Reviewed and held pending a separate decision, not because the data is wrong.

## Aliases of corrected zones

These need no file of their own: tzinfo resolves a link by loading its target,
so they pick up the correction automatically. They are listed because their
behaviour changes too.

| Alias | Target |
| --- | --- |
| `America/Ensenada` | `America/Tijuana` |
| `America/Godthab` | `America/Nuuk` |
| `America/Santa_Isabel` | `America/Tijuana` |
| `America/Yellowknife` | `America/Edmonton` |
| `Canada/Mountain` | `America/Edmonton` |
| `Canada/Pacific` | `America/Vancouver` |
| `Europe/Tiraspol` | `Europe/Chisinau` |
| `Iran` | `Asia/Tehran` |
| `Mexico/BajaNorte` | `America/Tijuana` |
| `Mexico/BajaSur` | `America/Mazatlan` |
| `Portugal` | `Europe/Lisbon` |

## Present in the vendored release only

The installed gem has no identifier for these, so nothing in the application
can ever ask for them and no override is possible.

- `America/Coyhaique`

## Full enumeration

| # | Identifier | Verdict | Note |
| --- | --- | --- | --- |
| 1 | `Africa/Abidjan` | identical |  |
| 2 | `Africa/Accra` | alias | alias for Africa/Abidjan |
| 3 | `Africa/Addis_Ababa` | alias | alias for Africa/Nairobi |
| 4 | `Africa/Algiers` | identical |  |
| 5 | `Africa/Asmara` | alias | alias for Africa/Nairobi |
| 6 | `Africa/Asmera` | alias | alias for Africa/Nairobi |
| 7 | `Africa/Bamako` | alias | alias for Africa/Abidjan |
| 8 | `Africa/Bangui` | alias | alias for Africa/Lagos |
| 9 | `Africa/Banjul` | alias | alias for Africa/Abidjan |
| 10 | `Africa/Bissau` | identical |  |
| 11 | `Africa/Blantyre` | alias | alias for Africa/Maputo |
| 12 | `Africa/Brazzaville` | alias | alias for Africa/Lagos |
| 13 | `Africa/Bujumbura` | alias | alias for Africa/Maputo |
| 14 | `Africa/Cairo` | identical |  |
| 15 | `Africa/Casablanca` | differs, excluded by policy | Vendored data is LESS correct than the bundled data for instants after 2026-09-20: it omits the Ramadan-window rule still observed in Morocco, so shipping it would introduce a one-hour regression. |
| 16 | `Africa/Ceuta` | identical |  |
| 17 | `Africa/Conakry` | alias | alias for Africa/Abidjan |
| 18 | `Africa/Dakar` | alias | alias for Africa/Abidjan |
| 19 | `Africa/Dar_es_Salaam` | alias | alias for Africa/Nairobi |
| 20 | `Africa/Djibouti` | alias | alias for Africa/Nairobi |
| 21 | `Africa/Douala` | alias | alias for Africa/Lagos |
| 22 | `Africa/El_Aaiun` | differs, excluded by policy | Vendored data is LESS correct than the bundled data for instants after 2026-09-20: it omits the Ramadan-window rule still observed in Morocco, so shipping it would introduce a one-hour regression. |
| 23 | `Africa/Freetown` | alias | alias for Africa/Abidjan |
| 24 | `Africa/Gaborone` | alias | alias for Africa/Maputo |
| 25 | `Africa/Harare` | alias | alias for Africa/Maputo |
| 26 | `Africa/Johannesburg` | identical |  |
| 27 | `Africa/Juba` | identical |  |
| 28 | `Africa/Kampala` | alias | alias for Africa/Nairobi |
| 29 | `Africa/Khartoum` | identical |  |
| 30 | `Africa/Kigali` | alias | alias for Africa/Maputo |
| 31 | `Africa/Kinshasa` | alias | alias for Africa/Lagos |
| 32 | `Africa/Lagos` | identical |  |
| 33 | `Africa/Libreville` | alias | alias for Africa/Lagos |
| 34 | `Africa/Lome` | alias | alias for Africa/Abidjan |
| 35 | `Africa/Luanda` | alias | alias for Africa/Lagos |
| 36 | `Africa/Lubumbashi` | alias | alias for Africa/Maputo |
| 37 | `Africa/Lusaka` | alias | alias for Africa/Maputo |
| 38 | `Africa/Malabo` | alias | alias for Africa/Lagos |
| 39 | `Africa/Maputo` | differs before epoch only (not shipped) | differs only before 1970-01-01 |
| 40 | `Africa/Maseru` | alias | alias for Africa/Johannesburg |
| 41 | `Africa/Mbabane` | alias | alias for Africa/Johannesburg |
| 42 | `Africa/Mogadishu` | alias | alias for Africa/Nairobi |
| 43 | `Africa/Monrovia` | identical |  |
| 44 | `Africa/Nairobi` | identical |  |
| 45 | `Africa/Ndjamena` | identical |  |
| 46 | `Africa/Niamey` | alias | alias for Africa/Lagos |
| 47 | `Africa/Nouakchott` | alias | alias for Africa/Abidjan |
| 48 | `Africa/Ouagadougou` | alias | alias for Africa/Abidjan |
| 49 | `Africa/Porto-Novo` | alias | alias for Africa/Lagos |
| 50 | `Africa/Sao_Tome` | identical |  |
| 51 | `Africa/Timbuktu` | alias | alias for Africa/Abidjan |
| 52 | `Africa/Tripoli` | identical |  |
| 53 | `Africa/Tunis` | identical |  |
| 54 | `Africa/Windhoek` | identical |  |
| 55 | `America/Adak` | identical |  |
| 56 | `America/Anchorage` | identical |  |
| 57 | `America/Anguilla` | alias | alias for America/Puerto_Rico |
| 58 | `America/Antigua` | alias | alias for America/Puerto_Rico |
| 59 | `America/Araguaina` | identical |  |
| 60 | `America/Argentina/Buenos_Aires` | identical |  |
| 61 | `America/Argentina/Catamarca` | identical |  |
| 62 | `America/Argentina/ComodRivadavia` | alias | alias for America/Argentina/Catamarca |
| 63 | `America/Argentina/Cordoba` | identical |  |
| 64 | `America/Argentina/Jujuy` | identical |  |
| 65 | `America/Argentina/La_Rioja` | identical |  |
| 66 | `America/Argentina/Mendoza` | identical |  |
| 67 | `America/Argentina/Rio_Gallegos` | identical |  |
| 68 | `America/Argentina/Salta` | identical |  |
| 69 | `America/Argentina/San_Juan` | identical |  |
| 70 | `America/Argentina/San_Luis` | identical |  |
| 71 | `America/Argentina/Tucuman` | identical |  |
| 72 | `America/Argentina/Ushuaia` | identical |  |
| 73 | `America/Aruba` | alias | alias for America/Puerto_Rico |
| 74 | `America/Asuncion` | differs |  |
| 75 | `America/Atikokan` | alias | alias for America/Panama |
| 76 | `America/Atka` | alias | alias for America/Adak |
| 77 | `America/Bahia` | identical |  |
| 78 | `America/Bahia_Banderas` | differs |  |
| 79 | `America/Barbados` | identical |  |
| 80 | `America/Belem` | identical |  |
| 81 | `America/Belize` | identical |  |
| 82 | `America/Blanc-Sablon` | alias | alias for America/Puerto_Rico |
| 83 | `America/Boa_Vista` | identical |  |
| 84 | `America/Bogota` | identical (superseded) | identical under 1.2026.3; corrected in the 1.2026.5 refresh (1992 transition) |
| 85 | `America/Boise` | identical |  |
| 86 | `America/Buenos_Aires` | alias | alias for America/Argentina/Buenos_Aires |
| 87 | `America/Cambridge_Bay` | identical |  |
| 88 | `America/Campo_Grande` | identical |  |
| 89 | `America/Cancun` | differs |  |
| 90 | `America/Caracas` | identical |  |
| 91 | `America/Catamarca` | alias | alias for America/Argentina/Catamarca |
| 92 | `America/Cayenne` | identical |  |
| 93 | `America/Cayman` | alias | alias for America/Panama |
| 94 | `America/Chicago` | identical |  |
| 95 | `America/Chihuahua` | differs before epoch only (not shipped) | differs only before 1970-01-01 |
| 96 | `America/Ciudad_Juarez` | differs before epoch only (not shipped) | differs only before 1970-01-01 |
| 97 | `America/Coral_Harbour` | alias | alias for America/Panama |
| 98 | `America/Cordoba` | alias | alias for America/Argentina/Cordoba |
| 99 | `America/Costa_Rica` | identical |  |
| 100 | `America/Creston` | alias | alias for America/Phoenix |
| 101 | `America/Cuiaba` | identical |  |
| 102 | `America/Curacao` | alias | alias for America/Puerto_Rico |
| 103 | `America/Danmarkshavn` | identical |  |
| 104 | `America/Dawson` | identical |  |
| 105 | `America/Dawson_Creek` | identical |  |
| 106 | `America/Denver` | identical |  |
| 107 | `America/Detroit` | identical |  |
| 108 | `America/Dominica` | alias | alias for America/Puerto_Rico |
| 109 | `America/Edmonton` | differs |  |
| 110 | `America/Eirunepe` | identical |  |
| 111 | `America/El_Salvador` | identical |  |
| 112 | `America/Ensenada` | alias | alias for America/Tijuana |
| 113 | `America/Fort_Nelson` | identical |  |
| 114 | `America/Fort_Wayne` | alias | alias for America/Indiana/Indianapolis |
| 115 | `America/Fortaleza` | identical |  |
| 116 | `America/Glace_Bay` | identical |  |
| 117 | `America/Godthab` | alias | alias for America/Nuuk |
| 118 | `America/Goose_Bay` | identical |  |
| 119 | `America/Grand_Turk` | identical |  |
| 120 | `America/Grenada` | alias | alias for America/Puerto_Rico |
| 121 | `America/Guadeloupe` | alias | alias for America/Puerto_Rico |
| 122 | `America/Guatemala` | identical |  |
| 123 | `America/Guayaquil` | identical |  |
| 124 | `America/Guyana` | identical |  |
| 125 | `America/Halifax` | identical |  |
| 126 | `America/Havana` | identical |  |
| 127 | `America/Hermosillo` | differs |  |
| 128 | `America/Indiana/Indianapolis` | identical |  |
| 129 | `America/Indiana/Knox` | identical |  |
| 130 | `America/Indiana/Marengo` | identical |  |
| 131 | `America/Indiana/Petersburg` | identical |  |
| 132 | `America/Indiana/Tell_City` | identical |  |
| 133 | `America/Indiana/Vevay` | identical |  |
| 134 | `America/Indiana/Vincennes` | identical |  |
| 135 | `America/Indiana/Winamac` | identical |  |
| 136 | `America/Indianapolis` | alias | alias for America/Indiana/Indianapolis |
| 137 | `America/Inuvik` | identical (superseded) | identical under 1.2026.3; corrected in the 1.2026.5 refresh (permanent DST from 2026-11-01) |
| 138 | `America/Iqaluit` | identical |  |
| 139 | `America/Jamaica` | identical |  |
| 140 | `America/Jujuy` | alias | alias for America/Argentina/Jujuy |
| 141 | `America/Juneau` | identical |  |
| 142 | `America/Kentucky/Louisville` | identical |  |
| 143 | `America/Kentucky/Monticello` | identical |  |
| 144 | `America/Knox_IN` | alias | alias for America/Indiana/Knox |
| 145 | `America/Kralendijk` | alias | alias for America/Puerto_Rico |
| 146 | `America/La_Paz` | identical |  |
| 147 | `America/Lima` | identical |  |
| 148 | `America/Los_Angeles` | identical |  |
| 149 | `America/Louisville` | alias | alias for America/Kentucky/Louisville |
| 150 | `America/Lower_Princes` | alias | alias for America/Puerto_Rico |
| 151 | `America/Maceio` | identical |  |
| 152 | `America/Managua` | identical |  |
| 153 | `America/Manaus` | identical |  |
| 154 | `America/Marigot` | alias | alias for America/Puerto_Rico |
| 155 | `America/Martinique` | identical |  |
| 156 | `America/Matamoros` | identical |  |
| 157 | `America/Mazatlan` | differs |  |
| 158 | `America/Mendoza` | alias | alias for America/Argentina/Mendoza |
| 159 | `America/Menominee` | identical |  |
| 160 | `America/Merida` | differs |  |
| 161 | `America/Metlakatla` | identical |  |
| 162 | `America/Mexico_City` | differs before epoch only (not shipped) | differs only before 1970-01-01 |
| 163 | `America/Miquelon` | differs before epoch only (not shipped) | differs only before 1970-01-01 |
| 164 | `America/Moncton` | identical |  |
| 165 | `America/Monterrey` | differs before epoch only (not shipped) | differs only before 1970-01-01 |
| 166 | `America/Montevideo` | identical |  |
| 167 | `America/Montreal` | alias | alias for America/Toronto |
| 168 | `America/Montserrat` | alias | alias for America/Puerto_Rico |
| 169 | `America/Nassau` | alias | alias for America/Toronto |
| 170 | `America/New_York` | identical |  |
| 171 | `America/Nipigon` | alias | alias for America/Toronto |
| 172 | `America/Nome` | identical |  |
| 173 | `America/Noronha` | identical |  |
| 174 | `America/North_Dakota/Beulah` | identical |  |
| 175 | `America/North_Dakota/Center` | identical |  |
| 176 | `America/North_Dakota/New_Salem` | identical |  |
| 177 | `America/Nuuk` | differs |  |
| 178 | `America/Ojinaga` | differs before epoch only (not shipped) | differs only before 1970-01-01 |
| 179 | `America/Panama` | identical |  |
| 180 | `America/Pangnirtung` | alias | alias for America/Iqaluit |
| 181 | `America/Paramaribo` | identical |  |
| 182 | `America/Phoenix` | identical |  |
| 183 | `America/Port-au-Prince` | identical |  |
| 184 | `America/Port_of_Spain` | alias | alias for America/Puerto_Rico |
| 185 | `America/Porto_Acre` | alias | alias for America/Rio_Branco |
| 186 | `America/Porto_Velho` | identical |  |
| 187 | `America/Puerto_Rico` | identical |  |
| 188 | `America/Punta_Arenas` | identical |  |
| 189 | `America/Rainy_River` | alias | alias for America/Winnipeg |
| 190 | `America/Rankin_Inlet` | identical |  |
| 191 | `America/Recife` | identical |  |
| 192 | `America/Regina` | identical |  |
| 193 | `America/Resolute` | identical |  |
| 194 | `America/Rio_Branco` | identical |  |
| 195 | `America/Rosario` | alias | alias for America/Argentina/Cordoba |
| 196 | `America/Santa_Isabel` | alias | alias for America/Tijuana |
| 197 | `America/Santarem` | identical |  |
| 198 | `America/Santiago` | identical |  |
| 199 | `America/Santo_Domingo` | identical |  |
| 200 | `America/Sao_Paulo` | identical |  |
| 201 | `America/Scoresbysund` | differs |  |
| 202 | `America/Shiprock` | alias | alias for America/Denver |
| 203 | `America/Sitka` | identical |  |
| 204 | `America/St_Barthelemy` | alias | alias for America/Puerto_Rico |
| 205 | `America/St_Johns` | identical |  |
| 206 | `America/St_Kitts` | alias | alias for America/Puerto_Rico |
| 207 | `America/St_Lucia` | alias | alias for America/Puerto_Rico |
| 208 | `America/St_Thomas` | alias | alias for America/Puerto_Rico |
| 209 | `America/St_Vincent` | alias | alias for America/Puerto_Rico |
| 210 | `America/Swift_Current` | identical |  |
| 211 | `America/Tegucigalpa` | identical |  |
| 212 | `America/Thule` | identical |  |
| 213 | `America/Thunder_Bay` | alias | alias for America/Toronto |
| 214 | `America/Tijuana` | differs |  |
| 215 | `America/Toronto` | differs before epoch only (not shipped) | differs only before 1970-01-01 |
| 216 | `America/Tortola` | alias | alias for America/Puerto_Rico |
| 217 | `America/Vancouver` | differs |  |
| 218 | `America/Virgin` | alias | alias for America/Puerto_Rico |
| 219 | `America/Whitehorse` | identical |  |
| 220 | `America/Winnipeg` | identical (superseded) | identical under 1.2026.3; corrected in the 1.2026.5 refresh (permanent DST from 2026-11-01) |
| 221 | `America/Yakutat` | identical |  |
| 222 | `America/Yellowknife` | alias | alias for America/Edmonton |
| 223 | `Antarctica/Casey` | differs |  |
| 224 | `Antarctica/Davis` | identical |  |
| 225 | `Antarctica/DumontDUrville` | alias | alias for Pacific/Port_Moresby |
| 226 | `Antarctica/Macquarie` | identical |  |
| 227 | `Antarctica/Mawson` | identical |  |
| 228 | `Antarctica/McMurdo` | alias | alias for Pacific/Auckland |
| 229 | `Antarctica/Palmer` | identical |  |
| 230 | `Antarctica/Rothera` | identical |  |
| 231 | `Antarctica/South_Pole` | alias | alias for Pacific/Auckland |
| 232 | `Antarctica/Syowa` | alias | alias for Asia/Riyadh |
| 233 | `Antarctica/Troll` | identical |  |
| 234 | `Antarctica/Vostok` | kind flip | linked in the installed gem, data in the vendored release |
| 235 | `Arctic/Longyearbyen` | alias | alias for Europe/Berlin |
| 236 | `Asia/Aden` | alias | alias for Asia/Riyadh |
| 237 | `Asia/Almaty` | differs |  |
| 238 | `Asia/Amman` | identical |  |
| 239 | `Asia/Anadyr` | identical |  |
| 240 | `Asia/Aqtau` | identical |  |
| 241 | `Asia/Aqtobe` | identical |  |
| 242 | `Asia/Ashgabat` | identical |  |
| 243 | `Asia/Ashkhabad` | alias | alias for Asia/Ashgabat |
| 244 | `Asia/Atyrau` | identical |  |
| 245 | `Asia/Baghdad` | identical |  |
| 246 | `Asia/Bahrain` | alias | alias for Asia/Qatar |
| 247 | `Asia/Baku` | identical |  |
| 248 | `Asia/Bangkok` | identical |  |
| 249 | `Asia/Barnaul` | identical |  |
| 250 | `Asia/Beirut` | identical |  |
| 251 | `Asia/Bishkek` | identical |  |
| 252 | `Asia/Brunei` | alias | alias for Asia/Kuching |
| 253 | `Asia/Calcutta` | alias | alias for Asia/Kolkata |
| 254 | `Asia/Chita` | identical |  |
| 255 | `Asia/Choibalsan` | kind flip, excluded by policy | Upstream converted this from a standalone rule-only zone into an alias of a representative city. That is a change of meaning rather than a correction: the identifier stops naming a set of rules and starts naming a place, so it inherits that place-specific history, and stored records that the rule-only definition placed correctly can move by an hour. See the per-zone table in the diff report for what this one would have become and where its offset would have changed. Reviewed and held pending a separate decision, not because the data is wrong. |
| 256 | `Asia/Chongqing` | alias | alias for Asia/Shanghai |
| 257 | `Asia/Chungking` | alias | alias for Asia/Shanghai |
| 258 | `Asia/Colombo` | identical |  |
| 259 | `Asia/Dacca` | alias | alias for Asia/Dhaka |
| 260 | `Asia/Damascus` | identical |  |
| 261 | `Asia/Dhaka` | identical |  |
| 262 | `Asia/Dili` | differs before epoch only (not shipped) | differs only before 1970-01-01 |
| 263 | `Asia/Dubai` | identical |  |
| 264 | `Asia/Dushanbe` | identical |  |
| 265 | `Asia/Famagusta` | identical |  |
| 266 | `Asia/Gaza` | differs |  |
| 267 | `Asia/Harbin` | alias | alias for Asia/Shanghai |
| 268 | `Asia/Hebron` | differs |  |
| 269 | `Asia/Ho_Chi_Minh` | differs before epoch only (not shipped) | differs only before 1970-01-01 |
| 270 | `Asia/Hong_Kong` | identical |  |
| 271 | `Asia/Hovd` | identical |  |
| 272 | `Asia/Irkutsk` | identical |  |
| 273 | `Asia/Istanbul` | alias | alias for Europe/Istanbul |
| 274 | `Asia/Jakarta` | identical |  |
| 275 | `Asia/Jayapura` | identical |  |
| 276 | `Asia/Jerusalem` | identical |  |
| 277 | `Asia/Kabul` | identical |  |
| 278 | `Asia/Kamchatka` | identical |  |
| 279 | `Asia/Karachi` | identical |  |
| 280 | `Asia/Kashgar` | alias | alias for Asia/Urumqi |
| 281 | `Asia/Kathmandu` | identical |  |
| 282 | `Asia/Katmandu` | alias | alias for Asia/Kathmandu |
| 283 | `Asia/Khandyga` | identical |  |
| 284 | `Asia/Kolkata` | identical |  |
| 285 | `Asia/Krasnoyarsk` | identical |  |
| 286 | `Asia/Kuala_Lumpur` | alias | alias for Asia/Singapore |
| 287 | `Asia/Kuching` | identical |  |
| 288 | `Asia/Kuwait` | alias | alias for Asia/Riyadh |
| 289 | `Asia/Macao` | alias | alias for Asia/Macau |
| 290 | `Asia/Macau` | identical |  |
| 291 | `Asia/Magadan` | identical |  |
| 292 | `Asia/Makassar` | identical |  |
| 293 | `Asia/Manila` | differs |  |
| 294 | `Asia/Muscat` | alias | alias for Asia/Dubai |
| 295 | `Asia/Nicosia` | identical |  |
| 296 | `Asia/Novokuznetsk` | identical |  |
| 297 | `Asia/Novosibirsk` | identical |  |
| 298 | `Asia/Omsk` | identical |  |
| 299 | `Asia/Oral` | identical |  |
| 300 | `Asia/Phnom_Penh` | alias | alias for Asia/Bangkok |
| 301 | `Asia/Pontianak` | identical |  |
| 302 | `Asia/Pyongyang` | identical |  |
| 303 | `Asia/Qatar` | identical |  |
| 304 | `Asia/Qostanay` | differs |  |
| 305 | `Asia/Qyzylorda` | identical |  |
| 306 | `Asia/Rangoon` | alias | alias for Asia/Yangon |
| 307 | `Asia/Riyadh` | identical |  |
| 308 | `Asia/Saigon` | alias | alias for Asia/Ho_Chi_Minh |
| 309 | `Asia/Sakhalin` | identical |  |
| 310 | `Asia/Samarkand` | identical |  |
| 311 | `Asia/Seoul` | identical |  |
| 312 | `Asia/Shanghai` | identical |  |
| 313 | `Asia/Singapore` | identical |  |
| 314 | `Asia/Srednekolymsk` | identical |  |
| 315 | `Asia/Taipei` | identical |  |
| 316 | `Asia/Tashkent` | identical |  |
| 317 | `Asia/Tbilisi` | identical |  |
| 318 | `Asia/Tehran` | differs |  |
| 319 | `Asia/Tel_Aviv` | alias | alias for Asia/Jerusalem |
| 320 | `Asia/Thimbu` | alias | alias for Asia/Thimphu |
| 321 | `Asia/Thimphu` | identical |  |
| 322 | `Asia/Tokyo` | identical |  |
| 323 | `Asia/Tomsk` | identical |  |
| 324 | `Asia/Ujung_Pandang` | alias | alias for Asia/Makassar |
| 325 | `Asia/Ulaanbaatar` | identical |  |
| 326 | `Asia/Ulan_Bator` | alias | alias for Asia/Ulaanbaatar |
| 327 | `Asia/Urumqi` | identical |  |
| 328 | `Asia/Ust-Nera` | identical |  |
| 329 | `Asia/Vientiane` | alias | alias for Asia/Bangkok |
| 330 | `Asia/Vladivostok` | identical |  |
| 331 | `Asia/Yakutsk` | identical |  |
| 332 | `Asia/Yangon` | identical |  |
| 333 | `Asia/Yekaterinburg` | identical |  |
| 334 | `Asia/Yerevan` | identical |  |
| 335 | `Atlantic/Azores` | differs |  |
| 336 | `Atlantic/Bermuda` | identical |  |
| 337 | `Atlantic/Canary` | identical |  |
| 338 | `Atlantic/Cape_Verde` | identical |  |
| 339 | `Atlantic/Faeroe` | alias | alias for Atlantic/Faroe |
| 340 | `Atlantic/Faroe` | identical |  |
| 341 | `Atlantic/Jan_Mayen` | alias | alias for Europe/Berlin |
| 342 | `Atlantic/Madeira` | differs |  |
| 343 | `Atlantic/Reykjavik` | alias | alias for Africa/Abidjan |
| 344 | `Atlantic/South_Georgia` | identical |  |
| 345 | `Atlantic/St_Helena` | alias | alias for Africa/Abidjan |
| 346 | `Atlantic/Stanley` | identical |  |
| 347 | `Australia/ACT` | alias | alias for Australia/Sydney |
| 348 | `Australia/Adelaide` | identical |  |
| 349 | `Australia/Brisbane` | identical |  |
| 350 | `Australia/Broken_Hill` | identical |  |
| 351 | `Australia/Canberra` | alias | alias for Australia/Sydney |
| 352 | `Australia/Currie` | alias | alias for Australia/Hobart |
| 353 | `Australia/Darwin` | identical |  |
| 354 | `Australia/Eucla` | identical |  |
| 355 | `Australia/Hobart` | identical |  |
| 356 | `Australia/LHI` | alias | alias for Australia/Lord_Howe |
| 357 | `Australia/Lindeman` | identical |  |
| 358 | `Australia/Lord_Howe` | identical |  |
| 359 | `Australia/Melbourne` | identical |  |
| 360 | `Australia/NSW` | alias | alias for Australia/Sydney |
| 361 | `Australia/North` | alias | alias for Australia/Darwin |
| 362 | `Australia/Perth` | identical |  |
| 363 | `Australia/Queensland` | alias | alias for Australia/Brisbane |
| 364 | `Australia/South` | alias | alias for Australia/Adelaide |
| 365 | `Australia/Sydney` | identical |  |
| 366 | `Australia/Tasmania` | alias | alias for Australia/Hobart |
| 367 | `Australia/Victoria` | alias | alias for Australia/Melbourne |
| 368 | `Australia/West` | alias | alias for Australia/Perth |
| 369 | `Australia/Yancowinna` | alias | alias for Australia/Broken_Hill |
| 370 | `Brazil/Acre` | alias | alias for America/Rio_Branco |
| 371 | `Brazil/DeNoronha` | alias | alias for America/Noronha |
| 372 | `Brazil/East` | alias | alias for America/Sao_Paulo |
| 373 | `Brazil/West` | alias | alias for America/Manaus |
| 374 | `CET` | differs before epoch only (not shipped) | data in the installed gem, linked in the vendored release |
| 375 | `CST6CDT` | differs before epoch only (not shipped) | data in the installed gem, linked in the vendored release |
| 376 | `Canada/Atlantic` | alias | alias for America/Halifax |
| 377 | `Canada/Central` | alias | alias for America/Winnipeg |
| 378 | `Canada/Eastern` | alias | alias for America/Toronto |
| 379 | `Canada/Mountain` | alias | alias for America/Edmonton |
| 380 | `Canada/Newfoundland` | alias | alias for America/St_Johns |
| 381 | `Canada/Pacific` | alias | alias for America/Vancouver |
| 382 | `Canada/Saskatchewan` | alias | alias for America/Regina |
| 383 | `Canada/Yukon` | alias | alias for America/Whitehorse |
| 384 | `Chile/Continental` | alias | alias for America/Santiago |
| 385 | `Chile/EasterIsland` | alias | alias for Pacific/Easter |
| 386 | `Cuba` | alias | alias for America/Havana |
| 387 | `EET` | kind flip, excluded by policy | Upstream converted this from a standalone rule-only zone into an alias of a representative city. That is a change of meaning rather than a correction: the identifier stops naming a set of rules and starts naming a place, so it inherits that place-specific history, and stored records that the rule-only definition placed correctly can move by an hour. See the per-zone table in the diff report for what this one would have become and where its offset would have changed. Reviewed and held pending a separate decision, not because the data is wrong. |
| 388 | `EST` | differs before epoch only (not shipped) | data in the installed gem, linked in the vendored release |
| 389 | `EST5EDT` | differs before epoch only (not shipped) | data in the installed gem, linked in the vendored release |
| 390 | `Egypt` | alias | alias for Africa/Cairo |
| 391 | `Eire` | alias | alias for Europe/Dublin |
| 392 | `Etc/GMT` | identical |  |
| 393 | `Etc/GMT+0` | alias | alias for Etc/GMT |
| 394 | `Etc/GMT+1` | identical |  |
| 395 | `Etc/GMT+10` | identical |  |
| 396 | `Etc/GMT+11` | identical |  |
| 397 | `Etc/GMT+12` | identical |  |
| 398 | `Etc/GMT+2` | identical |  |
| 399 | `Etc/GMT+3` | identical |  |
| 400 | `Etc/GMT+4` | identical |  |
| 401 | `Etc/GMT+5` | identical |  |
| 402 | `Etc/GMT+6` | identical |  |
| 403 | `Etc/GMT+7` | identical |  |
| 404 | `Etc/GMT+8` | identical |  |
| 405 | `Etc/GMT+9` | identical |  |
| 406 | `Etc/GMT-0` | alias | alias for Etc/GMT |
| 407 | `Etc/GMT-1` | identical |  |
| 408 | `Etc/GMT-10` | identical |  |
| 409 | `Etc/GMT-11` | identical |  |
| 410 | `Etc/GMT-12` | identical |  |
| 411 | `Etc/GMT-13` | identical |  |
| 412 | `Etc/GMT-14` | identical |  |
| 413 | `Etc/GMT-2` | identical |  |
| 414 | `Etc/GMT-3` | identical |  |
| 415 | `Etc/GMT-4` | identical |  |
| 416 | `Etc/GMT-5` | identical |  |
| 417 | `Etc/GMT-6` | identical |  |
| 418 | `Etc/GMT-7` | identical |  |
| 419 | `Etc/GMT-8` | identical |  |
| 420 | `Etc/GMT-9` | identical |  |
| 421 | `Etc/GMT0` | alias | alias for Etc/GMT |
| 422 | `Etc/Greenwich` | alias | alias for Etc/GMT |
| 423 | `Etc/UCT` | alias | alias for Etc/UTC |
| 424 | `Etc/UTC` | identical |  |
| 425 | `Etc/Universal` | alias | alias for Etc/UTC |
| 426 | `Etc/Zulu` | alias | alias for Etc/UTC |
| 427 | `Europe/Amsterdam` | alias | alias for Europe/Brussels |
| 428 | `Europe/Andorra` | identical |  |
| 429 | `Europe/Astrakhan` | identical |  |
| 430 | `Europe/Athens` | identical |  |
| 431 | `Europe/Belfast` | alias | alias for Europe/London |
| 432 | `Europe/Belgrade` | identical |  |
| 433 | `Europe/Berlin` | identical |  |
| 434 | `Europe/Bratislava` | alias | alias for Europe/Prague |
| 435 | `Europe/Brussels` | identical |  |
| 436 | `Europe/Bucharest` | identical |  |
| 437 | `Europe/Budapest` | identical |  |
| 438 | `Europe/Busingen` | alias | alias for Europe/Zurich |
| 439 | `Europe/Chisinau` | differs |  |
| 440 | `Europe/Copenhagen` | alias | alias for Europe/Berlin |
| 441 | `Europe/Dublin` | identical |  |
| 442 | `Europe/Gibraltar` | identical |  |
| 443 | `Europe/Guernsey` | alias | alias for Europe/London |
| 444 | `Europe/Helsinki` | identical |  |
| 445 | `Europe/Isle_of_Man` | alias | alias for Europe/London |
| 446 | `Europe/Istanbul` | identical |  |
| 447 | `Europe/Jersey` | alias | alias for Europe/London |
| 448 | `Europe/Kaliningrad` | identical |  |
| 449 | `Europe/Kiev` | alias | alias for Europe/Kyiv |
| 450 | `Europe/Kirov` | identical |  |
| 451 | `Europe/Kyiv` | identical |  |
| 452 | `Europe/Lisbon` | differs |  |
| 453 | `Europe/Ljubljana` | alias | alias for Europe/Belgrade |
| 454 | `Europe/London` | identical |  |
| 455 | `Europe/Luxembourg` | alias | alias for Europe/Brussels |
| 456 | `Europe/Madrid` | identical |  |
| 457 | `Europe/Malta` | identical |  |
| 458 | `Europe/Mariehamn` | alias | alias for Europe/Helsinki |
| 459 | `Europe/Minsk` | identical |  |
| 460 | `Europe/Monaco` | alias | alias for Europe/Paris |
| 461 | `Europe/Moscow` | identical |  |
| 462 | `Europe/Nicosia` | alias | alias for Asia/Nicosia |
| 463 | `Europe/Oslo` | alias | alias for Europe/Berlin |
| 464 | `Europe/Paris` | identical |  |
| 465 | `Europe/Podgorica` | alias | alias for Europe/Belgrade |
| 466 | `Europe/Prague` | identical |  |
| 467 | `Europe/Riga` | identical |  |
| 468 | `Europe/Rome` | identical |  |
| 469 | `Europe/Samara` | identical |  |
| 470 | `Europe/San_Marino` | alias | alias for Europe/Rome |
| 471 | `Europe/Sarajevo` | alias | alias for Europe/Belgrade |
| 472 | `Europe/Saratov` | identical |  |
| 473 | `Europe/Simferopol` | identical |  |
| 474 | `Europe/Skopje` | alias | alias for Europe/Belgrade |
| 475 | `Europe/Sofia` | identical |  |
| 476 | `Europe/Stockholm` | alias | alias for Europe/Berlin |
| 477 | `Europe/Tallinn` | identical |  |
| 478 | `Europe/Tirane` | identical |  |
| 479 | `Europe/Tiraspol` | alias | alias for Europe/Chisinau |
| 480 | `Europe/Ulyanovsk` | identical |  |
| 481 | `Europe/Uzhgorod` | alias | alias for Europe/Kyiv |
| 482 | `Europe/Vaduz` | alias | alias for Europe/Zurich |
| 483 | `Europe/Vatican` | alias | alias for Europe/Rome |
| 484 | `Europe/Vienna` | identical |  |
| 485 | `Europe/Vilnius` | identical |  |
| 486 | `Europe/Volgograd` | identical |  |
| 487 | `Europe/Warsaw` | identical |  |
| 488 | `Europe/Zagreb` | alias | alias for Europe/Belgrade |
| 489 | `Europe/Zaporozhye` | alias | alias for Europe/Kyiv |
| 490 | `Europe/Zurich` | identical |  |
| 491 | `Factory` | identical |  |
| 492 | `GB` | alias | alias for Europe/London |
| 493 | `GB-Eire` | alias | alias for Europe/London |
| 494 | `GMT` | alias | alias for Etc/GMT |
| 495 | `GMT+0` | alias | alias for Etc/GMT |
| 496 | `GMT-0` | alias | alias for Etc/GMT |
| 497 | `GMT0` | alias | alias for Etc/GMT |
| 498 | `Greenwich` | alias | alias for Etc/GMT |
| 499 | `HST` | differs before epoch only (not shipped) | data in the installed gem, linked in the vendored release |
| 500 | `Hongkong` | alias | alias for Asia/Hong_Kong |
| 501 | `Iceland` | alias | alias for Africa/Abidjan |
| 502 | `Indian/Antananarivo` | alias | alias for Africa/Nairobi |
| 503 | `Indian/Chagos` | identical |  |
| 504 | `Indian/Christmas` | alias | alias for Asia/Bangkok |
| 505 | `Indian/Cocos` | alias | alias for Asia/Yangon |
| 506 | `Indian/Comoro` | alias | alias for Africa/Nairobi |
| 507 | `Indian/Kerguelen` | alias | alias for Indian/Maldives |
| 508 | `Indian/Mahe` | alias | alias for Asia/Dubai |
| 509 | `Indian/Maldives` | identical |  |
| 510 | `Indian/Mauritius` | identical |  |
| 511 | `Indian/Mayotte` | alias | alias for Africa/Nairobi |
| 512 | `Indian/Reunion` | alias | alias for Asia/Dubai |
| 513 | `Iran` | alias | alias for Asia/Tehran |
| 514 | `Israel` | alias | alias for Asia/Jerusalem |
| 515 | `Jamaica` | alias | alias for America/Jamaica |
| 516 | `Japan` | alias | alias for Asia/Tokyo |
| 517 | `Kwajalein` | alias | alias for Pacific/Kwajalein |
| 518 | `Libya` | alias | alias for Africa/Tripoli |
| 519 | `MET` | kind flip, excluded by policy | Upstream converted this from a standalone rule-only zone into an alias of a representative city. That is a change of meaning rather than a correction: the identifier stops naming a set of rules and starts naming a place, so it inherits that place-specific history, and stored records that the rule-only definition placed correctly can move by an hour. See the per-zone table in the diff report for what this one would have become and where its offset would have changed. Reviewed and held pending a separate decision, not because the data is wrong. |
| 520 | `MST` | differs before epoch only (not shipped) | data in the installed gem, linked in the vendored release |
| 521 | `MST7MDT` | differs before epoch only (not shipped) | data in the installed gem, linked in the vendored release |
| 522 | `Mexico/BajaNorte` | alias | alias for America/Tijuana |
| 523 | `Mexico/BajaSur` | alias | alias for America/Mazatlan |
| 524 | `Mexico/General` | alias | alias for America/Mexico_City |
| 525 | `NZ` | alias | alias for Pacific/Auckland |
| 526 | `NZ-CHAT` | alias | alias for Pacific/Chatham |
| 527 | `Navajo` | alias | alias for America/Denver |
| 528 | `PRC` | alias | alias for Asia/Shanghai |
| 529 | `PST8PDT` | differs before epoch only (not shipped) | data in the installed gem, linked in the vendored release |
| 530 | `Pacific/Apia` | identical |  |
| 531 | `Pacific/Auckland` | identical |  |
| 532 | `Pacific/Bougainville` | identical |  |
| 533 | `Pacific/Chatham` | identical |  |
| 534 | `Pacific/Chuuk` | alias | alias for Pacific/Port_Moresby |
| 535 | `Pacific/Easter` | identical |  |
| 536 | `Pacific/Efate` | identical |  |
| 537 | `Pacific/Enderbury` | alias | alias for Pacific/Kanton |
| 538 | `Pacific/Fakaofo` | identical |  |
| 539 | `Pacific/Fiji` | identical |  |
| 540 | `Pacific/Funafuti` | alias | alias for Pacific/Tarawa |
| 541 | `Pacific/Galapagos` | identical |  |
| 542 | `Pacific/Gambier` | identical |  |
| 543 | `Pacific/Guadalcanal` | identical |  |
| 544 | `Pacific/Guam` | identical |  |
| 545 | `Pacific/Honolulu` | identical |  |
| 546 | `Pacific/Johnston` | alias | alias for Pacific/Honolulu |
| 547 | `Pacific/Kanton` | identical |  |
| 548 | `Pacific/Kiritimati` | identical |  |
| 549 | `Pacific/Kosrae` | identical |  |
| 550 | `Pacific/Kwajalein` | identical |  |
| 551 | `Pacific/Majuro` | alias | alias for Pacific/Tarawa |
| 552 | `Pacific/Marquesas` | identical |  |
| 553 | `Pacific/Midway` | alias | alias for Pacific/Pago_Pago |
| 554 | `Pacific/Nauru` | identical |  |
| 555 | `Pacific/Niue` | identical |  |
| 556 | `Pacific/Norfolk` | identical |  |
| 557 | `Pacific/Noumea` | identical |  |
| 558 | `Pacific/Pago_Pago` | identical |  |
| 559 | `Pacific/Palau` | identical |  |
| 560 | `Pacific/Pitcairn` | identical |  |
| 561 | `Pacific/Pohnpei` | alias | alias for Pacific/Guadalcanal |
| 562 | `Pacific/Ponape` | alias | alias for Pacific/Guadalcanal |
| 563 | `Pacific/Port_Moresby` | identical |  |
| 564 | `Pacific/Rarotonga` | identical |  |
| 565 | `Pacific/Saipan` | alias | alias for Pacific/Guam |
| 566 | `Pacific/Samoa` | alias | alias for Pacific/Pago_Pago |
| 567 | `Pacific/Tahiti` | identical |  |
| 568 | `Pacific/Tarawa` | identical |  |
| 569 | `Pacific/Tongatapu` | identical |  |
| 570 | `Pacific/Truk` | alias | alias for Pacific/Port_Moresby |
| 571 | `Pacific/Wake` | alias | alias for Pacific/Tarawa |
| 572 | `Pacific/Wallis` | alias | alias for Pacific/Tarawa |
| 573 | `Pacific/Yap` | alias | alias for Pacific/Port_Moresby |
| 574 | `Poland` | alias | alias for Europe/Warsaw |
| 575 | `Portugal` | alias | alias for Europe/Lisbon |
| 576 | `ROC` | alias | alias for Asia/Taipei |
| 577 | `ROK` | alias | alias for Asia/Seoul |
| 578 | `Singapore` | alias | alias for Asia/Singapore |
| 579 | `Turkey` | alias | alias for Europe/Istanbul |
| 580 | `UCT` | alias | alias for Etc/UTC |
| 581 | `US/Alaska` | alias | alias for America/Anchorage |
| 582 | `US/Aleutian` | alias | alias for America/Adak |
| 583 | `US/Arizona` | alias | alias for America/Phoenix |
| 584 | `US/Central` | alias | alias for America/Chicago |
| 585 | `US/East-Indiana` | alias | alias for America/Indiana/Indianapolis |
| 586 | `US/Eastern` | alias | alias for America/New_York |
| 587 | `US/Hawaii` | alias | alias for Pacific/Honolulu |
| 588 | `US/Indiana-Starke` | alias | alias for America/Indiana/Knox |
| 589 | `US/Michigan` | alias | alias for America/Detroit |
| 590 | `US/Mountain` | alias | alias for America/Denver |
| 591 | `US/Pacific` | alias | alias for America/Los_Angeles |
| 592 | `US/Samoa` | alias | alias for Pacific/Pago_Pago |
| 593 | `UTC` | alias | alias for Etc/UTC |
| 594 | `Universal` | alias | alias for Etc/UTC |
| 595 | `W-SU` | alias | alias for Europe/Moscow |
| 596 | `WET` | kind flip, excluded by policy | Upstream converted this from a standalone rule-only zone into an alias of a representative city. That is a change of meaning rather than a correction: the identifier stops naming a set of rules and starts naming a place, so it inherits that place-specific history, and stored records that the rule-only definition placed correctly can move by an hour. See the per-zone table in the diff report for what this one would have become and where its offset would have changed. Reviewed and held pending a separate decision, not because the data is wrong. |
| 597 | `Zulu` | alias | alias for Etc/UTC |
