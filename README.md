# YimConfig

Universal Config System For YimMenu-Lua.

> [!NOTE]
> This is for script developers only.
> Supports pretty encoding *(enabled by default)*.

## Setup

1. In your main script folder, add a new folder and name it "includes".
2. Download [YimConfig.lua](https://github.com/YimMenu-Lua/YimConfig/releases/latest) and place it inside the /includes folder.
3. In your main script, add these globals:
    - `Config = require("YimConfig") -- You can name "Config" anything you want. You're gonna be calling it later to save and read values.`
    - `SCRIPT_NAME = "your_script_name" -- This global has to be exactly SCRIPT_NAME. Do not change it.`
    - `DEFAULT_CONFIG = {-- place your default variables here} -- anything that you're gonna be modifying, saving and reading, initialize it here and the global's name has to be DEFAULT_CONFIG, do not change it.`

## Usage

- Saving a variable: `Config.save("variable_name", variable_value)`
- Reading a variable: `Config.read("variable_name")`
- Resetting everything to default values stored in DEFAULT_CONFIG: `Config.reset()`

## Example Code

```Lua
-- create a new tab
config_test = gui.add_tab("Config Test")

-- We create a global named SCRIPT_NAME and give it a name that represents our script.
-- It will be used by the config to create a json file.
-- In this case, the file would be named "config_test.json"
SCRIPT_NAME = "config_test"

-- We create a global table named DEFAULT_CONFIG and place our variables inside along
-- with their default values.
DEFAULT_CONFIG = {
  bool_1 = false,
  bool_2 = true,
  string_1 = "a",
  number_1 = 1,
}

-- We require YimConfig. If we need to call this from another file,
-- this must be global instead.
local CFG = require("YimConfig")

-- We define our variables by reading them from the config.
local bool_1   = CFG.read("bool_1")
local bool_2   = CFG.read("bool_2")
local string_1 = CFG.read("string_1")
local number_1 = CFG.read("number_1")
config_test:add_imgui(function()
 -- We can simply call our local variables that we defined above but
 -- we're gonna be calling CFG.read() inside ImGui widgets instead.
 -- That way, when we reset the config the values will update immediately.
 -- Otherwise, we'll have to reload the script to see changes after resetting.
  bool_1, b1used = ImGui.Checkbox("First Bool", CFG.read("bool_1"))
  if b1used then
    CFG.save("bool_1", bool_1)
  end
  ImGui.SameLine()
  bool_2, b2used = ImGui.Checkbox("Second Bool", CFG.read("bool_2"))
  if b2used then
    CFG.save("bool_2", bool_2)
  end
  ImGui.Text(string.format("String = %s", CFG.read("string_1")))
  ImGui.SameLine()
  if ImGui.Button("Change String") then
    string_1 = string_1 == "a" and "Z" or "a"
    CFG.save("string_1", string_1)
  end
  ImGui.Text(string.format("Number = %s", CFG.read("number_1")))
  ImGui.SameLine()
  if ImGui.Button("Change Number") then
    number_1 = number_1 == 1 and 69 or 1
    CFG.save("number_1", number_1)
  end
  if ImGui.Button("Check Saved Config") then
    for c, _ in pairs(DEFAULT_CONFIG) do
      log.debug(string.format("%s = %s", c, CFG.read(tostring(c))))
    end
  end
  if ImGui.Button("Reset To Default") then
    CFG.reset()
  end
end)
```

## Credits

- [Harmless](https://github.com/harmless05) For the original code.
- [Jeffrey Friedl's JSON.lua package](http://regex.info/blog/lua/json).
