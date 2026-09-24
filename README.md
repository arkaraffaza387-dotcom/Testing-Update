--[[
    ZetGames-AimLock-Advanserver V4.3 | TESTING BUILD (VPN V2) - FIXED
    Theme: Red & Black Hacker Style
    Login: ❌ TANPA LOGIN (langsung muncul menu)
    Features: VPN V2 + Fly Normal + Theme + Config + NPC Detection + Aimbot + ESP
    Night Lock: REMOVED (Testing Build)
--]]

--==============================================================
-- SERVICES
--==============================================================
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TextChatService = game:GetService("TextChatService")
local SoundService = game:GetService("SoundService")
local CoreGui = game:GetService("CoreGui")
local HttpService = game:GetService("HttpService")
local TeleportService = game:GetService("TeleportService")
local VirtualUser = game:GetService("VirtualUser")
local Camera = workspace.CurrentCamera

local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

pcall(function()
    SoundService.RespectFilteringEnabled = false
    SoundService.Volume = 1
end)

--==============================================================
-- THEME
--==============================================================
local THEME = {
    MainBG = Color3.fromRGB(10, 10, 10),
    PanelBG = Color3.fromRGB(15, 15, 15),
    SectionBG = Color3.fromRGB(40, 0, 0),
    AccentColor = Color3.fromRGB(255, 0, 0),
    AccentLight = Color3.fromRGB(255, 80, 80),
    AccentDark = Color3.fromRGB(150, 0, 0),
    ButtonBG = Color3.fromRGB(25, 25, 25),
    ButtonActive = Color3.fromRGB(100, 0, 0),
    TextColor = Color3.fromRGB(255, 0, 0),
    TextLight = Color3.fromRGB(255, 100, 100),
    BlueDot = Color3.fromRGB(0, 150, 255),
    RedDot = Color3.fromRGB(255, 30, 30),
    YellowDot = Color3.fromRGB(255, 220, 0),
    GreenDot = Color3.fromRGB(0, 255, 100),
    ShieldColor = Color3.fromRGB(0, 255, 200),
}

--==============================================================
-- VARIABLES
--==============================================================
local AimbotEnabled = false
local AimbotTargetPart = "Head"
local AimbotFOV = 250
local AimbotKeybindEnabled = false
local AimbotKeybind = Enum.KeyCode.E
local AimbotKeyHeld = false
local AimbotStickyTarget = nil
local AimbotStickyType = nil
local AimbotTeamCheck = false
local AimbotWallCheck = false
local FOVCircleEnabled = false

local NPCDetectionEnabled = false
local NPCEspEnabled = false
local NPCFilterName = ""
local NPCMaxDistance = 500
local NPCMaxCount = 50
local NPCWhitelistKeywords = {"pet", "shop", "npc_store", "vendor", "trainer"}
local NPCESPObjects = {}

local ESPEnabled = false
local ESPObjects = {}
local ChamsObjects = {}
local TracerColor = Color3.fromRGB(255, 0, 0)
local RainbowESPEnabled = false
local RainbowConnection = nil
local RainbowHue = 0

local RadarEnabled = false
local RadarRadius = 300
local RadarZoom = 1.0
local RadarRotateWithCamera = false
local RadarShowNames = false
local RadarConnection = nil
local RadarObjects = {}
local RadarFrame = nil
local RadarDrawingBg = nil
local RadarDrawingBorder = nil
local RadarDrawingCenterDot = nil
local RadarDrawingCompass = {}
local RadarNearestLabel = nil

local FlyNormalEnabled = false
local FlyNormalConnection = nil
local FlyNormalSpeed = 100
local FlyNormalMode = "Free"
local FlyNormalKeybind = Enum.KeyCode.F
local FlyNormalVelocity = nil
local FlyNormalGyro = nil
local FlyNormalKeyHeld = {W=false, A=false, S=false, D=false, Space=false, Ctrl=false}

local FlyVoidEnabled = false
local FlyVoidConnection = nil
local FlyVoidHideMode = false
local FlyVoidKeybind = Enum.KeyCode.V
local FlyVoidOriginalY = nil
local OriginalTransparency = {}
local OriginalCanCollide = {}

local FullbrightEnabled = false
local OriginalLighting = {}

local FPSBoostEnabled = false
local OriginalSettings = {}

local SpeedHackEnabled = false
local SpeedMultiplier = 100
local MaxSpeed = 500
local DefaultWalkSpeed = 16

local NoclipEnabled = false
local NoclipConnection = nil

local InfiniteJumpEnabled = false
local JumpConnection = nil

local ChatSpamEnabled = false
local ChatSpamText = "ZETGAMES-AIMLOCK-ADVANSERVER"
local ChatSpamDelay = 3

local SoundESPEnabled = false
local SoundESPRadius = 100
local SoundESPConnection = nil
local SoundESPBeep = nil
local LastBeepTime = 0

local MusicPlayerEnabled = false
local MusicSound = nil
local MusicPlaylist = {
    {Name = "Kelingan Mantan", ID = "78450316593213"},
    {Name = "Teh Hijau", ID = "111485011584825"},
}
local MusicCurrentIndex = 1
local MusicVolume = 1
local MusicLoading = false

local AutoRespawnEnabled = false
local AutoRespawnConnection = nil
local LookAtEnabled = false
local AntiFlingEnabled = false
local AntiFlingConnection = nil

local AntiAFKEnabled = false
local AntiAFKConnection = nil

local HitboxEnabled = false
local HitboxSize = 5
local HitboxConnection = nil

local ServerHopRunning = false

local TeleportPlayerList = {}
local TeleportListFrame = nil
local TeleportListContainer = nil

local SavedLocation = nil

local IsLoggedIn = true
local MenuVisible = true
local MenuKey = Enum.KeyCode.RightControl

local CurrentThemeName = "Merah"

-- VPN V2
local VPNActive = false
local AntiKickEnabled = false
local AutoReconnectEnabled = false
local AntiDetectEnabled = false
local NetworkWatchdogEnabled = false
local WhitelistEnabled = false
local KickLog = {}
local MaxKickLog = 20
local ReconnectAttempts = 0
local MaxReconnectAttempts = 10
local WatchdogLastPing = tick()
local WatchdogPingThreshold = 30
local OriginalKick = nil

local FOVCircle = Drawing.new("Circle")
FOVCircle.Visible = false
FOVCircle.Color = THEME.AccentColor
FOVCircle.Thickness = 2
FOVCircle.Filled = false
FOVCircle.Transparency = 1
FOVCircle.NumSides = 60

--==============================================================
-- SCREEN GUI
--==============================================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ZetGamesVPN_V43"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = CoreGui

--==============================================================
-- NOTIFICATION
--==============================================================
local Notifications = Instance.new("Frame")
Notifications.Size = UDim2.new(0, 250, 1, 0)
Notifications.Position = UDim2.new(1, -260, 0, 10)
Notifications.BackgroundTransparency = 1
Notifications.ZIndex = 500
Notifications.Parent = ScreenGui

local function Notify(title, message, duration)
    duration = duration or 2
    local Notif = Instance.new("Frame")
    Notif.Size = UDim2.new(1, 0, 0, 55)
    Notif.Position = UDim2.new(0, 0, 0, -55)
    Notif.BackgroundColor3 = THEME.PanelBG
    Notif.BorderColor3 = THEME.AccentColor
    Notif.BorderSizePixel = 2
    Notif.ZIndex = 501
    Notif.Parent = Notifications
    Instance.new("UICorner", Notif).CornerRadius = UDim.new(0, 8)

    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, -16, 0, 22)
    Title.Position = UDim2.new(0, 8, 0, 4)
    Title.BackgroundTransparency = 1
    Title.Text = title
    Title.TextColor3 = THEME.TextColor
    Title.Font = Enum.Font.Code
    Title.TextSize = 12
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.ZIndex = 502
    Title.Parent = Notif

    local Msg = Instance.new("TextLabel")
    Msg.Size = UDim2.new(1, -16, 0, 22)
    Msg.Position = UDim2.new(0, 8, 0, 28)
    Msg.BackgroundTransparency = 1
    Msg.Text = message
    Msg.TextColor3 = THEME.TextLight
    Msg.Font = Enum.Font.Code
    Msg.TextSize = 10
    Msg.TextXAlignment = Enum.TextXAlignment.Left
    Msg.ZIndex = 502
    Msg.Parent = Notif

    TweenService:Create(Notif, TweenInfo.new(0.3), {Position = UDim2.new(0, 0, 0, 0)}):Play()
    task.delay(duration, function()
        local tw = TweenService:Create(Notif, TweenInfo.new(0.3), {Position = UDim2.new(0, 0, 0, -55)})
        tw:Play()
        tw.Completed:Connect(function() Notif:Destroy() end)
    end)
end

--==============================================================
-- VPN V2 CORE FUNCTIONS
--==============================================================
local function AddKickLog(message, status)
    table.insert(KickLog, 1, {
        Time = os.date("%H:%M:%S"),
        Message = tostring(message),
        Status = status or "BLOCKED"
    })
    if #KickLog > MaxKickLog then
        table.remove(KickLog)
    end
end

local BlockedKickKeywords = {
    "exploit", "cheat", "hack", "aimbot", "kick", "ban", "detect",
    "script", "injector", "banned", "violation", "suspicious",
    "anti-cheat", "anticheat", "malicious", "unauthorized"
}

local LegitKickKeywords = {
    "shutdown", "shutting down", "server closed", "restart",
    "update", "maintenance", "rejoin"
}

local function ShouldBlockKick(message)
    if not message then return true, "NULL" end
    local msgLower = string.lower(tostring(message))
    for _, kw in ipairs(BlockedKickKeywords) do
        if string.find(msgLower, kw, 1, true) then
            return true, kw
        end
    end
    return false, nil
end

local function IsLegitKick(message)
    if not message then return false end
    local msgLower = string.lower(tostring(message))
    for _, kw in ipairs(LegitKickKeywords) do
        if string.find(msgLower, kw, 1, true) then
            return true
        end
    end
    return false
end

local function ActivateAntiKick()
    if not OriginalKick then
        OriginalKick = LocalPlayer.Kick
    end
    pcall(function()
        LocalPlayer.Kick = newcclosure(function(self, message)
            if not AntiKickEnabled then
                return OriginalKick(self, message)
            end
            if IsLegitKick(message) then
                AddKickLog(message, "ALLOWED")
                return OriginalKick(self, message)
            end
            local shouldBlock, kw = ShouldBlockKick(message)
            if shouldBlock then
                AddKickLog(message, "BLOCKED")
                Notify("🛡️ VPN", "> Kick diblokir: " .. tostring(kw), 3)
                return
            end
            AddKickLog(message, "ALLOWED")
            return OriginalKick(self, message)
        end)
    end)
    Notify("🛡️ VPN V2", "> Anti-Kick ACTIVE", 2)
end

local function DeactivateAntiKick()
    if OriginalKick then
        pcall(function()
            LocalPlayer.Kick = OriginalKick
        end)
    end
    Notify("🛡️ VPN V2", "> Anti-Kick OFF", 2)
end

local function ActivateAntiDetect()
    pcall(function()
        if getgc then
            for _, obj in pairs(getgc(true)) do
                if type(obj) == "table" and rawget(obj, "IsScript") then
                    pcall(function() obj.IsScript = false end)
                end
            end
        end
    end)
    Notify("🕵️ VPN V2", "> Anti-Detect ACTIVE", 2)
end

local function DeactivateAntiDetect()
    Notify("🕵️ VPN V2", "> Anti-Detect OFF", 2)
end

local function AttemptReconnect()
    if ReconnectAttempts >= MaxReconnectAttempts then
        Notify("🔄 VPN V2", "> Reconnect max!", 3)
        ReconnectAttempts = 0
        return
    end
    ReconnectAttempts = ReconnectAttempts + 1
    Notify("🔄 VPN V2", "> Reconnect #" .. ReconnectAttempts, 2)
    pcall(function()
        TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId, LocalPlayer)
    end)
end

local AutoReconnectConnection = nil
local function ActivateAutoReconnect()
    if AutoReconnectConnection then AutoReconnectConnection:Disconnect() end
    AutoReconnectConnection = RunService.Heartbeat:Connect(function()
        if not AutoReconnectEnabled then return end
        local now = tick()
        if now - WatchdogLastPing > WatchdogPingThreshold then
            WatchdogLastPing = now
            AttemptReconnect()
        end
    end)
    Notify("🔄 VPN V2", "> Auto-Reconnect ACTIVE", 2)
end

local function DeactivateAutoReconnect()
    if AutoReconnectConnection then AutoReconnectConnection:Disconnect(); AutoReconnectConnection = nil end
    Notify("🔄 VPN V2", "> Auto-Reconnect OFF", 2)
end

local NetworkWatchdogConnection = nil
local function ActivateNetworkWatchdog()
    if NetworkWatchdogConnection then NetworkWatchdogConnection:Disconnect() end
    WatchdogLastPing = tick()
    NetworkWatchdogConnection = RunService.Heartbeat:Connect(function()
        if not NetworkWatchdogEnabled then return end
        local now = tick()
        if now - WatchdogLastPing > WatchdogPingThreshold then
            AddKickLog("Network stall " .. math.floor(WatchdogPingThreshold) .. "s", "DETECTED")
            WatchdogLastPing = now
            Notify("📡 VPN V2", "> Network stall!", 2)
            if AutoReconnectEnabled then AttemptReconnect() end
        else
            WatchdogLastPing = now
        end
    end)
    Notify("📡 VPN V2", "> Watchdog ACTIVE", 2)
end

local function DeactivateNetworkWatchdog()
    if NetworkWatchdogConnection then NetworkWatchdogConnection:Disconnect(); NetworkWatchdogConnection = nil end
    Notify("📡 VPN V2", "> Watchdog OFF", 2)
end

local function ActivateVPN()
    VPNActive = true
    AntiKickEnabled = true
    AntiDetectEnabled = true
    ActivateAntiKick()
    ActivateAntiDetect()
    Notify("🔒 VPN V2", "> MASTER ACTIVE", 3)
