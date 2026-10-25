# Template and rendering rules

Status: requirements for the first implementation. No renderer or templates have been built yet.

## 1. Content and composition

AI supplies structured card content and a separate illustration. Card content includes:

- Name.
- Mana cost, including an explicit distinction between no cost and `{0}`.
- Supertypes, card types, and subtypes, displayed as a type line.
- Rules text, with optional reminder text and inline mana or action symbols.
- Optional flavor text: a quote, joke, or story passage with no gameplay effect.
- Power and toughness for creatures, or other values required by a supported special layout.
- Card colors, any color indicator, rarity, and required layout information.

Set configuration supplies the set symbol, set code, language, numbering policy, credits, shared visual style, and card back. The illustration is its own asset; it must not contain the card title, rules, frame, or stats as substitutes for rendered fields.

The program must insert content exactly. It may wrap text and apply the defined fitting policy; it must not abbreviate, paraphrase, omit, or invent content.

## 2. Shared geometry

Each layout family has one authoritative geometry definition, using a fixed coordinate system. All its variants must share:

- Canvas size, aspect ratio, outer border, trim, and safe-area geometry.
- Title and mana-cost regions, including their separation.
- Illustration window and clipping mask.
- Type-line region and reserved positions for the set symbol and optional color indicator.
- Rules and flavor-text regions and their separation policy.
- Stats-box position and footprint, when applicable.
- Footer and collector-information positions.
- Fonts, base sizes, line spacing, alignment, padding, and text-fitting policy.

Color and rarity changes must not alter these values. Legendary decoration must fit within its reserved decorative area and must not displace content. Decorative textures must not reduce text contrast or intrude into content regions.

The standard creature and noncreature layouts must share their main field geometry. Removing the creature stats box must not shift the other fields. Reserve its footprint consistently.

Special layouts may have different geometry when their mechanics require it. Declare each as a separate layout family; all color and rarity variants within that family must still align exactly. Both faces of a two-faced card require explicitly defined layouts and previews.

## 3. Frame colors and card categories

Choose the set's deck colors before planning its template variants. Then derive the actual requirements from individual cards, lands, artifacts, tokens, and special layouts.

- Single-color cards use the corresponding frame treatment: white, blue, black, red, or green.
- Multicolor cards use the set's documented multicolor treatment. Record the supported color combinations explicitly.
- Hybrid costs may require a distinct hybrid treatment. Do not silently treat a hybrid frame as an ordinary multicolor frame.
- Provide explicit treatments for lands, colorless nonland cards, and artifacts where needed. Colored artifacts must preserve their relevant color information.
- A land's mana production may inform its decorative accents; producing colored mana does not itself make the land colored.
- Card color generally follows its mana cost, but color indicators and color-defining rules can create exceptions. Store or resolve these explicitly; never guess from the illustration or deck name.
- Color identity is a separate concept from card color. Do not use it as an automatic frame-color replacement.

Example: a red-green deck may require red, green, red-green, colorless artifact, and land treatments. Do not put every card into a red-green frame.

## 4. Rarity and set symbol

Use one set-symbol design with these standard rarity treatments:

| Rarity | Set-symbol treatment |
| --- | --- |
| Common | Black or white, with suitable contrast |
| Uncommon | Silver |
| Rare | Gold |
| Mythic rare | Orange-red |

Rarity does not change the entire frame color, geometry, font, or illustration size. Gold framing for a multicolor card is independent of a gold set symbol for a rare card. Basic lands need an explicit configured symbol/rarity treatment.

Official modern rares and mythics commonly have a holofoil stamp. This custom renderer does not require an authenticity stamp. If the set uses a decorative badge instead, define it as an optional fixed-position asset and preview it; do not imply official authentication.

## 5. Legendary status and type-specific fields

- Legendary is independent of rarity. Do not infer legendary status from a gold symbol, rarity, a person's name, or visual importance.
- A legendary variant may add the approved crown-like title decoration. It must retain the same title baseline, mana position, and other field geometry as the ordinary variant.
- Creatures require power/toughness. Noncreature cards must not receive a creature stats box merely to fill space.
- Lands normally have no mana cost. An absent cost is not a zero cost.
- A color indicator is separate from the set symbol and belongs in its own reserved location when needed.
- Planeswalkers require loyalty and ability-cost layout; battles require defense; Sagas, Classes, Adventures, split cards, and other special formats require explicitly supported layouts.
- Tokens require the fields appropriate to their actual characteristics; do not invent mana costs or stats for tokens that do not have them.
- Unusual formats, including Vanguard hand/life modifiers, are supported only through an explicit layout definition.

