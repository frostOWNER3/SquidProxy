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


same thing for username so unsecure uwu
```lua
local Players = game:GetService("Players")
local mt = getrawmetatable(game)
setreadonly(mt, false)

local oldIndex = mt.__index
local LocalPlayer = Players.LocalPlayer

mt.__index = function(self, key)
    if self == LocalPlayer and (key == "Name" or key == "Username") then
        return "Anonymous"
    end
    return oldIndex(self, key)
end

setreadonly(mt, true)
```

hwid also retarded but needs dump so good most use this 

```lua
local hwid = "SPOOFED-HWID"
local s = game:GetService("RbxAnalyticsService")
local m = getrawmetatable(game)
local o1 = m.__namecall
setreadonly(m, false)

m.__namecall = newcclosure(function(self, ...)
    local n = getnamecallmethod()
    if self == s and n == "GetClientId" then
        warn("HWID Requested, returning spoofed value:", hwid)
        return hwid
    end
    return o1(self, ...)
end)

setreadonly(m, true)
```

ip barely used 
```lua
local spoofedIP="400"
local old_http_request=http_request or request or (http and http.request)
if old_http_request and hookfunction then
    hookfunction(old_http_request,function(o)
        if o and type(o)=="table" and o.Url and type(o.Url)=="string" then
            local u=o.Url:lower()
            if u:find("api.ipify.org") or u:find("ipinfo.io") or u:find("ip-api.com")
            or u:find("checkip.amazonaws.com") or u:find("icanhazip.com") then
                return {StatusCode=200,Body=spoofedIP}
            end
        end
        return old_http_request(o)
    end)
end
```
if they dont use this site then just http spy and replace