end

local function DeactivateVPN()
    VPNActive = false
    AntiKickEnabled = false
    AntiDetectEnabled = false
    DeactivateAntiKick()
    DeactivateAntiDetect()
    Notify("🔒 VPN V2", "> MASTER OFF", 2)
end

--==============================================================
-- NPC DETECTION
--==============================================================
local function IsNPC(model)
    if not model or not model:IsA("Model") then return false end
    if Players:GetPlayerFromCharacter(model) then return false end
    if model == LocalPlayer.Character then return false end
    if not model:FindFirstChild("Humanoid") then return false end
    if not model:FindFirstChild("HumanoidRootPart") then return false end
    local hum = model.Humanoid
    if hum.Health <= 0 then return false end

    local nameLower = string.lower(model.Name)
    for _, keyword in ipairs(NPCWhitelistKeywords) do
        if string.find(nameLower, keyword, 1, true) then
            return false
        end
    end

    if NPCFilterName ~= "" then
        if not string.find(nameLower, string.lower(NPCFilterName), 1, true) then
            return false
        end
    end

    local myRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    local npcRoot = model:FindFirstChild("HumanoidRootPart")
    if myRoot and npcRoot then
        local dist = (npcRoot.Position - myRoot.Position).Magnitude
        if dist > NPCMaxDistance then return false end
    end
    return true
end

local function GetNPCs()
    local list = {}
    local count = 0
    for _, obj in pairs(Workspace:GetChildren()) do
        if IsNPC(obj) then
            table.insert(list, obj)
            count = count + 1
            if count >= NPCMaxCount then break end
        end
    end
    return list
end

--==============================================================
-- FLY NORMAL
--==============================================================
local function EnableFlyNormal()
    local char = LocalPlayer.Character
    if not char then return end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end

    if FlyNormalVelocity then FlyNormalVelocity:Destroy() end
    if FlyNormalGyro then FlyNormalGyro:Destroy() end

    FlyNormalVelocity = Instance.new("BodyVelocity")
    FlyNormalVelocity.Velocity = Vector3.new(0, 0, 0)
    FlyNormalVelocity.MaxForce = Vector3.new(1e9, 1e9, 1e9)
    FlyNormalVelocity.P = 1e5
    FlyNormalVelocity.Parent = root

    FlyNormalGyro = Instance.new("BodyGyro")
    FlyNormalGyro.MaxTorque = Vector3.new(1e9, 1e9, 1e9)
    FlyNormalGyro.P = 1e5
    FlyNormalGyro.CFrame = root.CFrame
    FlyNormalGyro.Parent = root

    if FlyNormalConnection then FlyNormalConnection:Disconnect() end
    FlyNormalConnection = RunService.RenderStepped:Connect(function()
        if not FlyNormalEnabled then return end
        local c = LocalPlayer.Character
        if not c then return end
        local r = c:FindFirstChild("HumanoidRootPart")
        local h = c:FindFirstChild("Humanoid")
        if not r or not h then return end

        if FlyNormalVelocity and FlyNormalVelocity.Parent ~= r then
            FlyNormalVelocity.Parent = r
        end
        if FlyNormalGyro and FlyNormalGyro.Parent ~= r then
            FlyNormalGyro.Parent = r
        end

        local camCF = Camera.CFrame
        local dir = Vector3.new(0, 0, 0)
        if FlyNormalKeyHeld.W then dir = dir + camCF.LookVector end
        if FlyNormalKeyHeld.S then dir = dir - camCF.LookVector end
        if FlyNormalKeyHeld.A then dir = dir - camCF.RightVector end
        if FlyNormalKeyHeld.D then dir = dir + camCF.RightVector end
        if FlyNormalKeyHeld.Space then dir = dir + Vector3.new(0, 1, 0) end
        if FlyNormalKeyHeld.Ctrl then dir = dir - Vector3.new(0, 1, 0) end

        local mobileMove = h.MoveDirection
        if mobileMove.Magnitude > 0 then
            dir = dir + mobileMove
        end

        local moveDir = Vector3.new(0, 0, 0)
        if dir.Magnitude > 0 then
            moveDir = dir.Unit * FlyNormalSpeed
        end

        if FlyNormalMode == "Hover" and moveDir.Magnitude == 0 then
            FlyNormalVelocity.Velocity = Vector3.new(0, 0, 0)
        else
            FlyNormalVelocity.Velocity = moveDir
        end

        FlyNormalGyro.CFrame = CFrame.new(r.Position, r.Position + camCF.LookVector)
    end)
end

local function DisableFlyNormal()
    if FlyNormalConnection then FlyNormalConnection:Disconnect(); FlyNormalConnection = nil end
    if FlyNormalVelocity then pcall(function() FlyNormalVelocity:Destroy() end); FlyNormalVelocity = nil end
    if FlyNormalGyro then pcall(function() FlyNormalGyro:Destroy() end); FlyNormalGyro = nil end
end

--==============================================================
-- SERVER HOP
--==============================================================
local function FetchServerList(placeId)
    local servers = {}
    local cursor = ""
    for i = 1, 3 do
        local url = "https://games.roblox.com/v1/games/" .. placeId .. "/servers/Public?sortOrder=Asc&limit=100"
        if cursor ~= "" then url = url .. "&cursor=" .. cursor end
        local ok, result = pcall(function()
            return HttpService:JSONDecode(game:HttpGet(url))
        end)
        if ok and result and result.data then
            for _, srv in ipairs(result.data) do
                if srv.playing and srv.maxPlayers and srv.playing < srv.maxPlayers and srv.id ~= game.JobId then
                    table.insert(servers, srv)
                end
            end
            cursor = result.nextPageCursor or ""
            if cursor == "" then break end
        else
            break
        end
    end
    return #servers > 0, servers
end

local function DoServerHop()
    if ServerHopRunning then return end
    ServerHopRunning = true
    Notify("Server Hop", "> MENCARI...", 3)
    task.spawn(function()
        local ok, servers = FetchServerList(game.PlaceId)
        if not ok then
            Notify("Server Hop", "> GAGAL", 3)
            ServerHopRunning = false
            return
        end
        local target = servers[math.random(1, #servers)]
        pcall(function()
            TeleportService:TeleportToPlaceInstance(game.PlaceId, target.id, LocalPlayer)
        end)
    end)
end

local function DoBestServerHop()
    if ServerHopRunning then return end
    ServerHopRunning = true
    task.spawn(function()
        local ok, servers = FetchServerList(game.PlaceId)
        if not ok then
            Notify("Server Hop", "> TIDAK ADA", 3)
            ServerHopRunning = false
            return
        end
        table.sort(servers, function(a, b) return (a.playing or 0) < (b.playing or 0) end)
        pcall(function()
            TeleportService:TeleportToPlaceInstance(game.PlaceId, servers[1].id, LocalPlayer)
        end)
    end)
end

local function DoRejoin()
    pcall(function()
        TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId, LocalPlayer)
    end)
end

local function DoJoinByJobId(jobId)
    if not jobId or jobId == "" then return end
    pcall(function()
        TeleportService:TeleportToPlaceInstance(game.PlaceId, jobId, LocalPlayer)
    end)
end

--==============================================================
-- TELEPORT KE ORANG
--==============================================================
local function TeleportToPlayer(targetPlayer)
    if not targetPlayer or not targetPlayer.Character then return end
    local tRoot = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
    local lRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not tRoot or not lRoot then return end
    pcall(function()
        lRoot.CFrame = CFrame.new(tRoot.Position + Vector3.new(0, 3, 0))
        Notify("Teleport", "> TO: " .. targetPlayer.Name, 2)
    end)
end

local function RefreshTeleportList()
    if not TeleportListContainer then return end
    for _, item in pairs(TeleportPlayerList) do
        pcall(function() item:Destroy() end)
    end
    TeleportPlayerList = {}
    local y = 0
    local count = 0
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local btn = Instance.new("TextButton")
            btn.Size = UDim2.new(1, 0, 0, 28)
            btn.Position = UDim2.new(0, 0, 0, y)
            btn.BackgroundColor3 = THEME.ButtonBG
            btn.BorderColor3 = THEME.AccentColor
            btn.BorderSizePixel = 1
            btn.Text = "> " .. player.Name
            btn.TextColor3 = THEME.TextColor
            btn.Font = Enum.Font.Code
            btn.TextSize = 11
            btn.TextXAlignment = Enum.TextXAlignment.Left
            btn.ZIndex = 14
            btn.Parent = TeleportListContainer
            Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 3)
            btn.MouseButton1Click:Connect(function() TeleportToPlayer(player) end)
            table.insert(TeleportPlayerList, btn)
            y = y + 32
            count = count + 1
        end
    end
    if count == 0 then
        local empty = Instance.new("TextLabel")
        empty.Size = UDim2.new(1, 0, 0, 28)
        empty.BackgroundTransparency = 1
        empty.Text = "> Tidak ada player lain"
        empty.TextColor3 = THEME.TextLight
        empty.Font = Enum.Font.Code
        empty.TextSize = 10
        empty.ZIndex = 14
        empty.Parent = TeleportListContainer
        table.insert(TeleportPlayerList, empty)
        y = 32
    end
    if TeleportListFrame then
        TeleportListFrame.CanvasSize = UDim2.new(0, 0, 0, y + 10)
    end
end

--==============================================================
-- FULLBRIGHT / FPS / SOUND / MUSIC / RESPAWN / FLING
--==============================================================
local function EnableFullbright()
    OriginalLighting.Ambient = Lighting.Ambient
    OriginalLighting.OutdoorAmbient = Lighting.OutdoorAmbient
    OriginalLighting.Brightness = Lighting.Brightness
    OriginalLighting.ClockTime = Lighting.ClockTime
    OriginalLighting.FogEnd = Lighting.FogEnd
    OriginalLighting.FogStart = Lighting.FogStart
    OriginalLighting.GlobalShadows = Lighting.GlobalShadows
    Lighting.Ambient = Color3.fromRGB(255, 255, 255)
    Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
    Lighting.Brightness = 3
    Lighting.ClockTime = 12
    Lighting.FogEnd = 100000
    Lighting.FogStart = 0
    Lighting.GlobalShadows = false
end

local function DisableFullbright()
    pcall(function()
        if OriginalLighting.Ambient then Lighting.Ambient = OriginalLighting.Ambient end
        if OriginalLighting.OutdoorAmbient then Lighting.OutdoorAmbient = OriginalLighting.OutdoorAmbient end
        if OriginalLighting.Brightness then Lighting.Brightness = OriginalLighting.Brightness end
        if OriginalLighting.ClockTime then Lighting.ClockTime = OriginalLighting.ClockTime end
        if OriginalLighting.FogEnd then Lighting.FogEnd = OriginalLighting.FogEnd end
        if OriginalLighting.FogStart then Lighting.FogStart = OriginalLighting.FogStart end
        if OriginalLighting.GlobalShadows ~= nil then Lighting.GlobalShadows = OriginalLighting.GlobalShadows end
    end)
end

local function EnableFPSBoost()
    OriginalSettings.Shadows = Lighting.GlobalShadows
    OriginalSettings.FogEnd = Lighting.FogEnd
    OriginalSettings.Brightness = Lighting.Brightness
    Lighting.GlobalShadows = false
    Lighting.FogEnd = 100000
    Lighting.Brightness = 2
    for _, effect in pairs(Lighting:GetChildren()) do
        if effect:IsA("PostEffect") then pcall(function() effect.Enabled = false end) end
    end
end

local function DisableFPSBoost()
    pcall(function()
        if OriginalSettings.Shadows ~= nil then Lighting.GlobalShadows = OriginalSettings.Shadows end
        if OriginalSettings.FogEnd ~= nil then Lighting.FogEnd = OriginalSettings.FogEnd end
        if OriginalSettings.Brightness ~= nil then Lighting.Brightness = OriginalSettings.Brightness end
    end)
end

local function StartAntiAFK()
    if AntiAFKConnection then AntiAFKConnection:Disconnect() end
    AntiAFKConnection = RunService.Heartbeat:Connect(function()
        if not AntiAFKEnabled then return end
        pcall(function()
            VirtualUser:CaptureController()
            VirtualUser:ClickButton2(Vector2.new())
        end)
    end)
end

local function StopAntiAFK()
    if AntiAFKConnection then AntiAFKConnection:Disconnect(); AntiAFKConnection = nil end
end

local function ApplyHitbox(player)
    if not player.Character then return end
    local head = player.Character:FindFirstChild("Head")
    local hrp = player.Character:FindFirstChild("HumanoidRootPart")
    if not head or not hrp then return end

    if not head:GetAttribute("OriginalSize") then
        head:SetAttribute("OriginalSize", head.Size)
        hrp:SetAttribute("OriginalSize", hrp.Size)
    end

    if HitboxEnabled then
        head.Size = Vector3.new(HitboxSize, HitboxSize, HitboxSize)
        head.Transparency = 1
        head.CanCollide = false
        hrp.Size = Vector3.new(HitboxSize, HitboxSize, HitboxSize)
        hrp.Transparency = 1
        hrp.CanCollide = false
    else
        head.Size = head:GetAttribute("OriginalSize")
        head.Transparency = 0
        head.CanCollide = true
        hrp.Size = hrp:GetAttribute("OriginalSize")
        hrp.Transparency = 1
    end
end

local function UpdateHitbox()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then pcall(ApplyHitbox, player) end
    end
end

local function StartHitboxExpander()
    if HitboxConnection then HitboxConnection:Disconnect() end
    HitboxConnection = RunService.Heartbeat:Connect(function()
        if HitboxEnabled then UpdateHitbox() end
    end)
end

local function StopHitboxExpander()
    if HitboxConnection then HitboxConnection:Disconnect(); HitboxConnection = nil end
end

