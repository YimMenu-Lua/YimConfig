# YimConfig

Universal Config System For YimMenu-Lua.

> [!NOTE]
> This is for script developers only.
> 
> Supports pretty encoding *(enabled by default)*.
> 
> Supports encryption *(Base64 + XOR)*.

## Setup

1. In your main script folder, add a new folder and name it "includes".
2. Download [YimConfig.lua](https://github.com/YimMenu-Lua/YimConfig/releases/latest) and place it inside the /includes folder.

## Usage

- **Initializing YimConfig:**

```Lua
local YimConfig = require("YimConfig")
local CFG = YimConfig:New(script_name, default_config)
```

- **Saving a variable:** `CFG:SaveItem("variable_name", value)`
- **Reading a variable:** `value = CFG:ReadItem("variable_name")`
- **Resetting everything to default:** `CFG:Reset()`
- **Encrypting a save file:** `CFG:Encrypt()`
- **Decrypting a save file:** `CFG:Decrypt()`
- **Reading a file regardless of whether it is encrypted or not:** `data = CFG:Read()`
- **Getting a Json instance:** `Json = CFG.json`

### Init Params

- `script_name`: string: **Required:** The name of your script (or any name you want). Will be used to create and manage the `.json` file.
- `default_config`: string/integer/table: **Required:** The default config. Can be a single string or number value or a table. Must be provided and will be used to fall back to it if the config file fails to be created/read.
- `pretty_encoding`: boolean: **Optional:** Pretty format your save file instead of one single line *(if using a table config)*.
- `indent`: number: **Optional:** Number of indent spaces if using pretty encoding *(defaults to 2)*.
- `strict_parsing`: boolean: **Optional:** Used to guarantee decode-encode round-trip equivalency by marking Lua tables with metatables. Can be useful if you lose float precision after encoding and decoding a large float but can also cause issues in other applications like parsing empty strings.
- `encryption_key`: string: **Optional:** A string or string of bytes that will be used internally as an encryption/decyption key.

## Example Code

```Lua
local script_name = "YimConfig Test"
local YimConfig = require("YimConfig")
local config_test = gui.add_tab(script_name)
local default_cfg = {
    bool_1 = false,
    bool_2 = true,
    string_1 = "a",
    number_1 = 1,
}

local CFG = YimConfig:New(script_name, default_cfg)
local bool_1 = CFG:ReadItem("bool_1")
local bool_2 = CFG:ReadItem("bool_2")
local string_1 = CFG:ReadItem("string_1")
local number_1 = CFG:ReadItem("number_1")

config_test:add_imgui(function()
    bool_1, b1used = ImGui.Checkbox("First Bool", bool_1)
    if b1used then
        CFG:SaveItem("bool_1", bool_1)
    end

    ImGui.SameLine()
    bool_2, b2used = ImGui.Checkbox("Second Bool", bool_2)
    if b2used then
        CFG:SaveItem("bool_2", bool_2)
    end

    ImGui.Text(string.format("String = %s", string_1))
    ImGui.SameLine()
    if ImGui.Button("Change String") then
        string_1 = string_1 == "a" and "Z" or "a"
        CFG:SaveItem("string_1", string_1)
    end

    ImGui.Text(string.format("Number = %s", number_1))
    ImGui.SameLine()
    if ImGui.Button("Change Number") then
        number_1 = number_1 == 1 and 69 or 1
        CFG:SaveItem("number_1", number_1)
    end

    if ImGui.Button("Check Saved Config") then
        for c, _ in pairs(default_cfg) do
            log.debug(string.format("%s = %s", c, CFG:ReadItem(tostring(c))))
        end
    end

    if ImGui.Button("Reset To Default") then
        CFG:Reset()
        bool_1 = CFG:ReadItem("bool_1")
        bool_2 = CFG:ReadItem("bool_2")
        string_1 = CFG:ReadItem("string_1")
        number_1 = CFG:ReadItem("number_1")
    end
end)
```

## Credits

- [Harmless](https://github.com/harmless05) For the original code.
- [Jeffrey Friedl's JSON.lua package](http://regex.info/blog/lua/json).
