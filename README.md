````md

This was vibe coded with ChatGPT.
It could probably be better but I don't really want to dedicate a lot of time to this.
It works as of today. If it breaks, I may or may not fix it.
Feel free to PR.

# Roll20 Roll Tables: Import + Pretty Output (PowerCards)

These two small scripts solve a common Roll20 problem:

1. **Get a big table into Roll20 without clicking 100 times** (import from a Handout)
2. **Make the roll output readable and nice** (PowerCards with rarity emoji)

If you can copy and paste, you can use these.

---

## What you need first (prereqs)

* A Roll20 game with **API/Mods enabled** (this is required to run scripts).
* **PowerCards installed** (only required for the “Pretty Output” script).
* You must be **GM** in the game.

---

## Overview: how the workflow works

1. Put your table data in a **Handout** (as a table).
2. Run `!mdtbl` once to create the **Rollable Table**.
3. Roll it either:

   * plain: `[[1t[YourTable]]]`, or
   * pretty: `!rtcard --table|YourTable`

After the table is in your “Library” game, use **Transmogrifier** to copy it into your other games.

---

# Part 1: Import a Handout table into a Rollable Table (`!mdtbl`)

## Step 1: Create the Handout

1. In Roll20, go to **Journal**.
2. Click **+ Add** → **Handout**.
3. Name it something obvious, like: **Ammunition**.
4. In the Handout, paste your table into **Notes**.

### Important note about table format

This importer reads a real table (what Roll20 stores as HTML).
If you paste a Markdown-style table (pipes) Roll20 usually converts it into an actual table automatically, which is exactly what we want.

Your table should have 3 columns:

* **Name** (or “Ammunition”)
* **Weight**
* **Description**

---

## Step 2: Run the import command

If your table is in **Notes**:

```text
!mdtbl --handout|Ammunition --table|Ammunition --mode|replace --showplayers|false --field|notes
```

If your table is in **GM Notes** instead:

```text
!mdtbl --handout|Ammunition --table|Ammunition --mode|replace --showplayers|false --field|gmnotes
```

### What this command does (in plain English)

* Finds the handout named `Ammunition`
* Reads the table inside it
* Creates (or updates) a Roll20 Rollable Table named `Ammunition`
* Adds all the rows as weighted results

---

## Step 3: Test it (plain Roll20 roll)

```text
[[1t[Ammunition]]]
```

### If the output looks “ugly” (example: `**bold**`)

That is normal. Roll20 rollable table results do **not** render Markdown formatting.
If you want “nice output”, use Part 2 (PowerCards).

---

# Part 2: Roll the table with PowerCards formatting (`!rtcard`)

This script:

* Rolls from your Rollable Table
* Prints a clean PowerCard
* Adds an emoji based on rarity keywords (Common, Rare, etc.)
* Strips Markdown markers like `**` so they do not clutter the result

## Roll publicly to chat

```text
!rtcard --table|Ammunition
```

## Whisper the result to GM only

```text
!rtcard --table|Ammunition --whisper|gm
```

### Tip

If you want players to click a button, create a macro that runs:

```text
!rtcard --table|Ammunition
```

---

# Weights explained (this matters a lot)

## What “Weight” means

Weight controls **how likely** an entry is to be picked.

Example:

* Weight 10 = very common
* Weight 1 = very rare

If a table has:

* “Common Arrow” weight **10**
* “Legendary Arrow” weight **1**

Then “Common Arrow” is about 10 times as likely to show up as “Legendary Arrow”.

## How the Ammunition example was standardized

The Ammunition table uses a simple rarity-to-weight approach so results feel right:

* **Common**: weight **10**
* **Uncommon**: weight **7**
* **Rare**: weight **4**
* **Very Rare**: weight **2**
* **Legendary**: weight **1**

This makes rare items show up rarely without needing complicated math.

---

# Rarity detection (emoji support)

The PowerCards script looks for these keywords anywhere in the result text (name or description).
If it finds one, it adds the corresponding emoji.

| Rarity keyword | Emoji |
| -------------- | ----- |
| Common         | ⚪     |
| Uncommon       | 🟢    |
| Rare           | 🔵    |
| Very Rare      | 🟣    |
| Legendary      | 🟠    |

## How to make sure your table triggers emoji correctly

Include the rarity word exactly like this in the entry text:

* `Common`
* `Uncommon`
* `Rare`
* `Very Rare`
* `Legendary`

Example formats that work:

* `Bull Arrow (Uncommon): ...`
* `Bull Arrow - Uncommon. ...`
* `**Uncommon.** ...` (the script strips the `**`, but still finds the word)

---

# Troubleshooting (quick fixes)

## “Nothing imports” or “No tables found”

* Confirm you pasted your table into the handout **Notes** (or use `--field|gmnotes` if you used GM Notes).
* Confirm the handout actually contains a table (not just plain text). If needed, paste the table again.

## The importer works but the roll output shows asterisks (`**`)

That is expected for plain `[[1t[Table]]]` rolls. Use:

```text
!rtcard --table|YourTable
```

## I want to update the table later

* Edit the Handout table.
* Run the same import command again with `--mode|replace` (it will rebuild the table items).

---

# Using this across multiple Roll20 games

Best practice:

1. Maintain your tables in a single **Library game**.
2. Import/update there.
3. Use **Transmogrifier** to copy the Rollable Tables into your other games.
```