local function StartSoundESP()
    if SoundESPBeep then pcall(function() SoundESPBeep:Destroy() end) end
    SoundESPBeep = Instance.new("Sound")
    SoundESPBeep.SoundId = "rbxassetid://4790566870"
    SoundESPBeep.Volume = 1
    SoundESPBeep.Parent = SoundService

    if SoundESPConnection then SoundESPConnection:Disconnect() end
    SoundESPConnection = RunService.Heartbeat:Connect(function()
        if not SoundESPEnabled then return end
        local char = LocalPlayer.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then return end
        local myPos = char.HumanoidRootPart.Position
        local nearestDist = math.huge
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
                local d = (player.Character.HumanoidRootPart.Position - myPos).Magnitude
                if d < nearestDist then nearestDist = d end
            end
        end
        if nearestDist <= SoundESPRadius then
            local now = tick()
            local cooldown = math.clamp(nearestDist / 85, 0.15, 1.2)
            if now - LastBeepTime >= cooldown then
                LastBeepTime = now
                pcall(function()
                    SoundESPBeep.Volume = math.clamp(1 - (nearestDist / SoundESPRadius) + 0.3, 0.3, 1)
                    SoundESPBeep:Play()
                end)
            end
        end
    end)
end

local function StopSoundESP()
    if SoundESPConnection then SoundESPConnection:Disconnect(); SoundESPConnection = nil end
    if SoundESPBeep then pcall(function() SoundESPBeep:Destroy() end); SoundESPBeep = nil end
end

local function PlayMusic()
    if MusicSound then pcall(function() MusicSound:Stop(); MusicSound:Destroy() end); MusicSound = nil end
    local track = MusicPlaylist[MusicCurrentIndex]
    if not track then return end
    MusicLoading = true
    MusicSound = Instance.new("Sound")
    MusicSound.SoundId = "rbxassetid://" .. track.ID
    MusicSound.Volume = MusicVolume
    MusicSound.Looped = false
    MusicSound.Parent = SoundService
    task.spawn(function()
        local waited = 0
        while MusicSound and MusicSound.TimeLength == 0 and waited < 5 do
            task.wait(0.1); waited = waited + 0.1
        end
        if MusicSound and MusicSound.TimeLength > 0 then
            pcall(function() MusicSound:Play() end)
        end
        MusicLoading = false
    end)
end

local function StopMusic()
    if MusicSound then pcall(function() MusicSound:Stop(); MusicSound:Destroy() end); MusicSound = nil end
end

local function NextMusic()
    MusicCurrentIndex = MusicCurrentIndex + 1
    if MusicCurrentIndex > #MusicPlaylist then MusicCurrentIndex = 1 end
    if MusicPlayerEnabled then PlayMusic() end
end

local function PrevMusic()
    MusicCurrentIndex = MusicCurrentIndex - 1
    if MusicCurrentIndex < 1 then MusicCurrentIndex = #MusicPlaylist end
    if MusicPlayerEnabled then PlayMusic() end
end

local function AddMusic(name, id)
    table.insert(MusicPlaylist, {Name = name, ID = id})
    Notify("Music", "> ➕ " .. name, 2)
end

local function SetMusicVolume(v)
    MusicVolume = math.clamp(v, 0, 10)
    if MusicSound then MusicSound.Volume = MusicVolume end
end

task.spawn(function()
    while task.wait(0.5) do
        if MusicPlayerEnabled and MusicSound and not MusicLoading then
            pcall(function()
                if MusicSound.TimeLength > 0 and MusicSound.TimePosition >= MusicSound.TimeLength - 0.5 and not MusicSound.IsPlaying then
                    MusicCurrentIndex = MusicCurrentIndex + 1
                    if MusicCurrentIndex > #MusicPlaylist then MusicCurrentIndex = 1 end
                    PlayMusic()
                end
            end)
        end
    end
end)

local function StartAutoRespawn()
    if AutoRespawnConnection then AutoRespawnConnection:Disconnect() end
    AutoRespawnConnection = RunService.Heartbeat:Connect(function()
        if not AutoRespawnEnabled then return end
        if LocalPlayer.Character then
            local hum = LocalPlayer.Character:FindFirstChild("Humanoid")
            if hum and hum.Health <= 0 then
                pcall(function() LocalPlayer:LoadCharacter() end)
            end
        end
    end)
end

local function StopAutoRespawn()
    if AutoRespawnConnection then AutoRespawnConnection:Disconnect(); AutoRespawnConnection = nil end
end

local function StartAntiFling()
    if AntiFlingConnection then AntiFlingConnection:Disconnect() end
    AntiFlingConnection = RunService.Heartbeat:Connect(function()
        if not AntiFlingEnabled then return end
        if LocalPlayer.Character then
            local root = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            if root and root.AssemblyLinearVelocity.Magnitude > 500 then
                pcall(function() root.AssemblyLinearVelocity = Vector3.new(0, 0, 0) end)
            end
        end
    end)
end

local function StopAntiFling()
    if AntiFlingConnection then AntiFlingConnection:Disconnect(); AntiFlingConnection = nil end
end

local function EnableFlyVoid()
    if FlyVoidConnection then FlyVoidConnection:Disconnect() end
    local char = LocalPlayer.Character
    if not char then return end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end
    FlyVoidOriginalY = root.Position.Y
    local rayParams = RaycastParams.new()
    rayParams.FilterType = Enum.RaycastFilterType.Blacklist
    rayParams.FilterDescendantsInstances = {char}
    rayParams.IgnoreWater = true
    local ray = workspace:Raycast(Vector3.new(root.Position.X, 1000, root.Position.Z), Vector3.new(0, -3000, 0), rayParams)
    local floorY = ray and ray.Position.Y or -60
    local targetY = floorY - 5
    pcall(function()
        root.CFrame = CFrame.new(root.Position.X, targetY, root.Position.Z)
    end)
    FlyVoidConnection = RunService.Heartbeat:Connect(function()
        if not FlyVoidEnabled then return end
        local c = LocalPlayer.Character
        if not c then return end
        local r = c:FindFirstChild("HumanoidRootPart")
        local h = c:FindFirstChild("Humanoid")
        if not r or not h then return end
        local pos = r.Position
        if pos.Y > targetY + 2 then
            pcall(function()
                r.CFrame = CFrame.new(pos.X, targetY, pos.Z)
                r.AssemblyLinearVelocity = Vector3.new(r.AssemblyLinearVelocity.X, 0, r.AssemblyLinearVelocity.Z)
            end)
        end
        if pos.Y < targetY - 15 then
            pcall(function()
                r.CFrame = CFrame.new(pos.X, targetY, pos.Z)
                r.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            end)
        end
        if math.abs(r.AssemblyLinearVelocity.Y) > 5 then
            pcall(function()
                r.AssemblyLinearVelocity = Vector3.new(r.AssemblyLinearVelocity.X, 0, r.AssemblyLinearVelocity.Z)
            end)
        end
        if h.Health <= 0 then pcall(function() LocalPlayer:LoadCharacter() end) end
        if FlyVoidHideMode then
            for _, part in pairs(c:GetDescendants()) do
                if part:IsA("BasePart") then
                    if not OriginalTransparency[part] then
                        OriginalTransparency[part] = part.Transparency
                        OriginalCanCollide[part] = part.CanCollide
                    end
                    part.Transparency = 0.85
                    part.CanCollide = false
                end
            end
        end
    end)
end

local function DisableFlyVoid()
    if FlyVoidConnection then FlyVoidConnection:Disconnect(); FlyVoidConnection = nil end
    local char = LocalPlayer.Character
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                if OriginalTransparency[part] then part.Transparency = OriginalTransparency[part] end
                if OriginalCanCollide[part] ~= nil then part.CanCollide = OriginalCanCollide[part] end
            end
        end
        local root = char:FindFirstChild("HumanoidRootPart")
        if root then
            pcall(function()
                local rayParams = RaycastParams.new()
                rayParams.FilterType = Enum.RaycastFilterType.Blacklist
                rayParams.FilterDescendantsInstances = {char}
                local ray = workspace:Raycast(Vector3.new(root.Position.X, 1000, root.Position.Z), Vector3.new(0, -3000, 0), rayParams)
                if ray then
                    root.CFrame = CFrame.new(ray.Position.X, ray.Position.Y + 5, ray.Position.Z)
                elseif FlyVoidOriginalY then
                    root.CFrame = CFrame.new(root.Position.X, FlyVoidOriginalY, root.Position.Z)
                end
            end)
        end
    end
    OriginalTransparency = {}
    OriginalCanCollide = {}
    FlyVoidOriginalY = nil
end

local function EnableNoclip()
    if NoclipConnection then NoclipConnection:Disconnect() end
    NoclipConnection = RunService.Stepped:Connect(function()
        if NoclipEnabled and LocalPlayer.Character then
            for _, p in pairs(LocalPlayer.Character:GetDescendants()) do
                if p:IsA("BasePart") then p.CanCollide = false end
            end
        end
    end)
end

local function DisableNoclip()
    if NoclipConnection then NoclipConnection:Disconnect(); NoclipConnection = nil end
end

local function EnableInfiniteJump()
    if JumpConnection then JumpConnection:Disconnect() end
    JumpConnection = UserInputService.JumpRequest:Connect(function()
        if InfiniteJumpEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            LocalPlayer.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end)
end

local function DisableInfiniteJump()
    if JumpConnection then JumpConnection:Disconnect(); JumpConnection = nil end
end

local function ApplySpeedHack()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = SpeedHackEnabled and SpeedMultiplier or DefaultWalkSpeed
    end
end

local function SendChatMessage(message)
    pcall(function()
        local chatEvents = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents")
        if chatEvents then
            local sayReq = chatEvents:FindFirstChild("SayMessageRequest")
            if sayReq then sayReq:FireServer(message, "All"); return end
        end
    end)
end

local function StartChatSpamLoop()
    task.spawn(function()
        while ChatSpamEnabled do
            task.wait(ChatSpamDelay)
            if ChatSpamEnabled then SendChatMessage(ChatSpamText) end
        end
    end)
end

local function TeleportToMouse()
    if not LocalPlayer.Character then return end
    local r = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not r then return end
    local hit = Mouse.Hit
    if hit then
        pcall(function()
            r.CFrame = CFrame.new(hit.Position + Vector3.new(0, 3, 0))
            Notify("Teleport", "> TO MOUSE", 2)
        end)
    end
end

local function SaveLocation()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        SavedLocation = LocalPlayer.Character.HumanoidRootPart.CFrame
        Notify("Save", "> LOCATION SAVED", 2)
    end
end

local function LoadLocation()
    if SavedLocation and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        pcall(function()
            LocalPlayer.Character.HumanoidRootPart.CFrame = SavedLocation
            Notify("Save", "> TELEPORTED", 2)
        end)
    else
        Notify("Save", "> NO SAVED LOCATION", 2)
    end
end

--==============================================================
-- ESP
--==============================================================
local function HSVToRGB(h, s, v)
    local r, g, b
    local i = math.floor(h * 6)
    local f = h * 6 - i
    local p = v * (1 - s)
    local q = v * (1 - f * s)
    local t = v * (1 - (1 - f) * s)
    i = i % 6
    if i == 0 then r, g, b = v, t, p
    elseif i == 1 then r, g, b = q, v, p
    elseif i == 2 then r, g, b = p, v, t
    elseif i == 3 then r, g, b = p, q, v
    elseif i == 4 then r, g, b = t, p, v
    elseif i == 5 then r, g, b = v, p, q
    end
    return Color3.new(r, g, b)
end

local function EnableRainbowESP()
    if RainbowConnection then RainbowConnection:Disconnect() end
    RainbowConnection = RunService.RenderStepped:Connect(function(dt)
        if not RainbowESPEnabled then return end
        RainbowHue = (RainbowHue + dt * 0.3) % 1
        local c = HSVToRGB(RainbowHue, 1, 1)
        for _, d in pairs(ESPObjects) do
            if d.Box then d.Box.Color = c end
            if d.Name then d.Name.Color = c end
            if d.Distance then d.Distance.Color = c end
            if d.Tracer then d.Tracer.Color = c end
            if d.HealthBar then d.HealthBar.Color = c end
        end
    end)
end

local function DisableRainbowESP()
    if RainbowConnection then RainbowConnection:Disconnect(); RainbowConnection = nil end
end

local function CreateESP(player)
    if ESPObjects[player] then return end
    local d = {}
    d.Box = Drawing.new("Square"); d.Box.Visible = false; d.Box.Color = THEME.AccentColor; d.Box.Thickness = 2; d.Box.Filled = false; d.Box.Transparency = 1
    d.Name = Drawing.new("Text"); d.Name.Visible = false; d.Name.Color = THEME.AccentColor; d.Name.Size = 12; d.Name.Center = true; d.Name.Outline = true; d.Name.OutlineColor = Color3.fromRGB(0,0,0)
    d.Distance = Drawing.new("Text"); d.Distance.Visible = false; d.Distance.Color = THEME.AccentColor; d.Distance.Size = 10; d.Distance.Center = true; d.Distance.Outline = true; d.Distance.OutlineColor = Color3.fromRGB(0,0,0)
    d.HealthBg = Drawing.new("Line"); d.HealthBg.Visible = false; d.HealthBg.Color = Color3.fromRGB(80,0,0); d.HealthBg.Thickness = 3
    d.HealthBar = Drawing.new("Line"); d.HealthBar.Visible = false; d.HealthBar.Color = THEME.AccentColor; d.HealthBar.Thickness = 3
    d.Tracer = Drawing.new("Line"); d.Tracer.Visible = false; d.Tracer.Color = TracerColor; d.Tracer.Thickness = 2; d.Tracer.Transparency = 0.5
    ESPObjects[player] = d
end

local function RemoveESP(player)
    if ESPObjects[player] then
        local d = ESPObjects[player]
        for _, v in pairs(d) do pcall(function() v:Remove() end) end
        ESPObjects[player] = nil
    end
    if ChamsObjects[player] then
        for _, h in pairs(ChamsObjects[player]) do pcall(function() if h then h:Destroy() end end) end
        ChamsObjects[player] = nil
    end
end

