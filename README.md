# Amphibia

A UI library for Roblox, written in Luau. Tabs, sections and a full set of controls, with
per-element keybinds, saved configs, themes and search built in.

The interface is drawn from a hand-made design rather than assembled in code: every window, row and
chip comes from a layout authored in Studio, which is why spacing and motion stay consistent across
elements.

> **Status.** Feature-complete and in use, but no longer actively developed. Issues may go
> unanswered — fork freely.

---

## Install

```lua
local Amphibia = loadstring(game:HttpGet("https://raw.githubusercontent.com/janederose/amphibia-library/refs/heads/main/library.lua"))()
```

## Quick start

```lua
local Window = Amphibia:CreateWindow({
    Name = "amphibia",
    Version = "v1.0",
    ToggleUIKeybind = "K",
    Theme = "Default",
    ConfigurationSaving = { Enabled = true, FolderName = "Amphibia", FileName = "Default" },
})

local Tab = Window:CreateTab({ Name = "Player", Category = "Main" })
local Section = Tab:CreateSection({ Name = "Movement", Side = "Left" })

Section:CreateToggle({
    Name = "Fly",
    CurrentValue = false,
    Flag = "fly",
    Callback = function(value)
        print("fly =", value)
    end,
})

Section:CreateSlider({
    Name = "WalkSpeed",
    Range = { 16, 250 },
    Increment = 1,
    CurrentValue = 16,
    Flag = "walkspeed",
    Callback = function(value) end,
})

Window:CreateConfigTab({ Category = "Menu" })
```

---

## Features

- **Keybinds on anything.** Right-click any element to bind a key to it. Every type supports both
  `Hold` and `Toggle`; a bind stores the value to apply and restores what was there on release.
- **Configs.** Save, load, overwrite, rename and delete, plus export and import as a shareable
  code. Keybinds travel inside the config.
- **Themes.** Five palettes, applied live without rebuilding the interface.
- **Search.** Ranked across tabs, sections and elements, with typo tolerance — <kbd>Enter</kbd>
  jumps straight to the best match.
- **Notifications** and a connection status line.
- **Key system** with a remembered key, a loading screen and a minimised pill to bring the menu
  back.

---

## Window

```lua
Amphibia:CreateWindow({
    Name = "amphibia",                -- title
    Version = "v1.0",                 -- shown next to the title
    ToggleUIKeybind = "K",            -- key that hides and shows the menu

    KeySystem = false,
    KeySettings = {
        Title = "Welcome to amphibia.",
        Subtitle = "Enter your key to get started.",
        Key = { "yourkey" },          -- string or list of strings
        SaveKey = true,               -- remember it and sign in automatically next time
        Discord = "https://discord.gg/...",
        Website = "https://...",
        Youtube = "https://...",
    },

    ConfigurationSaving = {
        Enabled = true,
        FolderName = "Amphibia",
        FileName = "Default",
    },

    Theme = "Default",                -- Default | Midnight | Sakura | Emerald | Ocean
    LoadingTitle = "Just a moment...",
    LoadingDescription = "Setting things up.",
    LoadingTime = 2.4,
})
```

### Window methods

| Method | Does |
|---|---|
| `CreateTab({ Name, Category })` | New tab; the category is the group it appears under |
| `CreateConfigTab({ Name, Category })` | The built-in configs page |
| `CreateCategory(name)` | A category with no tabs in it yet |
| `SelectTab(tab)` | Switch tabs |
| `SetTheme(name)` | Change palette |
| `SetToggleKey(key)` | Rebind the show/hide key |
| `SetKeybindsListMode(mode)` | `"Default"`, `"Transparent"` or `"Hidden"` |
| `SetStatus(text, connected)` | Status line at the bottom |
| `Notify(settings)` | Same as `Amphibia:Notify` |
| `Toggle(open)` | Show or hide the menu |
| `SaveConfiguration()` / `LoadConfiguration()` | Write or read the current config |
| `Destroy()` | Unload the interface |

### Tabs and sections

```lua
local Tab = Window:CreateTab({ Name = "World", Category = "Main" })

local Section = Tab:CreateSection({
    Name = "Visuals",
    Side = "Right",      -- "Left" or "Right"
    Collapsed = false,   -- start folded
})
```

---

## Elements

Every element takes a `Name`, an optional `Flag` (the key it is saved and read under) and an
optional `Callback`. Each returns an object with a `Set` method, so it can be driven from code.

### Toggle

```lua
local Toggle = Section:CreateToggle({
    Name = "Noclip",
    CurrentValue = false,
    Flag = "noclip",
    Callback = function(value) end,
})

Toggle:Set(true)
```

### Slider

```lua
Section:CreateSlider({
    Name = "JumpPower",
    Range = { 50, 400 },
    Increment = 5,
    Suffix = "",          -- appended to the readout
    CurrentValue = 50,
    Flag = "jumppower",
    Callback = function(value) end,
})
```

### Number picker

```lua
Section:CreateNumberPicker({
    Name = "Distance",
    Min = 0, Max = 100, Increment = 1,
    CurrentValue = 20,
    Flag = "distance",
    Callback = function(value) end,
})
```

### Button

