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
