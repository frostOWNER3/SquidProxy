User ID Spoofing
Purpose
This script intercepts the UserId property of the local player and replaces it with a spoofed value. It is useful to prevent tracking or account-specific checks.

How It Works
Accessing the Metatable:

The script modifies the __index metamethod of the game object using the getrawmetatable function.
By setting setreadonly to false, it enables edits to the metatable.
Intercepting UserId:

If the requested key is UserId and belongs to the LocalPlayer, it returns a spoofed value defined in getgenv().spoofedUserId.
Dynamic Customization:

getgenv() allows global access to the spoofed value, making it easy to modify during runtime.
Restoring Read-Only State:

After changes, the metatable is set back to read-only for security.

```lua
getgenv().spoofedUserId = 12345678

local Players = game:GetService("Players")
local mt = getrawmetatable(game)
setreadonly(mt, false)

local oldIndex = mt.__index
local LocalPlayer = Players.LocalPlayer

mt.__index = function(self, key)
    if self == LocalPlayer and key == "UserId" then
        return getgenv().spoofedUserId
    end
    return oldIndex(self, key)
end

setreadonly(mt, true)
```

Username Spoofing
Purpose
Replaces the Name and Username properties of the local player, making them appear as a custom value.

How It Works
Intercepting Name and Username:

Similar to User ID spoofing, it checks for keys Name and Username in the LocalPlayer.
Customizable Values:

The spoofed values are defined in getgenv().spoofedUsername, allowing runtime updates.
Enhanced Security:

Returns the original property values for other players, ensuring the spoofing is localized to LocalPlayer.

```lua
getgenv().spoofedUsername = "AnonymousUser" 

local Players = game:GetService("Players")
local mt = getrawmetatable(game)
setreadonly(mt, false)

local oldIndex = mt.__index
local LocalPlayer = Players.LocalPlayer

mt.__index = function(self, key)
    if self == LocalPlayer and (key == "Name" or key == "Username") then
        return getgenv().spoofedUsername
    end
    return oldIndex(self, key)
end

setreadonly(mt, true)
```

HWID Spoofing
Purpose
Spoofs the HWID (Hardware ID) by intercepting requests made to RbxAnalyticsService.

How It Works
Intercepting Namecall:

The script hooks into the __namecall metamethod, which is used internally by Roblox for remote service calls.
Spoofing HWID:

When GetClientId is called on the RbxAnalyticsService, it returns a spoofed HWID.
Dynamic HWID:

getgenv().spoofedHWID provides an easy way to set the HWID during runtime.
Bypassing Scans:

This ensures the spoofed value only affects HWID requests, leaving other namecalls intact.

```lua
getgenv().spoofedHWID = "SPOOFED-HWID"

local s = game:GetService("RbxAnalyticsService")
local mt = getrawmetatable(game)
local oldNamecall = mt.__namecall
setreadonly(mt, false)

mt.__namecall = newcclosure(function(self, ...)
    local method = getnamecallmethod()
    if self == s and method == "GetClientId" then
        warn("HWID Requested, returning spoofed value:", getgenv().spoofedHWID)
        return getgenv().spoofedHWID
    end
    return oldNamecall(self, ...)
end)

setreadonly(mt, true)
```

IP Address Spoofing
Purpose
Replaces the user's IP address when certain common IP-checking services are contacted.

How It Works
Hooking http_request:

This script hooks into the http_request or equivalent network function and intercepts outgoing requests.
Identifying IP Services:

Checks if the request URL matches a list of known IP-checking services (e.g., ipinfo.io, api.ipify.org).
Returning Spoofed IP:

For matching URLs, it replaces the response body with a spoofed IP address.
Customizable Spoofing:

Users can set their spoofed IP in getgenv().spoofedIP.
```lua
getgenv().spoofedIP = "192.168.1.1" 

local oldHttpRequest = http_request or request or (http and http.request)
if oldHttpRequest and hookfunction then
    hookfunction(oldHttpRequest, function(request)
        if request and type(request) == "table" and request.Url then
            local url = request.Url:lower()
            if url:find("api.ipify.org") or url:find("ipinfo.io") or url:find("ip-api.com") 
            or url:find("checkip.amazonaws.com") or url:find("icanhazip.com") then
                return {StatusCode = 200, Body = getgenv().spoofedIP}
            end
        end
        return oldHttpRequest(request)
    end)
end
```





if u need a spy we have squid spy

```lua
-- // Squid Spy
print("[Squid Spy] Loaded")
local r = print
local m = getrawmetatable(game)
local n = m.__namecall
local os_clock = os.clock
local os_date = os.date
setreadonly(m, false)

m.__namecall = newcclosure(function(self, ...)
    local c = getnamecallmethod()
    local a = {...}
    
    if c == "HttpGet" and type(a[1]) == "string" then
        local url = a[1]
        local start_time = os_clock()
        spawn(function()
            local timestamp = os_date("%Y-%m-%d %H:%M:%S")
            r(("[Squid Spy] [%s] HttpGet -> URL: %s"):format(timestamp, url))
        end)
        local result = n(self, ...)
        local duration = os_clock() - start_time
        spawn(function()
            r(("[Squid Spy] HttpGet completed in %.2f seconds"):format(duration))
        end)
        return result
    elseif c == "HttpPost" and type(a[1]) == "string" then
        local url = a[1]
        local data = a[2] or ""
        local headers = a[3] or {}
        local start_time = os_clock()
        spawn(function()
            local timestamp = os_date("%Y-%m-%d %H:%M:%S")
            r(("[Squid Spy] [%s] HttpPost -> URL: %s"):format(timestamp, url))
            r("  Data: " .. tostring(data))
            if headers and type(headers) == "table" then
                r("  Headers:")
                for k, v in pairs(headers) do
                    r("    " .. tostring(k) .. ": " .. tostring(v))
                end
            else
                r("  Headers: None")
            end
        end)
        local result = n(self, ...)
        local duration = os_clock() - start_time
        spawn(function()
            r(("[Squid Spy] HttpPost completed in %.2f seconds"):format(duration))
        end)
        return result
    elseif c == "RequestAsync" and type(a[1]) == "table" then
        local req = a[1]
        local url = req.Url or "N/A"
        local method = req.Method or "GET"
        local data = req.Body or ""
        local headers = req.Headers or {}
        local start_time = os_clock()
        spawn(function()
            local timestamp = os_date("%Y-%m-%d %H:%M:%S")
            r(("[Squid Spy] [%s] RequestAsync -> Method: %s"):format(timestamp, method))
            r("  URL: " .. url)
            if headers and type(headers) == "table" then
                r("  Headers:")
                for k, v in pairs(headers) do
                    r("    " .. tostring(k) .. ": " .. tostring(v))
                end
            else
                r("  Headers: None")
            end
            if data ~= "" then
                r("  Body: " .. tostring(data))
            end
        end)
        local result = n(self, ...)
        local duration = os_clock() - start_time
        spawn(function()
            r(("[Squid Spy] RequestAsync completed in %.2f seconds"):format(duration))
        end)
        return result
    end

    return n(self, ...)
end)

setreadonly(m, true)
```