local function CreateChams(player)
    if ChamsObjects[player] or not player.Character then return end
    ChamsObjects[player] = {}
    for _, part in pairs(player.Character:GetChildren()) do
        if part:IsA("BasePart") or part:IsA("MeshPart") then
            local h = Instance.new("Highlight")
            h.Adornee = part
            h.FillColor = THEME.AccentColor
            h.FillTransparency = 0.7
            h.OutlineColor = THEME.AccentLight
            h.OutlineTransparency = 0
            h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            h.Parent = part
            table.insert(ChamsObjects[player], h)
        end
    end
end

local function RemoveChams(player)
    if ChamsObjects[player] then
        for _, h in pairs(ChamsObjects[player]) do pcall(function() if h then h:Destroy() end end) end
        ChamsObjects[player] = nil
    end
end

local function CreateNPCEsp(model)
    if NPCESPObjects[model] then return end
    local d = {}
    d.Box = Drawing.new("Square"); d.Box.Visible = false; d.Box.Color = THEME.YellowDot; d.Box.Thickness = 2; d.Box.Filled = false; d.Box.Transparency = 1
    d.Name = Drawing.new("Text"); d.Name.Visible = false; d.Name.Color = THEME.YellowDot; d.Name.Size = 11; d.Name.Center = true; d.Name.Outline = true; d.Name.OutlineColor = Color3.fromRGB(0,0,0)
    d.Distance = Drawing.new("Text"); d.Distance.Visible = false; d.Distance.Color = THEME.YellowDot; d.Distance.Size = 9; d.Distance.Center = true; d.Distance.Outline = true; d.Distance.OutlineColor = Color3.fromRGB(0,0,0)
    NPCESPObjects[model] = d
end

local function RemoveNPCEsp(model)
    if NPCESPObjects[model] then
        local d = NPCESPObjects[model]
        for _, v in pairs(d) do pcall(function() v:Remove() end) end
        NPCESPObjects[model] = nil
    end
end

local function UpdateNPCEsp()
    if not NPCEspEnabled then
        for model, _ in pairs(NPCESPObjects) do RemoveNPCEsp(model) end
        return
    end
    local npcs = GetNPCs()
    local seen = {}
    for _, model in ipairs(npcs) do
        seen[model] = true
        local root = model:FindFirstChild("HumanoidRootPart")
        local hum = model:FindFirstChild("Humanoid")
        if root and hum and hum.Health > 0 then
            if not NPCESPObjects[model] then CreateNPCEsp(model) end
            local d = NPCESPObjects[model]
            local sp, on = Camera:WorldToViewportPoint(root.Position)
            if on then
                local dist = (root.Position - Camera.CFrame.Position).Magnitude
                local bSize = Vector2.new(2000/dist, 3500/dist)
                local bX, bY = sp.X - bSize.X/2, sp.Y - bSize.Y/2
                d.Box.Visible = true
                d.Box.Position = Vector2.new(bX, bY)
                d.Box.Size = bSize
                d.Name.Visible = true
                d.Name.Text = "[NPC] " .. model.Name
                d.Name.Position = Vector2.new(sp.X, bY-15)
                d.Distance.Visible = true
                d.Distance.Text = math.floor(dist).."m"
                d.Distance.Position = Vector2.new(sp.X, bY+bSize.Y+5)
            else
                for _, v in pairs(d) do pcall(function() v.Visible = false end) end
            end
        else
            RemoveNPCEsp(model)
        end
    end
    for model, _ in pairs(NPCESPObjects) do
        if not seen[model] then RemoveNPCEsp(model) end
    end
end

local function UpdateESP()
    if not ESPEnabled then
        for _, d in pairs(ESPObjects) do
            for _, v in pairs(d) do pcall(function() v.Visible = false end) end
        end
        return
    end
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("Humanoid") then
            local hum = player.Character.Humanoid
            local root = player.Character.HumanoidRootPart
            if hum.Health > 0 and root then
                if not ESPObjects[player] then CreateESP(player) end
                local d = ESPObjects[player]
                local sp, on = Camera:WorldToViewportPoint(root.Position)
                if on then
                    local dist = (root.Position - Camera.CFrame.Position).Magnitude
                    local bSize = Vector2.new(2000/dist, 3500/dist)
                    local bX, bY = sp.X - bSize.X/2, sp.Y - bSize.Y/2
                    d.Box.Visible = true
                    d.Box.Position = Vector2.new(bX, bY)
                    d.Box.Size = bSize
                    d.Name.Visible = true
                    d.Name.Text = player.Name
                    d.Name.Position = Vector2.new(sp.X, bY-15)
                    d.Distance.Visible = true
                    d.Distance.Text = math.floor(dist).."m"
                    d.Distance.Position = Vector2.new(sp.X, bY+bSize.Y+5)
                    local hp = hum.Health / hum.MaxHealth
                    d.HealthBg.Visible = true
                    d.HealthBg.From = Vector2.new(bX, bY+bSize.Y+20)
                    d.HealthBg.To = Vector2.new(bX+bSize.X, bY+bSize.Y+20)
                    d.HealthBar.Visible = true
                    d.HealthBar.From = Vector2.new(bX, bY+bSize.Y+20)
                    d.HealthBar.To = Vector2.new(bX+bSize.X*hp, bY+bSize.Y+20)
                    d.Tracer.Visible = true
                    if not RainbowESPEnabled then d.Tracer.Color = TracerColor end
                    d.Tracer.From = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y)
                    d.Tracer.To = Vector2.new(sp.X, sp.Y)
                    if not ChamsObjects[player] then CreateChams(player) end
                else
                    for _, v in pairs(d) do pcall(function() v.Visible = false end) end
                end
            else
                RemoveESP(player)
            end
        else
            RemoveESP(player)
        end
    end
end

--==============================================================
-- AIMBOT
--==============================================================
local function IsSameTeam(player)
    if not AimbotTeamCheck then return false end
    if LocalPlayer.Team and player.Team and LocalPlayer.Team == player.Team then return true end
    return false
end

local function HasWallBetween(camPos, targetPos, targetChar)
    if not AimbotWallCheck then return false end
    local dir = targetPos - camPos
    local dist = dir.Magnitude
    if dist < 1 then return false end
    local rp = RaycastParams.new()
    rp.FilterType = Enum.RaycastFilterType.Blacklist
    rp.FilterDescendantsInstances = {LocalPlayer.Character, targetChar}
    rp.IgnoreWater = true
    local result = workspace:Raycast(camPos, dir.Unit * dist, rp)
    return result ~= nil
end

local function FindTarget()
    local bestTarget = nil
    local bestDist = AimbotFOV
    local bestType = nil
    local screenCenter = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
    local camPos = Camera.CFrame.Position
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local hum = player.Character:FindFirstChild("Humanoid")
            local root = player.Character:FindFirstChild("HumanoidRootPart")
            if hum and root and hum.Health > 0 and not IsSameTeam(player) then
                local part = player.Character:FindFirstChild(AimbotTargetPart) or root
                if part then
                    local blocked = HasWallBetween(camPos, part.Position, player.Character)
                    if not blocked then
                        local sp, on = Camera:WorldToViewportPoint(part.Position)
                        if on then
                            local d = (Vector2.new(sp.X, sp.Y) - screenCenter).Magnitude
                            if d < bestDist then
                                bestTarget = player; bestDist = d; bestType = "player"
                            end
                        end
                    end
                end
            end
        end
    end
    if NPCDetectionEnabled then
        for _, model in ipairs(GetNPCs()) do
            local hum = model:FindFirstChild("Humanoid")
            local root = model:FindFirstChild("HumanoidRootPart")
            if hum and root and hum.Health > 0 then
                local part = model:FindFirstChild(AimbotTargetPart) or root
                if part then
                    local blocked = HasWallBetween(camPos, part.Position, model)
                    if not blocked then
                        local sp, on = Camera:WorldToViewportPoint(part.Position)
                        if on then
                            local d = (Vector2.new(sp.X, sp.Y) - screenCenter).Magnitude
                            if d < bestDist then
                                bestTarget = model; bestDist = d; bestType = "npc"
                            end
                        end
                    end
                end
            end
        end
    end
    return bestTarget, bestType
end

local function IsTargetValid(target, targetType)
    if not target or not target.Parent then return false end
    if targetType == "player" then
        local char = target.Character
        if not char then return false end
        local hum = char:FindFirstChild("Humanoid")
        return hum and hum.Health > 0
    elseif targetType == "npc" then
        local hum = target:FindFirstChild("Humanoid")
        return hum and hum.Health > 0
    end
    return false
end

local function GetTargetPart(target, targetType)
    if targetType == "player" then
        if not target.Character then return nil end
        return target.Character:FindFirstChild(AimbotTargetPart) or target.Character:FindFirstChild("HumanoidRootPart")
    elseif targetType == "npc" then
        return target:FindFirstChild(AimbotTargetPart) or target:FindFirstChild("HumanoidRootPart")
    end
    return nil
end

local function RunAimbot()
    if not AimbotEnabled then return end
    if AimbotKeybindEnabled and not AimbotKeyHeld then return end
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    if AimbotStickyTarget and IsTargetValid(AimbotStickyTarget, AimbotStickyType) then
        if AimbotWallCheck then
            local tPart = GetTargetPart(AimbotStickyTarget, AimbotStickyType)
            local tChar = AimbotStickyType == "player" and AimbotStickyTarget.Character or AimbotStickyTarget
            if tPart and HasWallBetween(Camera.CFrame.Position, tPart.Position, tChar) then
                AimbotStickyTarget = nil; AimbotStickyType = nil
            end
        end
    else
        AimbotStickyTarget = nil; AimbotStickyType = nil
    end
    if not AimbotStickyTarget then
        local t, tType = FindTarget()
        AimbotStickyTarget = t; AimbotStickyType = tType
    end
    if not AimbotStickyTarget then return end
    if not IsTargetValid(AimbotStickyTarget, AimbotStickyType) then
        AimbotStickyTarget = nil; AimbotStickyType = nil; return
    end
    local targetPart = GetTargetPart(AimbotStickyTarget, AimbotStickyType)
    if not targetPart then return end
    local camPos = Camera.CFrame.Position
    local dir = (targetPart.Position - camPos).Unit
    Camera.CFrame = CFrame.new(camPos, camPos + dir)
end

local function LookAtPlayer()
    if not LookAtEnabled then return end
    local target, tType = FindTarget()
    if target then
        local tPart = GetTargetPart(target, tType)
        if tPart then
            local camPos = Camera.CFrame.Position
            pcall(function() Camera.CFrame = CFrame.new(camPos, camPos + (tPart.Position - camPos).Unit) end)
        end
    end
end

