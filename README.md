# FitFindr — Starter Kit

This starter kit contains everything you need to begin Project 2.

## What's Included

```
ai201-project2-fitfindr-starter/
├── data/
│   ├── listings.json          # 40 mock secondhand listings
│   └── wardrobe_schema.json   # Wardrobe format + example wardrobe
├── utils/
│   └── data_loader.py         # Helper functions for loading the data
├── planning.md                # Your planning template — fill this out first
└── requirements.txt           # Python dependencies
```

## Setup

```bash
pip install -r requirements.txt
```

Set your Groq API key in a `.env` file (get a free key at [console.groq.com](https://console.groq.com)):
```
GROQ_API_KEY=your_key_here
```

## The Mock Listings Dataset

`data/listings.json` contains 40 mock secondhand listings across categories (tops, bottoms, outerwear, shoes, accessories) and styles (vintage, y2k, grunge, cottagecore, streetwear, and more).

Each listing has: `id`, `title`, `description`, `category`, `style_tags`, `size`, `condition`, `price`, `colors`, `brand`, and `platform`.

Load it with:
```python
from utils.data_loader import load_listings
listings = load_listings()
```

## The Wardrobe Schema

`data/wardrobe_schema.json` defines the format your agent uses to represent a user's existing wardrobe. It includes:

- `schema`: field definitions for a wardrobe item
- `example_wardrobe`: a sample wardrobe with 10 items you can use for testing
- `empty_wardrobe`: a starting template for a new user

Load an example wardrobe with:
```python
from utils.data_loader import get_example_wardrobe
wardrobe = get_example_wardrobe()
```

## Where to Start

1. **Read `planning.md` and fill it out before writing any code.**
2. Verify the data loads correctly by running `python utils/data_loader.py`.
3. Build and test each tool individually before connecting them through your planning loop.

Your implementation files go in this same directory. There's no required file structure for your agent code — organize it however makes sense for your design.

---

## AI Usage

**Describe at least 2 specific instances where you used an AI tool during implementation.**

**Instance 1 — Validating the planning loop logic**

Input given to Claude: The full Planning Loop section of this document (the ordered list of tool calls and error branches).

What it produced: Claude identified that the empty wardrobe check was placed out of order — it appeared after the `suggest_outfit` error handling, but in execution order it happens before `suggest_outfit` is called. Claude suggested restructuring the description to match execution order exactly.

What was changed: The planning loop section was reordered so the empty wardrobe branch appears immediately after the `search_listings` success branch, before `suggest_outfit` is introduced. The underlying logic was not changed, only the ordering of the description.

**Instance 2 — Catching the listing mutation bug**

Input given to Claude: The `search_listings()` implementation that used `listing["score"] = ...` to attach scores directly to listing dicts.

What it produced: Claude flagged that mutating the original dicts from `load_listings()` would cause stale scores on subsequent calls if the data were ever cached, and suggested computing scores in a separate `(listing, score)` tuple list instead.

What was changed: Replaced the in-place mutation with a local `scored` list of tuples. The original listing dicts are never modified. The sort and top-3 cap logic was kept the same.