```lua
local Button = Section:CreateButton({ Name = "Respawn", Callback = function() end })
Button:Set("Renamed")
```

### Input

```lua
Section:CreateInput({
    Name = "Target",
    CurrentValue = "",
    PlaceholderText = "username",
    MaxLength = 48,
    Numeric = false,       -- digits, dot and minus only
    ClearOnSubmit = false,
    BoxWidth = 150,
    Flag = "target",
    Callback = function(text) end,
})
```

### Selector

A row of fixed choices with a sliding indicator. Best for two or three options.

```lua
Section:CreateSelector({
    Name = "Mode",
    Options = { "Off", "Low", "High" },
    CurrentOption = "Low",
    Flag = "mode",
    Callback = function(option) end,
})
```

### Dropdown

```lua
local Players = Section:CreateDropdown({
    Name = "Players",
    Options = { "Item 1", "Item 2" },
    CurrentOption = "Item 1",      -- a list when MultipleOptions is on
    MultipleOptions = false,
    Flag = "players",
    Callback = function(option) end,
})

Players:Refresh({ "New", "List" }, true)   -- second argument keeps the selection
```

### Colour picker

```lua
Section:CreateColorPicker({
    Name = "ESP box",
    Color = Color3.fromRGB(143, 168, 160),
    Transparency = 0,
    Flag = "esp_box",
    Callback = function(color, transparency) end,
})
```

### Label

```lua
local Label = Section:CreateLabel({ Name = "Some text" })
Label:Set("Updated text")
```

### Progress bar

Display only: it takes no input, has no keybind menu and is not stored in configs.

```lua
local Bar = Section:CreateProgressBar({
    Name = "Downloading assets",     -- while it is running
    IdleName = "Ready",              -- while it sits at the minimum
    CompletedName = "Done",          -- once it reaches the maximum
    Min = 0, Max = 100, Value = 0,
    Increment = 1,                   -- rounding step for the readout
    Suffix = "",                     -- unit, e.g. " MB"
    Format = "PercentOfTotal",       -- Percent | PercentOfTotal | Value | ValueOfMax | None | function
    Color = Color3.fromRGB(120, 205, 140),
    Indeterminate = false,           -- unknown duration: the fill sweeps instead
    HideValueOnComplete = true,      -- the readout fades out at the finish
    HideValueDelay = 1.5,
    Animate = true,
    Callback = function(value, fraction) end,
    OnComplete = function(value) end,
})

Bar:Set(40)
Bar:Add(10)
Bar:SetFraction(0.75)
Bar:SetIndeterminate(true)
Bar:Complete()
Bar:Reset()
```

Also available: `GetValue`, `GetFraction`, `SetRange`, `SetName`, `SetIdleName`,
`SetCompletedName`, `SetValueText`, `SetFormat`, `SetSuffix`, `SetColor`, `SetVisible`.

A custom `Format` is a function:

```lua
Format = function(value, fraction, bar)
    return ("%d / %d rounds"):format(value, bar.Max)
end
```

### Keybind element

There isn't one. Right-click any element to manage its keybinds instead. `CreateKeybind` is kept as
a stub that warns, so older scripts do not hard-crash.

---

## Keybinds

Right-click an element, choose **+ New keybind**, press a key. Each bind has a mode:

- **Hold** — applies the bound value while the key is down, restores the previous value on release.
- **Toggle** — applies on the first press, restores on the next.
- **Press** — buttons only, fires once.

A toggle needs no value: binding one means "flip it". Several binds on one element share a single
record of the pre-bind value, so overlapping them never loses the original.

The overlay listing active binds can be dragged, and its position is remembered between sessions.

```lua
Amphibia.BindSystem.Add({ Element = Toggle, Key = "F", Mode = "Toggle" })
Amphibia.BindSystem.ForElement(Toggle)
Amphibia.BindSystem.Remove(bind)
```

---

## Flags and configs

Any element given a `Flag` is readable from code and saved into configs:

```lua
if Amphibia.Flags["fly"] then
    -- ...
end

Amphibia.Elements["fly"]:Set(false)
```

Configs live under the folder from `ConfigurationSaving` and store flags together with keybinds.
The configs page handles saving, loading, renaming and deleting, plus export and import through a
shareable code.

---

## Themes

```lua
Amphibia:SetTheme("Midnight")
Amphibia:GetTheme()
Amphibia:GetThemes()   -- { "Default", "Midnight", "Sakura", "Emerald", "Ocean" }
```

Themes retint the whole interface live. Colours the user picked themselves are left alone.

---

## Notifications

```lua
Amphibia:Notify({ Content = "Saved.", Color = "Green", Duration = 4 })   -- Green | Yellow | Red
```

---

## Notes

**The key system is not security.** The script runs on the player's machine, so the list of valid
keys is in memory and the gate can be stepped over. It stops casual sharing and nothing more. Real
protection requires a server that checks the key and returns the code itself.

**Executor support.** Configs and the remembered key need `readfile`, `writefile` and `isfolder`.
Without them the interface still works, but nothing persists between sessions.

---

## License

<!-- TODO: pick one. MIT is the usual choice for a library you want others to build on. -->
