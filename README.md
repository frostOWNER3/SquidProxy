# USER ID most unsecure

idk why u would use this only 1 acc can be used (worst)

```lua
local Players = game:GetService("Players")
local mt = getrawmetatable(game)
setreadonly(mt, false)

local oldIndex = mt.__index
local LocalPlayer = Players.LocalPlayer

mt.__index = function(self, key)
    if self == LocalPlayer and key == "UserId" then
        return 123
    end
    return oldIndex(self, key)
end

setreadonly(mt, true)
```
ereplace 123 with just a number
