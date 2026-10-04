# 👤📍NPC Spawn System

A universal AI Dungeon script designed to make sandbox scenarios and recurring locations feel more alive by allowing configured NPCs to naturally appear when the player enters specific locations.

# ✨ What is this?

NPC Spawn System is a tool for AI Dungeon creators who want their scenarios to feel less empty and more dynamic.

In sandbox scenarios, slice-of-life scenarios, RPGs, and other stories where the player regularly moves between recurring locations, the AI can sometimes generate completely new unnamed NPCs every time the player enters a place — or simply describe the location as empty.

This system provides the AI with additional context about which established NPCs can appear in a specific location.

Instead of relying entirely on the AI to decide who might be present, the system can:

- Detect when the player enters a configured location.
- Distinguish between a first visit and a return visit.
- Randomly determine whether an NPC should appear.
- Select one or more configured NPCs using weighted probabilities.
- Provide the selected character's Story Card information to the AI.
- Track which NPCs actually appeared.
- Remember their recent location to reduce implausible immediate movement between locations.
- Allow characters to appear naturally without forcing a predetermined scene.

The goal is simple:

«Make recurring locations feel inhabited without turning NPC spawning into a rigid scripted event system.»

---

# 💡 How it works

The system works through AI Dungeon's four scripting hooks:

Library → Input → Context → Output

### 📍 1. Input — Location Detection

The Input hook examines the player's action and recent story context to determine whether they have entered or moved to one of the configured locations.

The system supports:

- Location names
- Alternative names
- Common ways of referring to a location
- Basic movement language

For example, a location can be configured as:

The Old Mill

with aliases such as:

the mill
Old Mill
the old mill

This allows the system to recognize different ways the player may refer to the same place.

---

### 🎲 2. Context — NPC Selection

When the player enters a configured location, the system determines whether an NPC should appear.

Each location has two separate spawn chances:

- First Entry Chance — chance of spawning NPCs the first time the player visits.
- Revisit Chance — chance of spawning NPCs when returning later.

The system then selects NPCs using relative weights.

For example:

characters: {
  "Character One": 60,
  "Character Two": 30,
  "Character Three": 10
}

This does not mean the values have to add up to 100.

The approximate relative chances are:

- Character One → 60%
- Character Two → 30%
- Character Three → 10%

The same proportions could also be written as:

60 / 30 / 10

or:

600 / 300 / 100

---

### 🧠 3. Story Card Context

When an NPC is selected, the system can look for their existing Story Card and provide its information to the AI as additional generation context.

This means you do not need to duplicate the character's personality and background inside the spawn configuration.

Your Story Card remains the source of character information.

The spawn system simply tells the AI:

«This established character is a possible presence in this location right now.»

The AI remains responsible for deciding how that character behaves within the generated scene.

---

### 👀 4. Output — Presence Confirmation

The system does not assume that a selected NPC actually appeared.

The Context hook selects a possible NPC.

The Output hook then checks the generated AI response to see whether that character actually appeared.

If the selected NPC is present in the generated text, the system records:

- The NPC
- Their current location
- The turn in which they appeared

This distinction helps prevent the internal state from claiming that a character is present when the AI never actually included them in the scene.

---

## ⚙️ Configuration

The main configuration is located inside the Library block.

Each location is configured as a single block.

Example:

{
  name: "YOUR LOCATION NAME",
  aliases: [
    "another way to name this place",
    "another possible name",
    "a common name for this location"
  ],
  firstEntryChance: 25,
  revisitChance: 10,
  charactersPerSpawn: 1,
  characters: {
    "Character One": 60,
    "Character Two": 30,
    "Character Three": 10
  }
}

"name"

The main name of the location.

This automatically becomes one of the terms used to detect the location.

"aliases"

Alternative ways the player might refer to the same location.

It is recommended to include as many natural variations as reasonably necessary.

"firstEntryChance"

The percentage chance of spawning NPCs when the player enters this location for the first time.

Example:

firstEntryChance: 70

means a 70% chance.

"revisitChance"

The percentage chance of spawning NPCs when the player returns to a location they have already visited.

Example:

revisitChance: 25

means a 25% chance.

"charactersPerSpawn"

How many different NPCs can be selected during one spawn event.

Example:

charactersPerSpawn: 2

allows the system to select up to two different NPCs.

"characters"

The NPCs that can appear at that location and their relative weights.

Example:

characters: {
  "Character One": 60,
  "Character Two": 30,
  "Character Three": 10
}

The character names should match the names used by their Story Cards.

---

# 📦 Installation

NPC Spawn System is designed as a creator tool, so it does not have an automatic installation system inside AI Dungeon.

You install it manually through AI Dungeon's script editor.

Requirements

- AI Dungeon script edit mode
- Basic familiarity with copying and pasting scripts into AI Dungeon
- Story Cards for the NPCs you want the system to spawn

Setup

The system contains four blocks:

1. Library
2. Input
3. Context
4. Output

Copy each corresponding block into the matching AI Dungeon script hook.

Configure your locations

The only part you normally need to edit is the configuration inside the Library block.

The Library contains an example location and a clearly marked template.

To add another location:

