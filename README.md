# Field Quiz

An eBird-style photo quiz for any group of organisms, anywhere in the world, built on iNaturalist.

Pick a group (butterflies, snakes, hawks, oaks) and a place (Lima, Queensland, Connecticut). The quiz finds the species most often recorded there, pulls research-grade photos from real iNaturalist observations, and asks you to name them. It also builds a field guide page for the same species.

**Play it:** [Butterflies of Lima](https://jtrouern.github.io/field-quiz/?preset=lima-butterflies) · [Queensland Snake ID](https://jtrouern.github.io/field-quiz/?preset=queensland-snakes) · [Dangerous Animals of Queensland](https://jtrouern.github.io/field-quiz/?preset=queensland-dangers) · [Make your own](https://jtrouern.github.io/field-quiz/)

It is one HTML file with no build step and no server. Everything runs in the visitor's browser and talks directly to the public iNaturalist API.

## What's in this folder

| File | What it is |
|---|---|
| `index.html` | The whole app. Open it in a browser. |
| `configs/lima-butterflies.json` | Example of an automatic quiz: butterflies of Lima Province, Peru, grouped by family. |
| `configs/queensland-snakes.json` | Example of a hand-built quiz: 93 Queensland snakes and legless lizards, with danger levels, field notes and regions. |
| `queensland-dangers.json` | Example of a three-answer quiz: 75 Queensland animals other than snakes, rated lethal, dangerous or harmless, with look-alikes as wrong answers. |
| `README.md` | This file. |
| `LICENSE` | MIT licence. |

## Try it

Download the folder and double-click `index.html`. Pick one of the examples, or use **Make your own**:

1. Type what you want to learn (a common or scientific name) and pick it from the list.
2. Type a place and pick it from the list. Leave it empty for worldwide.
3. Choose how many species, how to group them (family, genus, order…), and the language for common names.
4. Press **Build quiz**.

## Three ways to set up a quiz

### 1. A link

Once the page is online, everything can go in the address:

```
index.html?taxon=47224&place=97128&n=60&group=family&lang=es
```

| Parameter | Meaning | Example |
|---|---|---|
| `taxon` | iNaturalist taxon ID or name | `47224` (butterflies) |
| `place` | iNaturalist place ID or name | `97128` (Lima Province) |
| `n` | Number of species, most-observed first | `60` |
| `group` | Rank to group answers by: `family`, `subfamily`, `order`, `genus`, `tribe`, or `none` | `family` |
| `lang` | Language for common names | `en`, `es`, `pt`, `fr` |
| `license` | `cc` to show only Creative Commons photos | `cc` |
| `title` | Page title | `Butterflies of Lima` |
| `preset` | Load a built-in example | `queensland-snakes` |
| `config` | URL of a config file on the same site | `configs/lima-butterflies.json` |

To find an ID, open the taxon or place on inaturalist.org and copy the number from the address bar, for example `inaturalist.org/taxa/47224-Papilionoidea`.

### 2. A config file

For anything beyond the basics, write a JSON config. The **Make your own** tab shows the config for whatever quiz you built, so you can copy it and edit it.

Then either:

- put it next to `index.html` and link to `index.html?config=my-quiz.json`, or
- paste it into the `<script type="application/json" id="quiz-config">` block near the bottom of `index.html`. That copy of the page will always open straight into your quiz. This also works when you open the file from your computer.

### 3. A hand-picked species list

Give a `species` list instead of (or as well as) a taxon. You can add your own groups, notes and filters. This is how the Queensland quiz works: each snake belongs to a danger level, and the first question is "Dangerous or harmless?"

## Config reference

```json
{
  "title": "Butterflies of Lima",
  "subtitle": "Lima Province, Peru",
  "intro": "Shown above the quiz settings.",
  "fieldRule": "Optional note shown below the settings.",

  "taxon": { "id": 47224, "name": "Papilionoidea" },
  "place": { "id": 97128, "name": "Lima Province, Peru" },
  "fallbackPlace": { "id": 6744, "name": "Australia" },
  "speciesCount": 60,
  "groupBy": "family",
  "locale": "en",
  "photoLicense": "any",
  "quality": "research",
  "minPhotos": 6
}
```

| Key | What it does | Default |
|---|---|---|
| `title`, `subtitle`, `intro`, `fieldRule` | Text on the page | Built from the taxon and place |
| `taxon` | ID, name, or `{id, name}`. Used when there's no `species` list. | — |
| `place` | ID, name, or `{id, name}`. A name like `"Lima, PE"` keeps only matches that contain "PE". | Worldwide |
| `fallbackPlace` | Where to look for extra photos when a species has fewer than `minPhotos` in `place` | Worldwide |
| `speciesCount` | How many of the most-observed species to use (up to 500) | 50 |
| `groupBy` | `family`, `subfamily`, `order`, `genus`, `tribe`, `none`, or `custom` (groups come from your species list) | `family` |
| `groupNoun` | What a group is called in questions, such as "danger level" | The rank name |
| `groupQuestion` | The first question in two-step mode | "Which family?" |
| `locale` | Language for common names | `en` |
| `photoLicense` | `any`, or `cc` for Creative Commons photos only | `any` |
| `quality` | `research` or `needs_id,research` | `research` |

### Hand-picked species

```json
"species": [
  { "name": "Pseudonaja textilis", "common": "Eastern Brown Snake",
    "group": "Dangerously venomous", "note": "Slender, plain brown…",
    "tags": ["SE", "W"], "look": "brown" },
  "Tropidonophis mairii"
]
```

Each entry can be just a scientific name, or an object with:

| Key | What it does |
|---|---|
| `name` | Scientific name, as iNaturalist spells it |
| `taxonId` | Optional iNaturalist ID. Skips the name lookup. |
| `common` | Common name to show |
| `group` | Which group it belongs to |
| `note` | Field marks, shown after each answer and in the field guide |
| `tags` | Filter values (see `tags` below) |
| `look` | Look-alike set. Species sharing a `look` value are used as each other's wrong answers. |

### Groups, answers and filters

```json
"groupBy": "custom",
"groupNoun": "danger level",
"groupQuestion": "Dangerous or harmless?",
"groups": [
  { "name": "Dangerously venomous", "tone": "danger", "answer": "Dangerous" },
  { "name": "Sea snake", "tone": "sea", "answer": "Dangerous" },
  { "name": "Harmless snake", "tone": "safe", "answer": "Harmless" }
],
"answers": { "Dangerous": "Could kill a person" },
"tags": { "label": "Where", "values": { "SE": "South-east", "W": "Inland" } }
```

- `groups` sets the order and colour of groups. `tone` can be `danger`, `warn`, `mild`, `sea` or `safe`. Leave it out for automatic colours.
- `answer` lets several groups share one answer in the first question. The Queensland quiz has seven groups but only three answers: Dangerous, Venomous (lower risk), Harmless.
- `answers` adds a short hint under each answer button.
- `tags` adds a row of filter chips, such as regions or habitats.

## Putting it online

### GitHub Pages (free; best if you want to share the code)

1. Create a GitHub repository and upload this folder.
2. In the repository's **Settings → Pages**, publish from the main branch.
3. Your quiz is at `https://<username>.github.io/<repo>/`. Add `?preset=…`, `?config=…` or the link parameters above.

### In a Ghost post (or any blog)

Add an **HTML card** to the post and paste the embed code from the **Make your own** tab:

```html
<iframe id="field-quiz" src="https://<username>.github.io/<repo>/?preset=lima-butterflies"
  style="width:100%;height:900px;border:0" loading="lazy" title="Butterflies of Lima"></iframe>
<script>
  // Optional: let the quiz set its own height
  addEventListener("message", e => {
    if (e.data && e.data.type === "field-quiz-height")
      document.getElementById("field-quiz").style.height = e.data.height + "px";
  });
</script>
```

Ghost can't host a standalone HTML file, so host `index.html` somewhere static (GitHub Pages, Netlify, Cloudflare Pages) and embed it.

## Photos, credit and fair use of iNaturalist

- Photos are loaded straight from iNaturalist and are never copied. Each one is shown with the observer's credit and a link to the observation.
- Many photos are "all rights reserved". For a public site, consider `"photoLicense": "cc"` to use only Creative Commons photos.
- The app asks iNaturalist for about one thing per second and caches results in the visitor's browser for a week, following iNaturalist's API guidelines. A quiz of 50 species with family groups takes about 5 requests to build.
- Common names come from iNaturalist and can vary by language and region.

## Limits

- **Few photos.** Species with few observations in the chosen place borrow photos from `fallbackPlace` (or worldwide). Those photos are labelled "from outside the quiz area".
- **Names that don't match.** In a hand-picked list, a name iNaturalist doesn't recognise shows "No iNaturalist photos yet" in the field guide. Check the spelling on inaturalist.org, or add its `taxonId`.
- **Captive and introduced animals.** The automatic species list only uses research-grade observations, which excludes captive animals. Introduced species that live wild will appear.

## Credits and licence

Data and photos: [iNaturalist](https://www.inaturalist.org) and its observers.
Inspired by the [eBird Media Quiz](https://ebird.org/quiz).
Code: MIT licence. Use it, change it, share it.