## 6. Required template previews

Before production use, the AI must generate and show a browsable preview gallery covering every template variant that the set can need within its declared scope.

The gallery must include:

1. Every required layout family.
2. Every required color/frame treatment within each applicable family.
3. Ordinary and legendary versions wherever applicable.
4. Every rarity treatment used by the set, including its set-symbol appearance.
5. Creature and noncreature examples, lands, artifacts, and tokens where required.
6. Every supported special layout and every face of a two-faced layout.
7. Optional decorative badges or other enabled frame options.

Maintain an explicit coverage matrix listing valid combinations and linking to a preview for each. Reuse a single preview for duplicate configurations, but do not omit required combinations merely because their pieces were shown separately. Exclude invalid or unused combinations explicitly with a reason. The goal is complete coverage for the set, not generating every Magic layout ever printed.

If no card list exists yet, declare the planned colors, card categories, layouts, and options first. Mark this as provisional coverage. Recompute the matrix when card requirements change; a card requiring an unpreviewed variant must not silently enter production.

Show two preview modes:

- **Field-map preview:** labeled placeholder regions, bounding boxes, safe areas, and anchors. Labels are preview overlays, never baked into reusable frame assets.
- **Rendered sample preview:** realistic sample artwork and actual text, mana symbols, type, flavor text, and applicable stats.

Use identical sample content, illustration crop, and typography across comparable color variants. Use the same sample when comparing rarities. Change only the dimension being demonstrated, so differences are easy to inspect.

Include a side-by-side comparison and an alignment overlay or equivalent geometry check. Provide long-title, long-type-line, dense-rules, multiline-flavor, many-mana-symbol, missing-optional-field, and nonnumeric-stats examples such as `*/*`.

Do not declare a family ready until all required previews exist, alignment and fitting checks pass, and the user has been shown the gallery. Record acceptance separately from generation and technical validation. Never infer acceptance from silence.

## 7. Deterministic rendering and fitting

- Reuse exact frame assets, masks, symbols, and geometry. Independently generating near-matching full-card templates is not sufficient.
- Define layer order, font files, symbol assets, text wrapping, and artwork crop behavior explicitly.
- Keep title and mana regions separate; one must never overwrite the other.
- Define any font-size reduction with fixed steps and a minimum readable size. Apply the same policy across variants.
- Text may flow within its declared region. If rules and flavor share a region, their separator and spacing must follow a documented policy consistent across variants.
- Do not move field boundaries or enlarge the illustration window to solve an individual card's fitting problem.
- If content does not fit, return an actionable error identifying the card and field. Never silently clip, truncate, hide text, or shrink below the allowed minimum.
- Artwork placement must use a documented crop or fit policy. Store any per-image crop/focal-point choice so rerenders reproduce it.
- The same content, assets, template version, and rendering settings must produce the same visible result.

## 8. Versioning and completion checks

Version template geometry, decorative assets, fonts, and symbol assets. Record those versions with rendered exports. Preserve previous versions rather than silently changing existing output.

Any visual template change requires refreshed affected previews and a new version. Recheck the coverage matrix when colors, card types, layouts, rarity options, or legendary treatments change.

Before marking a template release ready, verify:

- Required preview coverage is complete and visible to the user.
- Comparable variants have identical geometry.
- Symbols, frame colors, rarity treatments, and conditional fields match the supplied data.
- Sample and stress-case text is legible and fits without collisions.
- Export dimensions and safe areas match the intended print configuration.
- Preview/validation status, user acceptance, and individual card-art approval remain separate records.

## References

- [Wizards: Comprehensive Rules, section 2, Parts of a Card](https://media.wizards.com/2026/downloads/MagicCompRules%2020260619.pdf)
- [Wizards: Legendary frame and rules/flavor separator](https://magic.wizards.com/en/news/announcements/dominaria-frame-template-and-rules-changes-2018-03-21)
- [Wizards: Magic 2015 frame and holofoil stamp](https://magic.wizards.com/en/news/making-magic/directors-chair-2013-2014-01-06)

These sources describe official card conventions. Preview coverage, fixed geometry, deterministic composition, and versioning are project requirements established for MTGGenerator.