1. Copy the example/template location block.
2. Paste it below the previous location.
3. Change the location name.
4. Add its aliases.
5. Set the first-entry spawn chance.
6. Set the revisit spawn chance.
7. Set how many characters can spawn.
8. Add the NPC names and their relative weights.

You do not need to modify the main engine code.

The configuration is intentionally kept inside the script so that players cannot accidentally alter the scenario's spawn rules while playing.

---

# 🔄 Example

Imagine a scenario contains a recurring tavern called The Old Lantern.

You could configure:

{
  name: "The Old Lantern",
  aliases: [
    "the lantern",
    "Old Lantern",
    "the old tavern",
    "the tavern"
  ],
  firstEntryChance: 70,
  revisitChance: 30,
  charactersPerSpawn: 1,
  characters: {
    "Mira": 60,
    "Bram": 30,
    "Nell": 10
  }
}

When the player enters the tavern for the first time, the system may decide that an NPC should appear.

It could select Mira.

The AI then receives additional context indicating that Mira can naturally be present in the tavern, along with her existing Story Card information.

Later, when the player returns, the system performs another spawn check using the revisit chance.

The result can be different each time.

This allows a recurring location to feel populated without requiring the creator to manually script every possible visit.

---

# 🔒 Continuity

NPC Spawn System includes a lightweight continuity system.

When an NPC has recently appeared in one location, the system can temporarily prevent that NPC from being automatically selected for another location.

This helps avoid situations such as:

«The player leaves the tavern and immediately enters the library.»

And the system randomly decides that the same NPC should also be there moments later.

This does not permanently lock characters to locations.

NPCs can still move naturally through the story.

The system simply adds a short continuity buffer to reduce implausible automatic movement.

---

# 🎭 Universal & Scenario-Agnostic

NPC Spawn System intentionally does not contain scenario-specific characters, lore, factions, or locations.

The creator provides the locations and NPCs.

This makes it suitable for:

- 🏠 Slice-of-life scenarios
- 🌎 Sandbox scenarios
- 🧙 Fantasy adventures
- 🎸 Contemporary stories
- 🎭 Character-driven scenarios
- 🏙️ Urban settings
- 🏘️ Small-town settings
- 🗺️ RPG scenarios
- ❤️ Dating and social simulations
- 🕵️ Mystery scenarios
- 📖 Stories with many recurring locations

The system provides the framework.

The scenario provides the world.

---

# 🔌 Compatibility

NPC Spawn System is designed to be a standalone and universal script.

It has not been tested with other community-created AI Dungeon scripts, so compatibility with specific script combinations cannot currently be guaranteed.

However, the system is designed to be independent from other gameplay systems and should not inherently require or modify them.

It does not depend on:

- Relationship systems
- Fame systems
- Karma systems
- Chronicle systems
- Other scenario-specific mechanics

The script primarily uses its own state and its own logic while interacting with AI Dungeon's:

- Input
- Context
- Output
- Library

Because other scripts can also modify these same AI Dungeon hooks, unexpected interactions are still possible when combining multiple community scripts.

If you discover a compatibility issue, please report it so it can be investigated.

---

# ⚠️ Important Notes

NPC Spawn System does not force the AI to generate a character in every situation.

The system provides additional context and a spawn suggestion.

The AI still determines the actual narrative output.

This is intentional.

The purpose of the system is to make established NPCs more likely to appear naturally, not to turn every location into a rigid scripted encounter.

Likewise, selecting an NPC does not automatically mean that the NPC is considered present. The Output hook confirms whether the character actually appeared in the generated response.

---

# 🧪 Demo

The system does not have an automatic installation inside AI Dungeon because it is primarily intended as an editable creator tool.

However, a published AI Dungeon scenario is available as a demonstration.

In the demo, you can enter and leave the tavern repeatedly and observe how the system reacts to:

- First visits
- Returning to the location
- Different NPC selections
- NPC presence
- Location continuity

🎮 AI Dungeon Demo

[PLACEHOLDER — AI Dungeon Demo Link]

The demo is recommended if you want to see the system working before installing it into your own scenario.

---

# 🛠️ Current Status

Version: 1.0.1

Status: Functional / Active Development

The core NPC spawning system is functional and has been tested both through AI Dungeon's script testing environment and through a real scenario.

The current version includes:

- Location detection
- Location aliases
- First-entry spawning
- Revisit spawning
- Weighted NPC selection
- Multiple NPC selection
- Story Card context injection
- NPC presence confirmation
- Location tracking
- Recent NPC continuity protection

Further improvements may be added based on testing and community feedback.

---

# 📜 License

This project is free to use in your own AI Dungeon scenarios, including published scenarios.

You may:

- Use the system in your own scenarios.
- Modify the code.
- Integrate it into your own projects.
- Publish scenarios containing it.
- Create modified versions.

Credit is not required, but greatly appreciated.

If you use or modify this system in your scenario, I would appreciate a mention and, if possible, a link to this repository.

---

# ❤️ Credits

Created for AI Dungeon by Nynaleath.

This system was created as a universal creator tool to help AI Dungeon scenarios feel more alive and consistent when players repeatedly visit the same locations.

If you use it, I hope it makes your scenarios a little more lively. ^_^

For questions, suggestions, bug reports, or compatibility reports, please contact me through the AI Dungeon demo scenario or through the latest Reddit post about this script on my profile.