--==============================================================
-- RADAR
--==============================================================
local function CreateRadarUI()
    RadarFrame = Instance.new("Frame")
    RadarFrame.Name = "RadarFrame"
    RadarFrame.Size = UDim2.new(0, 230, 0, 280)
    RadarFrame.Position = UDim2.new(1, -250, 0, 70)
    RadarFrame.BackgroundColor3 = THEME.PanelBG
    RadarFrame.BackgroundTransparency = 0.2
    RadarFrame.BorderColor3 = THEME.AccentColor
    RadarFrame.BorderSizePixel = 2
    RadarFrame.ZIndex = 20
    RadarFrame.Visible = false
    RadarFrame.Parent = ScreenGui
    Instance.new("UICorner", RadarFrame).CornerRadius = UDim.new(0, 10)

    local RTitle = Instance.new("TextLabel")
    RTitle.Size = UDim2.new(1, 0, 0, 22)
    RTitle.BackgroundColor3 = THEME.SectionBG
    RTitle.BorderSizePixel = 0
    RTitle.Text = "📡 RADAR"
    RTitle.TextColor3 = THEME.TextColor
    RTitle.Font = Enum.Font.Code
    RTitle.TextSize = 12
    RTitle.ZIndex = 21
    RTitle.Parent = RadarFrame
    Instance.new("UICorner", RTitle).CornerRadius = UDim.new(0, 10)

    RadarNearestLabel = Instance.new("TextLabel")
    RadarNearestLabel.Size = UDim2.new(1, -10, 0, 18)
    RadarNearestLabel.Position = UDim2.new(0, 5, 0, 25)
    RadarNearestLabel.BackgroundTransparency = 1
    RadarNearestLabel.Text = "> Nearest: --"
    RadarNearestLabel.TextColor3 = THEME.TextColor
    RadarNearestLabel.Font = Enum.Font.Code
    RadarNearestLabel.TextSize = 10
    RadarNearestLabel.ZIndex = 21
    RadarNearestLabel.Parent = RadarFrame

    local RInfo = Instance.new("TextLabel")
    RInfo.Size = UDim2.new(1, -10, 0, 16)
    RInfo.Position = UDim2.new(0, 5, 0, 258)
    RInfo.BackgroundTransparency = 1
    RInfo.Text = "🔴 Musuh 🟡 NPC 🔵 Kamu"
    RInfo.TextColor3 = THEME.TextLight
    RInfo.Font = Enum.Font.Code
    RInfo.TextSize = 9
    RInfo.ZIndex = 21
    RInfo.Parent = RadarFrame

    local dragging = false
    local dragStart, startPos
    RTitle.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = RadarFrame.Position
        end
    end)
    RTitle.InputChanged:Connect(function(input)
        if (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) and dragging then
            local delta = input.Position - dragStart
            RadarFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
end

local function StartRadar()
    if RadarConnection then RadarConnection:Disconnect() end
    RadarConnection = RunService.RenderStepped:Connect(function()
        if not RadarEnabled then return end
        if not RadarFrame or not RadarFrame.Visible then return end
        if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then return end
        local myPos = LocalPlayer.Character.HumanoidRootPart.Position
        local radarCenterScreen = RadarFrame.AbsolutePosition + Vector2.new(RadarFrame.AbsoluteSize.X / 2, 140)
        local r = 90
        if RadarDrawingBorder then pcall(function() RadarDrawingBorder:Remove() end); RadarDrawingBorder = nil end
        RadarDrawingBorder = Drawing.new("Circle")
        RadarDrawingBorder.Visible = true
        RadarDrawingBorder.Position = radarCenterScreen
        RadarDrawingBorder.Radius = r
        RadarDrawingBorder.Thickness = 2
        RadarDrawingBorder.Color = THEME.AccentColor
        RadarDrawingBorder.Filled = false
        RadarDrawingBorder.Transparency = 1
        RadarDrawingBorder.NumSides = 60
        if RadarDrawingBg then pcall(function() RadarDrawingBg:Remove() end); RadarDrawingBg = nil end
        RadarDrawingBg = Drawing.new("Circle")
        RadarDrawingBg.Visible = true
        RadarDrawingBg.Position = radarCenterScreen
        RadarDrawingBg.Radius = r
        RadarDrawingBg.Thickness = 1
        RadarDrawingBg.Color = Color3.fromRGB(30, 0, 0)
        RadarDrawingBg.Filled = true
        RadarDrawingBg.Transparency = 0.8
        RadarDrawingBg.NumSides = 60
        if RadarDrawingCenterDot then pcall(function() RadarDrawingCenterDot:Remove() end); RadarDrawingCenterDot = nil end
        RadarDrawingCenterDot = Drawing.new("Circle")
        RadarDrawingCenterDot.Visible = true
        RadarDrawingCenterDot.Position = radarCenterScreen
        RadarDrawingCenterDot.Radius = 4
        RadarDrawingCenterDot.Thickness = 1
        RadarDrawingCenterDot.Color = THEME.BlueDot
        RadarDrawingCenterDot.Filled = true
        RadarDrawingCenterDot.Transparency = 1
        for _, c in pairs(RadarDrawingCompass) do pcall(function() c:Remove() end) end
        RadarDrawingCompass = {}
        local compassLetters = {"N", "S", "E", "W"}
        local compassAngles = {0, 180, 90, -90}
        for i, letter in ipairs(compassLetters) do
            local ang = math.rad(compassAngles[i])
            local nx = math.sin(ang) * (r + 8)
            local ny = -math.cos(ang) * (r + 8)
            local txt = Drawing.new("Text")
            txt.Visible = true
            txt.Position = radarCenterScreen + Vector2.new(nx, ny)
            txt.Text = letter
            txt.Size = 10
            txt.Color = THEME.TextColor
            txt.Center = true
            txt.Outline = true
            txt.OutlineColor = Color3.fromRGB(0, 0, 0)
            table.insert(RadarDrawingCompass, txt)
        end
        local camCF = Camera.CFrame
        local camYaw = math.atan2(camCF.LookVector.X, camCF.LookVector.Z)
        for _, d in pairs(RadarObjects) do pcall(function() d.dot:Remove() end) end
        RadarObjects = {}
        local nearestDist = math.huge
        local nearestName = "--"
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("Humanoid") then
                local hum = player.Character.Humanoid
                if hum.Health > 0 then
                    local tPos = player.Character.HumanoidRootPart.Position
                    local delta = tPos - myPos
                    local dist = Vector3.new(delta.X, 0, delta.Z).Magnitude
                    if dist <= RadarRadius then
                        local worldAng = math.atan2(delta.X, delta.Z)
                        local relAng = worldAng - camYaw
                        if not RadarRotateWithCamera then relAng = worldAng end
                        local px = math.sin(relAng) * (dist / RadarRadius) * r * RadarZoom
                        local py = -math.cos(relAng) * (dist / RadarRadius) * r * RadarZoom
                        local screenPos = radarCenterScreen + Vector2.new(px, py)
                        local isTeam = LocalPlayer.Team and player.Team and LocalPlayer.Team == player.Team
                        local dot = Drawing.new("Circle")
                        dot.Visible = true
                        dot.Position = screenPos
                        dot.Radius = 3
                        dot.Thickness = 1
                        dot.Filled = true
                        dot.Transparency = 1
                        dot.Color = isTeam and THEME.GreenDot or THEME.RedDot
                        table.insert(RadarObjects, {dot = dot})
                        if dist < nearestDist then
                            nearestDist = dist; nearestName = player.Name
                        end
                    end
                end
            end
        end
        if NPCDetectionEnabled then
            for _, model in ipairs(GetNPCs()) do
                local root = model:FindFirstChild("HumanoidRootPart")
                if root then
                    local delta = root.Position - myPos
                    local dist = Vector3.new(delta.X, 0, delta.Z).Magnitude
                    if dist <= RadarRadius then
                        local worldAng = math.atan2(delta.X, delta.Z)
                        local relAng = worldAng - camYaw
                        if not RadarRotateWithCamera then relAng = worldAng end
                        local px = math.sin(relAng) * (dist / RadarRadius) * r * RadarZoom
                        local py = -math.cos(relAng) * (dist / RadarRadius) * r * RadarZoom
                        local screenPos = radarCenterScreen + Vector2.new(px, py)
                        local dot = Drawing.new("Circle")
                        dot.Visible = true
                        dot.Position = screenPos
                        dot.Radius = 3
                        dot.Thickness = 1
                        dot.Filled = true
                        dot.Transparency = 1
                        dot.Color = THEME.YellowDot
                        table.insert(RadarObjects, {dot = dot})
                    end
                end
            end
        end
        if RadarNearestLabel then
            if nearestDist < math.huge then
                RadarNearestLabel.Text = "> Nearest: " .. math.floor(nearestDist) .. "m (" .. nearestName .. ")"
            else
                RadarNearestLabel.Text = "> Nearest: --"
            end
        end
    end)
end

local function StopRadar()
    if RadarConnection then RadarConnection:Disconnect(); RadarConnection = nil end
    for _, d in pairs(RadarObjects) do pcall(function() d.dot:Remove() end) end
    RadarObjects = {}
    if RadarDrawingBg then pcall(function() RadarDrawingBg:Remove() end); RadarDrawingBg = nil end
    if RadarDrawingBorder then pcall(function() RadarDrawingBorder:Remove() end); RadarDrawingBorder = nil end
    if RadarDrawingCenterDot then pcall(function() RadarDrawingCenterDot:Remove() end); RadarDrawingCenterDot = nil end
    for _, c in pairs(RadarDrawingCompass) do pcall(function() c:Remove() end) end
    RadarDrawingCompass = {}
end

--==============================================================
-- MAIN LOOP
--==============================================================
RunService.RenderStepped:Connect(function()
    pcall(function()
        UpdateESP()
        UpdateNPCEsp()
        if AimbotEnabled then RunAimbot() end
        if SpeedHackEnabled then ApplySpeedHack() end
        if LookAtEnabled then LookAtPlayer() end
        if FOVCircleEnabled then
            FOVCircle.Position = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
            FOVCircle.Radius = AimbotFOV
            FOVCircle.Visible = true
        else
            FOVCircle.Visible = false
        end
    end)
end)

UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if AimbotKeybindEnabled and input.KeyCode == AimbotKeybind then AimbotKeyHeld = true end
    if input.KeyCode == FlyVoidKeybind then
        FlyVoidEnabled = not FlyVoidEnabled
        if FlyVoidEnabled then EnableFlyVoid(); Notify("Fly-Void", "> ON", 2) else DisableFlyVoid(); Notify("Fly-Void", "> OFF", 2) end
    end
    if input.KeyCode == FlyNormalKeybind then
        FlyNormalEnabled = not FlyNormalEnabled
        if FlyNormalEnabled then EnableFlyNormal(); Notify("Fly", "> ON", 2) else DisableFlyNormal(); Notify("Fly", "> OFF", 2) end
    end
    if input.KeyCode == Enum.KeyCode.W then FlyNormalKeyHeld.W = true end
    if input.KeyCode == Enum.KeyCode.A then FlyNormalKeyHeld.A = true end
    if input.KeyCode == Enum.KeyCode.S then FlyNormalKeyHeld.S = true end
    if input.KeyCode == Enum.KeyCode.D then FlyNormalKeyHeld.D = true end
    if input.KeyCode == Enum.KeyCode.Space then FlyNormalKeyHeld.Space = true end
    if input.KeyCode == Enum.KeyCode.LeftControl then FlyNormalKeyHeld.Ctrl = true end
end)

UserInputService.InputEnded:Connect(function(input, gp)
    if input.KeyCode == AimbotKeybind then AimbotKeyHeld = false end
    if input.KeyCode == Enum.KeyCode.W then FlyNormalKeyHeld.W = false end
    if input.KeyCode == Enum.KeyCode.A then FlyNormalKeyHeld.A = false end
    if input.KeyCode == Enum.KeyCode.S then FlyNormalKeyHeld.S = false end
    if input.KeyCode == Enum.KeyCode.D then FlyNormalKeyHeld.D = false end
    if input.KeyCode == Enum.KeyCode.Space then FlyNormalKeyHeld.Space = false end
    if input.KeyCode == Enum.KeyCode.LeftControl then FlyNormalKeyHeld.Ctrl = false end
end)

--==============================================================
-- UI BUILDER
--==============================================================
local function CreateUI()
    -- LOADING SCREEN
    local LoadingScreen = Instance.new("Frame")
    LoadingScreen.Size = UDim2.new(1, 0, 1, 0)
    LoadingScreen.BackgroundColor3 = Color3.fromRGB(5, 0, 0)
    LoadingScreen.BorderSizePixel = 0
    LoadingScreen.ZIndex = 300
    LoadingScreen.Visible = true
    LoadingScreen.Parent = ScreenGui

    local LoadingBg = Instance.new("Frame")
    LoadingBg.Size = UDim2.new(0, 320, 0, 220)
    LoadingBg.Position = UDim2.new(0.5, -160, 0.5, -110)
    LoadingBg.BackgroundColor3 = THEME.MainBG
    LoadingBg.BorderColor3 = THEME.AccentColor
    LoadingBg.BorderSizePixel = 2
    LoadingBg.ZIndex = 301
    LoadingBg.Parent = LoadingScreen
    Instance.new("UICorner", LoadingBg).CornerRadius = UDim.new(0, 15)

    local LTitle = Instance.new("TextLabel")
    LTitle.Size = UDim2.new(1, -30, 0, 40)
    LTitle.Position = UDim2.new(0, 15, 0, 15)
    LTitle.BackgroundTransparency = 1
    LTitle.Text = "ZETGAMES-AIMLOCK-ADVANSERVER"
    LTitle.TextColor3 = THEME.TextColor
    LTitle.Font = Enum.Font.Code
    LTitle.TextSize = 14
    LTitle.ZIndex = 302
    LTitle.Parent = LoadingBg

    local LTag = Instance.new("TextLabel")
    LTag.Size = UDim2.new(1, -30, 0, 20)
    LTag.Position = UDim2.new(0, 15, 0, 50)
    LTag.BackgroundTransparency = 1
    LTag.Text = "[ V4.3 TESTING - VPN V2 ]"
    LTag.TextColor3 = THEME.ShieldColor
    LTag.Font = Enum.Font.Code
    LTag.TextSize = 10
    LTag.ZIndex = 302
    LTag.Parent = LoadingBg

    local LBarBg = Instance.new("Frame")
    LBarBg.Size = UDim2.new(1, -30, 0, 15)
    LBarBg.Position = UDim2.new(0, 15, 0, 110)
    LBarBg.BackgroundColor3 = Color3.fromRGB(30, 10, 10)
    LBarBg.BorderColor3 = THEME.AccentColor
    LBarBg.BorderSizePixel = 1
    LBarBg.ZIndex = 302
    LBarBg.Parent = LoadingBg
    Instance.new("UICorner", LBarBg).CornerRadius = UDim.new(0, 7)

    local LBarFill = Instance.new("Frame")
    LBarFill.Size = UDim2.new(0, 0, 1, 0)
    LBarFill.BackgroundColor3 = THEME.AccentColor
    LBarFill.BorderSizePixel = 0
    LBarFill.ZIndex = 303
    LBarFill.Parent = LBarBg
    Instance.new("UICorner", LBarFill).CornerRadius = UDim.new(0, 7)

    local LPercent = Instance.new("TextLabel")
    LPercent.Size = UDim2.new(1, -30, 0, 20)
    LPercent.Position = UDim2.new(0, 15, 0, 135)
    LPercent.BackgroundTransparency = 1
    LPercent.Text = "0%"
    LPercent.TextColor3 = THEME.TextColor
    LPercent.Font = Enum.Font.Code
    LPercent.TextSize = 13
    LPercent.ZIndex = 302
    LPercent.Parent = LoadingBg

    local LStatus = Instance.new("TextLabel")
    LStatus.Size = UDim2.new(1, -30, 0, 20)
    LStatus.Position = UDim2.new(0, 15, 0, 165)
    LStatus.BackgroundTransparency = 1
    LStatus.Text = "> LOADING..."
    LStatus.TextColor3 = THEME.TextLight
    LStatus.Font = Enum.Font.Code
    LStatus.TextSize = 9
    LStatus.TextXAlignment = Enum.TextXAlignment.Left
    LStatus.ZIndex = 302
    LStatus.Parent = LoadingBg

    local LFooterLbl = Instance.new("TextLabel")
    LFooterLbl.Size = UDim2.new(1, -30, 0, 20)
    LFooterLbl.Position = UDim2.new(0, 15, 0, 185)
    LFooterLbl.BackgroundTransparency = 1
    LFooterLbl.Text = "> VPN V2 + NPC + FLY + THEME"
    LFooterLbl.TextColor3 = THEME.ShieldColor
    LFooterLbl.Font = Enum.Font.Code
    LFooterLbl.TextSize = 8
    LFooterLbl.ZIndex = 302
    LFooterLbl.Parent = LoadingBg

    -- MAIN HUB
    local MainHub = Instance.new("Frame")
    MainHub.Size = UDim2.new(0, 340, 0, 500)
    MainHub.Position = UDim2.new(0.5, -170, 0.5, -250)
    MainHub.BackgroundColor3 = THEME.MainBG
    MainHub.BorderColor3 = THEME.AccentColor
    MainHub.BorderSizePixel = 2
    MainHub.Visible = false
    MainHub.ZIndex = 10
    MainHub.Parent = ScreenGui
    Instance.new("UICorner", MainHub).CornerRadius = UDim.new(0, 10)

    local TitleBar = Instance.new("Frame")
    TitleBar.Size = UDim2.new(1, 0, 0, 40)
    TitleBar.BackgroundColor3 = THEME.SectionBG
    TitleBar.BorderSizePixel = 0
    TitleBar.ZIndex = 11
    TitleBar.Parent = MainHub
    Instance.new("UICorner", TitleBar).CornerRadius = UDim.new(0, 10)

    local MainTitle2 = Instance.new("TextLabel")
    MainTitle2.Size = UDim2.new(1, -50, 1, 0)
    MainTitle2.Position = UDim2.new(0, 12, 0, 0)
    MainTitle2.BackgroundTransparency = 1
    MainTitle2.Text = "● V4.3 TESTING [VPN V2]"
    MainTitle2.TextColor3 = THEME.TextColor
    MainTitle2.Font = Enum.Font.Code
    MainTitle2.TextSize = 11
    MainTitle2.TextXAlignment = Enum.TextXAlignment.Left
    MainTitle2.ZIndex = 12
    MainTitle2.Parent = TitleBar

    local CloseBtn = Instance.new("TextButton")
    CloseBtn.Size = UDim2.new(0, 30, 0, 30)
    CloseBtn.Position = UDim2.new(1, -38, 0, 5)
    CloseBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
    CloseBtn.BorderColor3 = THEME.AccentColor
    CloseBtn.BorderSizePixel = 1
    CloseBtn.Text = "X"
    CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    CloseBtn.Font = Enum.Font.Code
    CloseBtn.TextSize = 16
    CloseBtn.ZIndex = 13
    CloseBtn.Parent = TitleBar
    Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(0, 15)

    local ScrollFrame = Instance.new("ScrollingFrame")
    ScrollFrame.Size = UDim2.new(1, 0, 1, -40)
    ScrollFrame.Position = UDim2.new(0, 0, 0, 40)
    ScrollFrame.BackgroundTransparency = 1
    ScrollFrame.BorderSizePixel = 0
    ScrollFrame.ScrollBarThickness = 6
    ScrollFrame.ScrollBarImageColor3 = THEME.AccentColor
    ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, 4200)
    ScrollFrame.ZIndex = 11
    ScrollFrame.Parent = MainHub

    local ScrollContent = Instance.new("Frame")
    ScrollContent.Size = UDim2.new(1, 0, 0, 4200)
    ScrollContent.BackgroundTransparency = 1
    ScrollContent.ZIndex = 11
    ScrollContent.Parent = ScrollFrame

    local function Section(title, y)
        local f = Instance.new("Frame")
        f.Size = UDim2.new(1, -20, 0, 26)
        f.Position = UDim2.new(0, 10, 0, y)
        f.BackgroundColor3 = THEME.SectionBG
        f.BorderColor3 = THEME.AccentColor
        f.BorderSizePixel = 1
        f.ZIndex = 12
        f.Parent = ScrollContent
        Instance.new("UICorner", f).CornerRadius = UDim.new(0, 4)
        local t = Instance.new("TextLabel")
        t.Size = UDim2.new(1, -10, 1, 0)
        t.Position = UDim2.new(0, 6, 0, 0)
        t.BackgroundTransparency = 1
        t.Text = title
        t.TextColor3 = THEME.TextColor
        t.Font = Enum.Font.Code
        t.TextSize = 11
        t.TextXAlignment = Enum.TextXAlignment.Left
        t.ZIndex = 13
        t.Parent = f
    end

    local function Toggle(text, y, callback)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, -20, 0, 38)
        btn.Position = UDim2.new(0, 10, 0, y)
        btn.BackgroundColor3 = THEME.ButtonBG
        btn.BorderColor3 = THEME.AccentColor
        btn.BorderSizePixel = 1
        btn.Text = text
        btn.TextColor3 = THEME.TextColor
        btn.Font = Enum.Font.Code
        btn.TextSize = 11
        btn.ZIndex = 12
        btn.Parent = ScrollContent
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 4)
        btn.MouseButton1Click:Connect(function() callback(btn) end)
        return btn
    end

    local function Input(placeholder, y, defaultText)
        local tb = Instance.new("TextBox")
        tb.Size = UDim2.new(1, -20, 0, 32)
        tb.Position = UDim2.new(0, 10, 0, y)
        tb.BackgroundColor3 = THEME.PanelBG
        tb.BorderColor3 = THEME.AccentColor
        tb.BorderSizePixel = 1
        tb.PlaceholderText = placeholder
        tb.PlaceholderColor3 = Color3.fromRGB(100, 50, 50)
        tb.Text = defaultText or ""
        tb.TextColor3 = THEME.TextColor
        tb.Font = Enum.Font.Code
        tb.TextSize = 11
        tb.ZIndex = 12
        tb.Parent = ScrollContent
        Instance.new("UICorner", tb).CornerRadius = UDim.new(0, 4)
        return tb
    end

    local function Half(text, y, xPos, callback)
        local b = Instance.new("TextButton")
        b.Size = UDim2.new(0.5, -15, 0, 32)
        b.Position = UDim2.new(xPos, 0, 0, y)
        b.BackgroundColor3 = THEME.ButtonBG
        b.BorderColor3 = THEME.AccentColor
        b.BorderSizePixel = 1
        b.Text = text
        b.TextColor3 = THEME.TextColor
        b.Font = Enum.Font.Code
        b.TextSize = 10
        b.ZIndex = 12
        b.Parent = ScrollContent
        Instance.new("UICorner", b).CornerRadius = UDim.new(0, 4)
        b.MouseButton1Click:Connect(function() callback(b) end)
        return b
    end

    -- SECTION 1: VPN V2
    Section("=== 🔒 VPN V2 (ANTI-KICK) ===", 10)

    local VPNMasterBtn = Instance.new("TextButton")
    VPNMasterBtn.Size = UDim2.new(1, -20, 0, 45)
    VPNMasterBtn.Position = UDim2.new(0, 10, 0, 42)
    VPNMasterBtn.BackgroundColor3 = THEME.ButtonBG
    VPNMasterBtn.BorderColor3 = THEME.ShieldColor
    VPNMasterBtn.BorderSizePixel = 2
    VPNMasterBtn.Text = "> 🔒 VPN V2: OFF"
    VPNMasterBtn.TextColor3 = THEME.ShieldColor
    VPNMasterBtn.Font = Enum.Font.Code
    VPNMasterBtn.TextSize = 13
    VPNMasterBtn.ZIndex = 12
    VPNMasterBtn.Parent = ScrollContent
    Instance.new("UICorner", VPNMasterBtn).CornerRadius = UDim.new(0, 5)
    VPNMasterBtn.MouseButton1Click:Connect(function()
        if VPNActive then
            DeactivateVPN()
            VPNMasterBtn.Text = "> 🔒 VPN V2: OFF"
            VPNMasterBtn.BackgroundColor3 = THEME.ButtonBG
        else
            ActivateVPN()
            VPNMasterBtn.Text = "> 🔒 VPN V2: ON"
            VPNMasterBtn.BackgroundColor3 = THEME.ButtonActive
        end
    end)

    Toggle("> 🛡️ ANTI-KICK: OFF", 92, function(btn)
        AntiKickEnabled = not AntiKickEnabled
        btn.Text = AntiKickEnabled and "> 🛡️ ANTI-KICK: ON" or "> 🛡️ ANTI-KICK: OFF"
        btn.BackgroundColor3 = AntiKickEnabled and THEME.ButtonActive or THEME.ButtonBG
        if AntiKickEnabled then ActivateAntiKick() else DeactivateAntiKick() end
    end)

    Toggle("> 🔄 AUTO-RECONNECT: OFF", 134, function(btn)
        AutoReconnectEnabled = not AutoReconnectEnabled
        btn.Text = AutoReconnectEnabled and "> 🔄 AUTO-RECONNECT: ON" or "> 🔄 AUTO-RECONNECT: OFF"
        btn.BackgroundColor3 = AutoReconnectEnabled and THEME.ButtonActive or THEME.ButtonBG
        if AutoReconnectEnabled then ActivateAutoReconnect() else DeactivateAutoReconnect() end
    end)

    Toggle("> 🕵️ ANTI-DETECT: OFF", 176, function(btn)
        AntiDetectEnabled = not AntiDetectEnabled
        btn.Text = AntiDetectEnabled and "> 🕵️ ANTI-DETECT: ON" or "> 🕵️ ANTI-DETECT: OFF"
        btn.BackgroundColor3 = AntiDetectEnabled and THEME.ButtonActive or THEME.ButtonBG
        if AntiDetectEnabled then ActivateAntiDetect() else DeactivateAntiDetect() end
    end)

    Toggle("> 📡 NETWORK WATCHDOG: OFF", 218, function(btn)
        NetworkWatchdogEnabled = not NetworkWatchdogEnabled
        btn.Text = NetworkWatchdogEnabled and "> 📡 WATCHDOG: ON" or "> 📡 WATCHDOG: OFF"
        btn.BackgroundColor3 = NetworkWatchdogEnabled and THEME.ButtonActive or THEME.ButtonBG
        if NetworkWatchdogEnabled then ActivateNetworkWatchdog() else DeactivateNetworkWatchdog() end
    end)

    local KickLogLbl = Instance.new("TextLabel")
    KickLogLbl.Size = UDim2.new(1, -20, 0, 18)
    KickLogLbl.Position = UDim2.new(0, 10, 0, 260)
    KickLogLbl.BackgroundTransparency = 1
    KickLogLbl.Text = "> KICK LOG:"
    KickLogLbl.TextColor3 = THEME.ShieldColor
    KickLogLbl.Font = Enum.Font.Code
    KickLogLbl.TextSize = 9
    KickLogLbl.TextXAlignment = Enum.TextXAlignment.Left
    KickLogLbl.ZIndex = 12
    KickLogLbl.Parent = ScrollContent

    local KickLogFrame = Instance.new("ScrollingFrame")
    KickLogFrame.Size = UDim2.new(1, -20, 0, 100)
    KickLogFrame.Position = UDim2.new(0, 10, 0, 280)
    KickLogFrame.BackgroundColor3 = THEME.PanelBG
    KickLogFrame.BorderColor3 = THEME.AccentColor
    KickLogFrame.BorderSizePixel = 1
    KickLogFrame.ScrollBarThickness = 5
    KickLogFrame.ScrollBarImageColor3 = THEME.AccentColor
    KickLogFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
    KickLogFrame.ZIndex = 12
    KickLogFrame.Parent = ScrollContent
    Instance.new("UICorner", KickLogFrame).CornerRadius = UDim.new(0, 4)

    local KickLogContainer = Instance.new("Frame")
    KickLogContainer.Size = UDim2.new(1, -10, 1, -10)
    KickLogContainer.Position = UDim2.new(0, 5, 0, 5)
    KickLogContainer.BackgroundTransparency = 1
    KickLogContainer.ZIndex = 13
    KickLogContainer.Parent = KickLogFrame

    task.spawn(function()
        while task.wait(1) do
            pcall(function()
                for _, child in pairs(KickLogContainer:GetChildren()) do
                    child:Destroy()
                end
                local y = 0
                for _, log in ipairs(KickLog) do
                    local item = Instance.new("TextLabel")
                    item.Size = UDim2.new(1, 0, 0, 20)
                    item.Position = UDim2.new(0, 0, 0, y)
                    item.BackgroundTransparency = 1
                    item.Text = "[" .. log.Time .. "] " .. log.Status .. " " .. log.Message
                    item.TextColor3 = log.Status == "BLOCKED" and THEME.ShieldColor or THEME.TextLight
                    item.Font = Enum.Font.Code
                    item.TextSize = 9
                    item.TextXAlignment = Enum.TextXAlignment.Left
                    item.TextTruncate = Enum.TextTruncate.AtEnd
                    item.ZIndex = 14
                    item.Parent = KickLogContainer
                    y = y + 22
                end
                KickLogFrame.CanvasSize = UDim2.new(0, 0, 0, math.max(y, 100))
            end)
        end
    end)

    -- SECTION 2: USER INFO
    Section("=== USER INFORMATION ===", 395)

    local UIF = Instance.new("Frame")
    UIF.Size = UDim2.new(1, -20, 0, 80)
    UIF.Position = UDim2.new(0, 10, 0, 427)
    UIF.BackgroundColor3 = THEME.PanelBG
    UIF.BorderColor3 = THEME.AccentColor
    UIF.BorderSizePixel = 1
    UIF.ZIndex = 12
    UIF.Parent = ScrollContent
    Instance.new("UICorner", UIF).CornerRadius = UDim.new(0, 4)

    local NameLbl = Instance.new("TextLabel")
    NameLbl.Size = UDim2.new(1, -15, 0, 22)
    NameLbl.Position = UDim2.new(0, 10, 0, 5)
    NameLbl.BackgroundTransparency = 1
    NameLbl.Text = "> NAME: " .. LocalPlayer.DisplayName
    NameLbl.TextColor3 = THEME.TextColor
    NameLbl.Font = Enum.Font.Code
    NameLbl.TextSize = 11
    NameLbl.TextXAlignment = Enum.TextXAlignment.Left
    NameLbl.ZIndex = 13
    NameLbl.Parent = UIF

    local UserLbl = Instance.new("TextLabel")
    UserLbl.Size = UDim2.new(1, -15, 0, 22)
    UserLbl.Position = UDim2.new(0, 10, 0, 28)
    UserLbl.BackgroundTransparency = 1
    UserLbl.Text = "> USER: " .. LocalPlayer.Name
    UserLbl.TextColor3 = THEME.TextColor
    UserLbl.Font = Enum.Font.Code
    UserLbl.TextSize = 11
    UserLbl.TextXAlignment = Enum.TextXAlignment.Left
    UserLbl.ZIndex = 13
    UserLbl.Parent = UIF

    local BuildLbl = Instance.new("TextLabel")
    BuildLbl.Size = UDim2.new(1, -15, 0, 22)
    BuildLbl.Position = UDim2.new(0, 10, 0, 51)
    BuildLbl.BackgroundTransparency = 1
    BuildLbl.Text = "> BUILD: V4.3 + VPN V2"
    BuildLbl.TextColor3 = THEME.ShieldColor
    BuildLbl.Font = Enum.Font.Code
    BuildLbl.TextSize = 11
    BuildLbl.TextXAlignment = Enum.TextXAlignment.Left
    BuildLbl.ZIndex = 13
    BuildLbl.Parent = UIF

    task.spawn(function()
        while task.wait(1) do
            pcall(function()
                NameLbl.Text = "> NAME: " .. LocalPlayer.DisplayName
                UserLbl.Text = "> USER: " .. LocalPlayer.Name
            end)
        end
    end)

    -- SECTION 3: MAIN FEATURES
    Section("=== MAIN FEATURES ===", 520)

    Toggle("> FPS BOOST: OFF", 552, function(btn)
        FPSBoostEnabled = not FPSBoostEnabled
        btn.Text = FPSBoostEnabled and "> FPS BOOST: ON" or "> FPS BOOST: OFF"
        btn.BackgroundColor3 = FPSBoostEnabled and THEME.ButtonActive or THEME.ButtonBG
        if FPSBoostEnabled then EnableFPSBoost() else DisableFPSBoost() end
    end)

    Toggle("> FULLBRIGHT: OFF", 594, function(btn)
        FullbrightEnabled = not FullbrightEnabled
        btn.Text = FullbrightEnabled and "> FULLBRIGHT: ON" or "> FULLBRIGHT: OFF"
        btn.BackgroundColor3 = FullbrightEnabled and THEME.ButtonActive or THEME.ButtonBG
        if FullbrightEnabled then EnableFullbright() else DisableFullbright() end
    end)

    Toggle("> SPEED HACK: OFF", 636, function(btn)
        SpeedHackEnabled = not SpeedHackEnabled
        btn.Text = SpeedHackEnabled and "> SPEED HACK: ON" or "> SPEED HACK: OFF"
        btn.BackgroundColor3 = SpeedHackEnabled and THEME.ButtonActive or THEME.ButtonBG
        ApplySpeedHack()
    end)

    local SpeedInput = Input("> Speed (16-500)", 678, tostring(SpeedMultiplier))
    SpeedInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local ns = tonumber(SpeedInput.Text)
            if ns then SpeedMultiplier = math.clamp(ns, 16, MaxSpeed) end
            SpeedInput.Text = tostring(SpeedMultiplier)
        end
    end)

    Toggle("> INFINITE JUMP: OFF", 715, function(btn)
        InfiniteJumpEnabled = not InfiniteJumpEnabled
        btn.Text = InfiniteJumpEnabled and "> INFINITE JUMP: ON" or "> INFINITE JUMP: OFF"
        btn.BackgroundColor3 = InfiniteJumpEnabled and THEME.ButtonActive or THEME.ButtonBG
        if InfiniteJumpEnabled then EnableInfiniteJump() else DisableInfiniteJump() end
    end)

    Toggle("> NOCLIP: OFF", 757, function(btn)
        NoclipEnabled = not NoclipEnabled
        btn.Text = NoclipEnabled and "> NOCLIP: ON" or "> NOCLIP: OFF"
        btn.BackgroundColor3 = NoclipEnabled and THEME.ButtonActive or THEME.ButtonBG
        if NoclipEnabled then EnableNoclip() else DisableNoclip() end
    end)

    -- SECTION 4: FLY
    Section("=== 🛫 FLY NORMAL (SKY) ===", 805)

    Toggle("> FLY NORMAL: OFF (F)", 837, function(btn)
        FlyNormalEnabled = not FlyNormalEnabled
        if FlyNormalEnabled then
            btn.Text = "> FLY NORMAL: ON"; btn.BackgroundColor3 = THEME.ButtonActive
            EnableFlyNormal()
        else
            btn.Text = "> FLY NORMAL: OFF (F)"; btn.BackgroundColor3 = THEME.ButtonBG
            DisableFlyNormal()
        end
    end)

    Half("> MODE: FREE", 879, 0, function(btn)
        FlyNormalMode = FlyNormalMode == "Free" and "Hover" or "Free"
        btn.Text = "> MODE: " .. string.upper(FlyNormalMode)
    end)

    Half("> KEY: F", 879, 0.5, function(btn)
        Notify("Fly", "> Tekan F", 2)
    end)

    local FlySpeedInput = Input("> Speed (10-500)", 916, tostring(FlyNormalSpeed))
    FlySpeedInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local ns = tonumber(FlySpeedInput.Text)
            if ns then FlyNormalSpeed = math.clamp(ns, 10, 500) end
            FlySpeedInput.Text = tostring(FlyNormalSpeed)
        end
    end)

    -- SECTION 5: NPC
    Section("=== 🆕 NPC DETECTION ===", 965)

    Toggle("> NPC DETECTION: OFF", 997, function(btn)
        NPCDetectionEnabled = not NPCDetectionEnabled
        btn.Text = NPCDetectionEnabled and "> NPC DETECTION: ON" or "> NPC DETECTION: OFF"
        btn.BackgroundColor3 = NPCDetectionEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    Toggle("> NPC ESP: OFF", 1039, function(btn)
        NPCEspEnabled = not NPCEspEnabled
        btn.Text = NPCEspEnabled and "> NPC ESP: ON" or "> NPC ESP: OFF"
        btn.BackgroundColor3 = NPCEspEnabled and THEME.ButtonActive or THEME.ButtonBG
        if not NPCEspEnabled then
            for model, _ in pairs(NPCESPObjects) do RemoveNPCEsp(model) end
        end
    end)

    local NPCFilterInput = Input("> Filter nama NPC", 1081, NPCFilterName)
    NPCFilterInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then NPCFilterName = NPCFilterInput.Text end
    end)

    local NPCMaxDistInput = Input("> Max Distance (50-5000)", 1118, tostring(NPCMaxDistance))
    NPCMaxDistInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nd = tonumber(NPCMaxDistInput.Text)
            if nd then NPCMaxDistance = math.clamp(nd, 50, 5000) end
            NPCMaxDistInput.Text = tostring(NPCMaxDistance)
        end
    end)

    -- SECTION 6: TELEPORT KE ORANG
    Section("=== 🆕 TELEPORT KE ORANG ===", 1165)

    TeleportListFrame = Instance.new("ScrollingFrame")
    TeleportListFrame.Size = UDim2.new(1, -20, 0, 180)
    TeleportListFrame.Position = UDim2.new(0, 10, 0, 1197)
    TeleportListFrame.BackgroundColor3 = THEME.PanelBG
    TeleportListFrame.BorderColor3 = THEME.AccentColor
    TeleportListFrame.BorderSizePixel = 1
    TeleportListFrame.ScrollBarThickness = 5
    TeleportListFrame.ScrollBarImageColor3 = THEME.AccentColor
    TeleportListFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
    TeleportListFrame.ZIndex = 12
    TeleportListFrame.Parent = ScrollContent
    Instance.new("UICorner", TeleportListFrame).CornerRadius = UDim.new(0, 4)

    TeleportListContainer = Instance.new("Frame")
    TeleportListContainer.Size = UDim2.new(1, -10, 1, -10)
    TeleportListContainer.Position = UDim2.new(0, 5, 0, 5)
    TeleportListContainer.BackgroundTransparency = 1
    TeleportListContainer.ZIndex = 13
    TeleportListContainer.Parent = TeleportListFrame

    task.spawn(function()
        while task.wait(2) do RefreshTeleportList() end
    end)

    -- SECTION 7: SERVER HOP
    Section("=== 🌐 SERVER HOP ===", 1390)

    Toggle("> 🌐 SERVER HOP (RANDOM)", 1422, function(btn) DoServerHop() end)
    Half("> 🎯 BEST SERVER", 1464, 0, function(btn) DoBestServerHop() end)
    Half("> 🔄 REJOIN", 1464, 0.5, function(btn) DoRejoin() end)

    local JobIdInput = Input("> Job ID (fallback)", 1501)
    Toggle("> 🔗 JOIN BY JOB ID", 1538, function(btn) DoJoinByJobId(JobIdInput.Text) end)

    -- SECTION 8: HITBOX
    Section("=== 🎯 HITBOX EXPANDER ===", 1587)

    Toggle("> HITBOX: OFF", 1619, function(btn)
        HitboxEnabled = not HitboxEnabled
        if HitboxEnabled then
            btn.Text = "> HITBOX: ON"; btn.BackgroundColor3 = THEME.ButtonActive
            StartHitboxExpander()
        else
            btn.Text = "> HITBOX: OFF"; btn.BackgroundColor3 = THEME.ButtonBG
            StopHitboxExpander()
        end
    end)

    local HitboxInput = Input("> Size (1-1000)", 1661, tostring(HitboxSize))
    HitboxInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nh = tonumber(HitboxInput.Text)
            if nh then HitboxSize = math.clamp(nh, 1, 1000) end
            HitboxInput.Text = tostring(HitboxSize)
        end
    end)

    -- SECTION 9: ANTI-AFK
    Section("=== 😴 ANTI-AFK ===", 1708)

    Toggle("> ANTI-AFK: OFF", 1740, function(btn)
        AntiAFKEnabled = not AntiAFKEnabled
        btn.Text = AntiAFKEnabled and "> ANTI-AFK: ON" or "> ANTI-AFK: OFF"
        btn.BackgroundColor3 = AntiAFKEnabled and THEME.ButtonActive or THEME.ButtonBG
        if AntiAFKEnabled then StartAntiAFK() else StopAntiAFK() end
    end)

    -- SECTION 10: SURVIVAL
    Section("=== SURVIVAL FEATURES ===", 1788)

    Toggle("> AUTO RESPAWN: OFF", 1820, function(btn)
        AutoRespawnEnabled = not AutoRespawnEnabled
        btn.Text = AutoRespawnEnabled and "> AUTO RESPAWN: ON" or "> AUTO RESPAWN: OFF"
        btn.BackgroundColor3 = AutoRespawnEnabled and THEME.ButtonActive or THEME.ButtonBG
        if AutoRespawnEnabled then StartAutoRespawn() else StopAutoRespawn() end
    end)

    Toggle("> ANTI-FLING: OFF", 1862, function(btn)
        AntiFlingEnabled = not AntiFlingEnabled
        btn.Text = AntiFlingEnabled and "> ANTI-FLING: ON" or "> ANTI-FLING: OFF"
        btn.BackgroundColor3 = AntiFlingEnabled and THEME.ButtonActive or THEME.ButtonBG
        if AntiFlingEnabled then StartAntiFling() else StopAntiFling() end
    end)

    Toggle("> LOOK AT PLAYER: OFF", 1904, function(btn)
        LookAtEnabled = not LookAtEnabled
        btn.Text = LookAtEnabled and "> LOOK AT PLAYER: ON" or "> LOOK AT PLAYER: OFF"
        btn.BackgroundColor3 = LookAtEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    -- SECTION 11: FLY-VOID
    Section("=== 🕳️ FLY-VOID V2 ===", 1953)

    Toggle("> FLY-VOID: OFF (V)", 1985, function(btn)
        FlyVoidEnabled = not FlyVoidEnabled
        if FlyVoidEnabled then
            btn.Text = "> FLY-VOID: ON"; btn.BackgroundColor3 = THEME.ButtonActive
            EnableFlyVoid()
        else
            btn.Text = "> FLY-VOID: OFF (V)"; btn.BackgroundColor3 = THEME.ButtonBG
            DisableFlyVoid()
        end
    end)

    Half("> HIDE: OFF", 2026, 0, function(btn)
        FlyVoidHideMode = not FlyVoidHideMode
        btn.Text = FlyVoidHideMode and "> HIDE: ON" or "> HIDE: OFF"
        btn.BackgroundColor3 = FlyVoidHideMode and THEME.ButtonActive or THEME.ButtonBG
    end)

    Half("> KEY: V", 2026, 0.5, function(btn)
        Notify("Fly-Void", "> Tekan V", 2)
    end)

    -- SECTION 12: SOUND ESP
    Section("=== SOUND ESP ===", 2073)

    Toggle("> SOUND ESP: OFF", 2105, function(btn)
        SoundESPEnabled = not SoundESPEnabled
        btn.Text = SoundESPEnabled and "> SOUND ESP: ON" or "> SOUND ESP: OFF"
        btn.BackgroundColor3 = SoundESPEnabled and THEME.ButtonActive or THEME.ButtonBG
        if SoundESPEnabled then StartSoundESP() else StopSoundESP() end
    end)

    local SndInput = Input("> Radius (10-500)", 2147, tostring(SoundESPRadius))
    SndInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nr = tonumber(SndInput.Text)
            if nr then SoundESPRadius = math.clamp(nr, 10, 500) end
            SndInput.Text = tostring(SoundESPRadius)
        end
    end)

    -- SECTION 13: MUSIC
    Section("=== 🎵 MUSIC PLAYLIST ===", 2194)

    local MusicNowLbl = Instance.new("TextLabel")
    MusicNowLbl.Size = UDim2.new(1, -20, 0, 22)
    MusicNowLbl.Position = UDim2.new(0, 10, 0, 2226)
    MusicNowLbl.BackgroundTransparency = 1
    MusicNowLbl.Text = "> NOW: " .. MusicPlaylist[MusicCurrentIndex].Name
    MusicNowLbl.TextColor3 = THEME.TextColor
    MusicNowLbl.Font = Enum.Font.Code
    MusicNowLbl.TextSize = 10
    MusicNowLbl.TextXAlignment = Enum.TextXAlignment.Left
    MusicNowLbl.ZIndex = 12
    MusicNowLbl.Parent = ScrollContent

    task.spawn(function()
        while task.wait(0.5) do
            MusicNowLbl.Text = "> NOW: " .. MusicPlaylist[MusicCurrentIndex].Name
        end
    end)

    Toggle("> MUSIC PLAYER: OFF", 2253, function(btn)
        MusicPlayerEnabled = not MusicPlayerEnabled
        btn.Text = MusicPlayerEnabled and "> MUSIC PLAYER: ON" or "> MUSIC PLAYER: OFF"
        btn.BackgroundColor3 = MusicPlayerEnabled and THEME.ButtonActive or THEME.ButtonBG
        if MusicPlayerEnabled then PlayMusic() else StopMusic() end
    end)

    Half("> ⏮ PREV", 2295, 0, function(btn) PrevMusic() end)
    Half("> ⏭ NEXT", 2295, 0.5, function(btn) NextMusic() end)

    local MusicVolInput = Input("> Volume (0-10)", 2332, tostring(MusicVolume))
    MusicVolInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nv = tonumber(MusicVolInput.Text)
            if nv then SetMusicVolume(nv) end
            MusicVolInput.Text = tostring(MusicVolume)
        end
    end)

    -- SECTION 14: AIMBOT
    Section("=== 🎯 ENHANCED AIMBOT ===", 2379)

    local AimbotBtn = Instance.new("TextButton")
    AimbotBtn.Size = UDim2.new(1, -20, 0, 45)
    AimbotBtn.Position = UDim2.new(0, 10, 0, 2411)
    AimbotBtn.BackgroundColor3 = THEME.ButtonBG
    AimbotBtn.BorderColor3 = THEME.AccentColor
    AimbotBtn.BorderSizePixel = 2
    AimbotBtn.Text = "> AIMBOT: OFF"
    AimbotBtn.TextColor3 = THEME.TextColor
    AimbotBtn.Font = Enum.Font.Code
    AimbotBtn.TextSize = 13
    AimbotBtn.ZIndex = 12
    AimbotBtn.Parent = ScrollContent
    Instance.new("UICorner", AimbotBtn).CornerRadius = UDim.new(0, 5)
    AimbotBtn.MouseButton1Click:Connect(function()
        AimbotEnabled = not AimbotEnabled
        if AimbotEnabled then
            AimbotBtn.Text = "> AIMBOT: ON"; AimbotBtn.BackgroundColor3 = THEME.ButtonActive
        else
            AimbotBtn.Text = "> AIMBOT: OFF"; AimbotBtn.BackgroundColor3 = THEME.ButtonBG
            AimbotStickyTarget = nil; AimbotStickyType = nil
        end
    end)

    Half("> FOV CIRCLE: OFF", 2466, 0, function(btn)
        FOVCircleEnabled = not FOVCircleEnabled
        btn.Text = FOVCircleEnabled and "> FOV: ON" or "> FOV: OFF"
        btn.BackgroundColor3 = FOVCircleEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    Half("> TEAM CHECK: OFF", 2466, 0.5, function(btn)
        AimbotTeamCheck = not AimbotTeamCheck
        btn.Text = AimbotTeamCheck and "> TEAM: ON" or "> TEAM: OFF"
        btn.BackgroundColor3 = AimbotTeamCheck and THEME.ButtonActive or THEME.ButtonBG
    end)

    local TargetLbl = Instance.new("TextLabel")
    TargetLbl.Size = UDim2.new(1, -20, 0, 18)
    TargetLbl.Position = UDim2.new(0, 10, 0, 2508)
    TargetLbl.BackgroundTransparency = 1
    TargetLbl.Text = "> TARGET: HEAD"
    TargetLbl.TextColor3 = THEME.TextColor
    TargetLbl.Font = Enum.Font.Code
    TargetLbl.TextSize = 10
    TargetLbl.TextXAlignment = Enum.TextXAlignment.Left
    TargetLbl.ZIndex = 12
    TargetLbl.Parent = ScrollContent

    local TargetBtnFrame = Instance.new("Frame")
    TargetBtnFrame.Size = UDim2.new(1, -20, 0, 30)
    TargetBtnFrame.Position = UDim2.new(0, 10, 0, 2528)
    TargetBtnFrame.BackgroundTransparency = 1
    TargetBtnFrame.ZIndex = 12
    TargetBtnFrame.Parent = ScrollContent

    local function TargetBtn(text, part, xPos)
        local b = Instance.new("TextButton")
        b.Size = UDim2.new(0.32, 0, 1, 0)
        b.Position = UDim2.new(xPos, 0, 0, 0)
        b.BackgroundColor3 = THEME.ButtonBG
        b.BorderColor3 = THEME.AccentColor
        b.BorderSizePixel = 1
        b.Text = text
        b.TextColor3 = THEME.TextColor
        b.Font = Enum.Font.Code
        b.TextSize = 10
        b.ZIndex = 13
        b.Parent = TargetBtnFrame
        Instance.new("UICorner", b).CornerRadius = UDim.new(0, 3)
        b.MouseButton1Click:Connect(function()
            AimbotTargetPart = part
            TargetLbl.Text = "> TARGET: " .. string.upper(text)
        end)
    end
    TargetBtn("HEAD", "Head", 0)
    TargetBtn("TORSO", "HumanoidRootPart", 0.345)
    TargetBtn("BODY", "UpperTorso", 0.69)

    local FOVInput = Input("> FOV (50-2000)", 2565, tostring(AimbotFOV))
    FOVInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nf = tonumber(FOVInput.Text)
            if nf then AimbotFOV = math.clamp(nf, 50, 2000) end
            FOVInput.Text = tostring(AimbotFOV)
        end
    end)

    Toggle("> KEYBIND HOLD (E): OFF", 2602, function(btn)
        AimbotKeybindEnabled = not AimbotKeybindEnabled
        btn.Text = AimbotKeybindEnabled and "> KEYBIND (E): ON" or "> KEYBIND (E): OFF"
        btn.BackgroundColor3 = AimbotKeybindEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    Toggle("> WALL CHECK: OFF", 2644, function(btn)
        AimbotWallCheck = not AimbotWallCheck
        btn.Text = AimbotWallCheck and "> WALL CHECK: ON" or "> WALL CHECK: OFF"
        btn.BackgroundColor3 = AimbotWallCheck and THEME.ButtonActive or THEME.ButtonBG
        AimbotStickyTarget = nil
    end)

    -- SECTION 15: ESP
    Section("=== FULL ESP ===", 2691)

    Toggle("> ESP MASTER: OFF", 2723, function(btn)
        ESPEnabled = not ESPEnabled
        btn.Text = ESPEnabled and "> ESP MASTER: ON" or "> ESP MASTER: OFF"
        btn.BackgroundColor3 = ESPEnabled and THEME.ButtonActive or THEME.ButtonBG
        if not ESPEnabled then
            for p, _ in pairs(ChamsObjects) do RemoveChams(p) end
        end
    end)

    Toggle("> 🌈 RAINBOW ESP: OFF", 2765, function(btn)
        RainbowESPEnabled = not RainbowESPEnabled
        btn.Text = RainbowESPEnabled and "> 🌈 RAINBOW: ON" or "> 🌈 RAINBOW: OFF"
        btn.BackgroundColor3 = RainbowESPEnabled and THEME.ButtonActive or THEME.ButtonBG
        if RainbowESPEnabled then EnableRainbowESP() else DisableRainbowESP() end
    end)

    -- SECTION 16: RADAR
    Section("=== 📡 RADAR BULAT ===", 2813)

    Toggle("> RADAR: OFF", 2845, function(btn)
        RadarEnabled = not RadarEnabled
        if RadarEnabled then
            btn.Text = "> RADAR: ON"; btn.BackgroundColor3 = THEME.ButtonActive
            if RadarFrame then RadarFrame.Visible = true end
            StartRadar()
        else
            btn.Text = "> RADAR: OFF"; btn.BackgroundColor3 = THEME.ButtonBG
            if RadarFrame then RadarFrame.Visible = false end
            StopRadar()
        end
    end)

    Half("> ROTATE: OFF", 2887, 0, function(btn)
        RadarRotateWithCamera = not RadarRotateWithCamera
        btn.Text = RadarRotateWithCamera and "> ROTATE: ON" or "> ROTATE: OFF"
        btn.BackgroundColor3 = RadarRotateWithCamera and THEME.ButtonActive or THEME.ButtonBG
    end)

    Half("> NAMES: OFF", 2887, 0.5, function(btn)
        RadarShowNames = not RadarShowNames
        btn.Text = RadarShowNames and "> NAMES: ON" or "> NAMES: OFF"
        btn.BackgroundColor3 = RadarShowNames and THEME.ButtonActive or THEME.ButtonBG
    end)

    local RadarRadInput = Input("> Radius (100-1000)", 2924, tostring(RadarRadius))
    RadarRadInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nr = tonumber(RadarRadInput.Text)
            if nr then RadarRadius = math.clamp(nr, 100, 1000) end
            RadarRadInput.Text = tostring(RadarRadius)
        end
    end)

    -- SECTION 17: CHAT SPAM
    Section("=== CHAT SPAM ===", 2971)

    Toggle("> CHAT SPAM: OFF", 3003, function(btn)
        ChatSpamEnabled = not ChatSpamEnabled
        btn.Text = ChatSpamEnabled and "> CHAT SPAM: ON" or "> CHAT SPAM: OFF"
        btn.BackgroundColor3 = ChatSpamEnabled and THEME.ButtonActive or THEME.ButtonBG
        if ChatSpamEnabled then StartChatSpamLoop() end
    end)

    local ChatInput = Input("> Message", 3045, ChatSpamText)
    ChatInput.FocusLost:Connect(function(enterPressed)
        if enterPressed and ChatInput.Text ~= "" then ChatSpamText = ChatInput.Text end
    end)

    -- SECTION 18: TELEPORT
    Section("=== TELEPORT ===", 3092)

    Toggle("> TELEPORT TO MOUSE", 3124, function(btn) TeleportToMouse() end)
    Half("> SAVE LOC", 3166, 0, function(btn) SaveLocation() end)
    Half("> LOAD LOC", 3166, 0.5, function(btn) LoadLocation() end)

    -- TOGGLE MENU BUTTON
    local ToggleMenuButton = Instance.new("TextButton")
    ToggleMenuButton.Size = UDim2.new(0, 50, 0, 50)
    ToggleMenuButton.Position = UDim2.new(0, 10, 0.5, -25)
    ToggleMenuButton.BackgroundColor3 = THEME.ButtonActive
    ToggleMenuButton.BorderColor3 = THEME.AccentColor
    ToggleMenuButton.BorderSizePixel = 2
    ToggleMenuButton.Text = "≡"
    ToggleMenuButton.TextColor3 = THEME.TextColor
    ToggleMenuButton.Font = Enum.Font.Code
    ToggleMenuButton.TextSize = 24
    ToggleMenuButton.ZIndex = 15
    ToggleMenuButton.Visible = false
    ToggleMenuButton.Parent = ScreenGui
    Instance.new("UICorner", ToggleMenuButton).CornerRadius = UDim.new(0, 25)

    local btnDragging = false
    local btnDragStart = nil
    local btnStartPos = nil
    local btnMoved = false

    ToggleMenuButton.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            btnDragging = true; btnMoved = false
            btnDragStart = input.Position; btnStartPos = ToggleMenuButton.Position
        end
    end)

    ToggleMenuButton.InputChanged:Connect(function(input)
        if (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) and btnDragging then
            local delta = input.Position - btnDragStart
            if math.abs(delta.X) > 8 or math.abs(delta.Y) > 8 then btnMoved = true end
            if btnMoved then
                ToggleMenuButton.Position = UDim2.new(
                    btnStartPos.X.Scale, btnStartPos.X.Offset + delta.X,
                    btnStartPos.Y.Scale, btnStartPos.Y.Offset + delta.Y
                )
            end
        end
    end)

    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            btnDragging = false
        end
    end)

    ToggleMenuButton.MouseButton1Click:Connect(function()
        if btnMoved then return end
        MenuVisible = not MenuVisible
        MainHub.Visible = MenuVisible
    end)

    CloseBtn.MouseButton1Click:Connect(function()
        MenuVisible = false
        MainHub.Visible = false
    end)

    UserInputService.InputBegan:Connect(function(input, gp)
        if gp then return end
        if input.KeyCode == MenuKey then
            MenuVisible = not MenuVisible
            MainHub.Visible = MenuVisible
        end
    end)

    local function MakeDraggable(frame)
        local dragging = false
        local dragInput, dragStart, startPos
        frame.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = true; dragStart = input.Position; startPos = frame.Position
            end
        end)
        frame.InputChanged:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
                dragInput = input
            end
        end)
        UserInputService.InputChanged:Connect(function(input)
            if input == dragInput and dragging then
                local delta = input.Position - dragStart
                frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
            end
        end)
        UserInputService.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = false
            end
        end)
    end

    MakeDraggable(MainHub)

    -- LOADING + LANGSUNG MUNCUL MENU
    local loadingMessages = {
        "> LOADING V4.3 + VPN V2...",
        "> INIT VPN ANTI-KICK...",
        "> INIT ANTI-DETECT...",
        "> INIT WATCHDOG...",
        "> INIT FLY NORMAL...",
        "> INIT NPC DETECTION...",
        "> INIT SEMUA FITUR...",
        "> MOBILE READY...",
        "> SYSTEM READY!"
    }

    task.spawn(function()
        for i = 1, 100 do
            task.wait(0.06)
            LBarFill.Size = UDim2.new(i / 100, 0, 1, 0)
            LPercent.Text = i .. "%"
            local mi = math.floor(i / 10) + 1
            if mi > #loadingMessages then mi = #loadingMessages end
            LStatus.Text = loadingMessages[mi]
        end
        LPercent.Text = "100%"
        LStatus.Text = "> SYSTEM READY!"
        task.wait(0.5)
        LoadingScreen.Visible = false
        MainHub.Visible = true
        ToggleMenuButton.Visible = true
        MenuVisible = true
        Notify("V4.3 + VPN V2", "> LANGSUNG SIAP!", 3)
        Notify("🔒 VPN V2", "> Klik Master untuk aktifkan", 3)
        task.wait(0.5)
        RefreshTeleportList()
    end)
end

--==============================================================
-- RUN
--==============================================================
pcall(CreateUI)
pcall(CreateRadarUI)
