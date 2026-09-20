--[[
    ZetGames-AimLock-Advanserver V3.9.1 | TESTING BUILD
    Theme: Red & Black Hacker Style
    Aimbot: SIMPLE + STICKY 100%
    New: Aimbot Simple + Radar Bulat + Fly-Void
    Night Lock: REMOVED
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
local Camera = workspace.CurrentCamera

local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

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
    SuccessColor = Color3.fromRGB(255, 50, 50),
    BlueDot = Color3.fromRGB(0, 150, 255),
    RedDot = Color3.fromRGB(255, 30, 30),
}

--==============================================================
-- VARIABLES
--==============================================================
local IsLoggedIn = false
local MenuVisible = true
local MenuKey = Enum.KeyCode.RightControl

-- AIMBOT SIMPLE
local AimbotEnabled = false
local AimbotTargetPart = "Head"
local AimbotSmoothness = 3  -- makin kecil = makin lengket
local AimbotFOV = 200
local AimbotKeybindEnabled = false
local AimbotKeybind = Enum.KeyCode.E
local AimbotKeyHeld = false
local AimbotStickyTarget = nil
local AimbotStickyTime = 0
local AimbotTeamCheck = false
local AimbotWallCheck = false

-- ESP
local ESPEnabled = false
local ESPBoxEnabled = true
local ESPNameEnabled = true
local ESPDistanceEnabled = true
local ESPHealthEnabled = true
local ESPTracerEnabled = true
local ESPObjects = {}

-- Tracer color
local TracerColor = Color3.fromRGB(255, 0, 0)

-- Radar
local RadarEnabled = false
local RadarRadius = 300
local RadarZoom = 1.0
local RadarRotateWithCamera = true
local RadarShowNames = false
local RadarConnection = nil
local RadarObjects = {}
local RadarFrame = nil
local RadarDrawingBg = nil
local RadarDrawingBorder = nil
local RadarDrawingCenterDot = nil
local RadarDrawingCompass = {}
local RadarNearestLabel = nil
local RadarSize = 200

-- Fly-Void
local FlyVoidEnabled = false
local FlyVoidConnection = nil
local FlyVoidHeight = -60
local FlyVoidHideMode = true
local FlyVoidKeybind = Enum.KeyCode.V
local OriginalTransparency = {}

-- Fullbright
local FullbrightEnabled = false
local OriginalLighting = {}

-- FPS Boost
local FPSBoostEnabled = false
local OriginalSettings = {}

-- Speed
local SpeedHackEnabled = false
local SpeedMultiplier = 100
local MaxSpeed = 500
local DefaultWalkSpeed = 16

-- Noclip
local NoclipEnabled = false
local NoclipConnection = nil

-- Infinite Jump
local InfiniteJumpEnabled = false
local JumpConnection = nil

-- Chat Spam
local ChatSpamEnabled = false
local ChatSpamText = "ZETGAMES-AIMLOCK-ADVANSERVER"
local ChatSpamDelay = 3

-- Sound ESP
local SoundESPEnabled = false
local SoundESPRadius = 100
local SoundESPConnection = nil
local SoundESPBeep = nil
local LastBeepTime = 0

-- Music
local MusicPlayerEnabled = false
local MusicSound = nil
local MusicID = "1837879082"
local MusicVolume = 1

-- Auto Respawn
local AutoRespawnEnabled = false
local AutoRespawnConnection = nil

-- Anti-Fling
local AntiFlingEnabled = false
local AntiFlingConnection = nil

-- Teleport
local SavedLocation = nil

-- FOV Circle
local FOVCircleEnabled = false
local FOVCircle = Drawing.new("Circle")
FOVCircle.Visible = false
FOVCircle.Color = THEME.AccentColor
FOVCircle.Thickness = 2
FOVCircle.Filled = false
FOVCircle.Transparency = 1
FOVCircle.NumSides = 60

--==============================================================
-- KEY SYSTEM
--==============================================================
local ValidKeys = {
    ["AzferModz"] = {Expiry = 0, Level = "Premium"},
    ["AzferFree"] = {Expiry = os.time({year=2026, month=9, day=5, hour=0, min=0, sec=0}), Level = "Free"},
    ["AzferCode"] = {Expiry = os.time({year=2026, month=11, day=26, hour=0, min=0, sec=0}), Level = "Code"},
    ["AzferHc"] = {Expiry = os.time({year=2027, month=1, day=27, hour=0, min=0, sec=0}), Level = "Code"},
    ["FazxyFree"] = {Expiry = os.time({year=2027, month=9, day=10, hour=0, min=0, sec=0}), Level = "Code"}
}
local KeyWebsite = "https://arkaraffaza387-dotcom.github.io/Key-Zero/"

--==============================================================
-- SCREEN GUI
--==============================================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ZetGamesAdvanserverV391"
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
-- FULLBRIGHT
--==============================================================
local function EnableFullbright()
    OriginalLighting.Ambient = Lighting.Ambient
    OriginalLighting.OutdoorAmbient = Lighting.OutdoorAmbient
    OriginalLighting.Brightness = Lighting.Brightness
    OriginalLighting.ClockTime = Lighting.ClockTime
    OriginalLighting.FogEnd = Lighting.FogEnd
    OriginalLighting.GlobalShadows = Lighting.GlobalShadows
    Lighting.Ambient = Color3.fromRGB(255, 255, 255)
    Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
    Lighting.Brightness = 3
    Lighting.ClockTime = 12
    Lighting.FogEnd = 100000
    Lighting.GlobalShadows = false
end

local function DisableFullbright()
    pcall(function()
        if OriginalLighting.Ambient then Lighting.Ambient = OriginalLighting.Ambient end
        if OriginalLighting.OutdoorAmbient then Lighting.OutdoorAmbient = OriginalLighting.OutdoorAmbient end
        if OriginalLighting.Brightness then Lighting.Brightness = OriginalLighting.Brightness end
        if OriginalLighting.ClockTime then Lighting.ClockTime = OriginalLighting.ClockTime end
        if OriginalLighting.FogEnd then Lighting.FogEnd = OriginalLighting.FogEnd end
        if OriginalLighting.GlobalShadows ~= nil then Lighting.GlobalShadows = OriginalLighting.GlobalShadows end
    end)
end

--==============================================================
-- FPS BOOST
--==============================================================
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
    for _, part in pairs(Workspace:GetDescendants()) do
        if part:IsA("ParticleEmitter") or part:IsA("Fire") or part:IsA("Smoke") or part:IsA("Sparkles") then
            pcall(function() part.Enabled = false end)
        end
    end
end

local function DisableFPSBoost()
    pcall(function()
        if OriginalSettings.Shadows ~= nil then Lighting.GlobalShadows = OriginalSettings.Shadows end
        if OriginalSettings.FogEnd ~= nil then Lighting.FogEnd = OriginalSettings.FogEnd end
        if OriginalSettings.Brightness ~= nil then Lighting.Brightness = OriginalSettings.Brightness end
    end)
end

--==============================================================
-- SOUND ESP
--==============================================================
local function StartSoundESP()
    if SoundESPBeep then pcall(function() SoundESPBeep:Destroy() end) end
    SoundESPBeep = Instance.new("Sound")
    SoundESPBeep.SoundId = "rbxassetid://4790566870"
    SoundESPBeep.Volume = 1
    SoundESPBeep.Parent = SoundService

    if SoundESPConnection then SoundESPConnection:Disconnect() end
    SoundESPConnection = RunService.Heartbeat:Connect(function()
        if not SoundESPEnabled or not IsLoggedIn then return end
        if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then return end
        local myPos = LocalPlayer.Character.HumanoidRootPart.Position
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

--==============================================================
-- MUSIC
--==============================================================
local function StartMusic()
    if MusicSound then pcall(function() MusicSound:Destroy() end) end
    MusicSound = Instance.new("Sound")
    MusicSound.SoundId = "rbxassetid://" .. MusicID
    MusicSound.Volume = MusicVolume
    MusicSound.Looped = true
    MusicSound.Parent = SoundService
    pcall(function() MusicSound:Play() end)
end

local function StopMusic()
    if MusicSound then pcall(function() MusicSound:Stop(); MusicSound:Destroy() end); MusicSound = nil end
end

local function UpdateMusic()
    if MusicSound then
        MusicSound.SoundId = "rbxassetid://" .. MusicID
        MusicSound.Volume = MusicVolume
        if MusicPlayerEnabled then pcall(function() MusicSound:Play() end) end
    end
end

--==============================================================
-- AUTO RESPAWN
--==============================================================
local function StartAutoRespawn()
    if AutoRespawnConnection then AutoRespawnConnection:Disconnect() end
    AutoRespawnConnection = RunService.Heartbeat:Connect(function()
        if not AutoRespawnEnabled or not IsLoggedIn then return end
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

--==============================================================
-- ANTI-FLING
--==============================================================
local function StartAntiFling()
    if AntiFlingConnection then AntiFlingConnection:Disconnect() end
    AntiFlingConnection = RunService.Heartbeat:Connect(function()
        if not AntiFlingEnabled or not IsLoggedIn then return end
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

--==============================================================
-- FLY-VOID
--==============================================================
local function EnableFlyVoid()
    if FlyVoidConnection then FlyVoidConnection:Disconnect() end
    FlyVoidConnection = RunService.Heartbeat:Connect(function()
        if not FlyVoidEnabled or not IsLoggedIn then return end
        local char = LocalPlayer.Character
        if not char then return end
        local root = char:FindFirstChild("HumanoidRootPart")
        local hum = char:FindFirstChild("Humanoid")
        if not root or not hum then return end

        local pos = root.Position
        local targetY = FlyVoidHeight

        -- Auto stuck di bawah map
        if pos.Y < -30 then
            pcall(function()
                root.CFrame = CFrame.new(pos.X, targetY, pos.Z)
                root.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            end)
        end

        -- Anti-kill
        if hum.Health <= 0 then
            pcall(function() LocalPlayer:LoadCharacter() end)
        end

        -- Hide mode
        if FlyVoidHideMode then
            local isBelow = pos.Y < 0
            for _, part in pairs(char:GetDescendants()) do
                if part:IsA("BasePart") then
                    if isBelow then
                        if not OriginalTransparency[part] then
                            OriginalTransparency[part] = part.Transparency
                        end
                        part.Transparency = 0.85
                        part.CanCollide = false
                    else
                        if OriginalTransparency[part] then
                            part.Transparency = OriginalTransparency[part]
                        end
                    end
                end
            end
        end

        -- Stabilize
        if math.abs(root.AssemblyLinearVelocity.Y) > 50 then
            root.AssemblyLinearVelocity = Vector3.new(root.AssemblyLinearVelocity.X, 0, root.AssemblyLinearVelocity.Z)
        end
    end)
end

local function DisableFlyVoid()
    if FlyVoidConnection then FlyVoidConnection:Disconnect(); FlyVoidConnection = nil end
    local char = LocalPlayer.Character
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") and OriginalTransparency[part] then
                part.Transparency = OriginalTransparency[part]
                part.CanCollide = true
            end
        end
    end
    OriginalTransparency = {}
end

--==============================================================
-- NOCLIP
--==============================================================
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

--==============================================================
-- INFINITE JUMP
--==============================================================
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

--==============================================================
-- SPEED
--==============================================================
local function ApplySpeedHack()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = SpeedHackEnabled and SpeedMultiplier or DefaultWalkSpeed
    end
end

--==============================================================
-- CHAT SPAM
--==============================================================
local function SendChatMessage(message)
    pcall(function()
        local chatEvents = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents")
        if chatEvents then
            local sayReq = chatEvents:FindFirstChild("SayMessageRequest")
            if sayReq then sayReq:FireServer(message, "All"); return end
        end
    end)
    pcall(function()
        local channels = TextChatService:FindFirstChild("TextChannels")
        if channels then
            local gen = channels:FindFirstChild("RBXGeneral")
            if gen then gen:SendAsync(message) end
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

--==============================================================
-- TELEPORT
--==============================================================
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
end

local function UpdateESP()
    if not ESPEnabled or not IsLoggedIn then
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

                    d.Box.Visible = ESPBoxEnabled
                    if ESPBoxEnabled then d.Box.Position = Vector2.new(bX, bY); d.Box.Size = bSize end
                    d.Name.Visible = ESPNameEnabled
                    if ESPNameEnabled then d.Name.Text = player.Name; d.Name.Position = Vector2.new(sp.X, bY-15) end
                    d.Distance.Visible = ESPDistanceEnabled
                    if ESPDistanceEnabled then d.Distance.Text = math.floor(dist).."m"; d.Distance.Position = Vector2.new(sp.X, bY+bSize.Y+5) end
                    d.HealthBg.Visible = ESPHealthEnabled
                    d.HealthBar.Visible = ESPHealthEnabled
                    if ESPHealthEnabled then
                        local hp = hum.Health / hum.MaxHealth
                        d.HealthBg.From = Vector2.new(bX, bY+bSize.Y+20)
                        d.HealthBg.To = Vector2.new(bX+bSize.X, bY+bSize.Y+20)
                        d.HealthBar.From = Vector2.new(bX, bY+bSize.Y+20)
                        d.HealthBar.To = Vector2.new(bX+bSize.X*hp, bY+bSize.Y+20)
                    end
                    d.Tracer.Visible = ESPTracerEnabled
                    if ESPTracerEnabled then
                        d.Tracer.Color = TracerColor
                        d.Tracer.From = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y)
                        d.Tracer.To = Vector2.new(sp.X, sp.Y)
                    end
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
-- 🎯 AIMBOT SIMPLE + STICKY 100%
--==============================================================
local function IsSameTeam(player)
    if not AimbotTeamCheck then return false end
    if LocalPlayer.Team and player.Team and LocalPlayer.Team == player.Team then return true end
    return false
end

local function IsWallBetween(origin, target, targetChar)
    if not AimbotWallCheck then return false end
    local dir = (target - origin).Unit
    local dist = (target - origin).Magnitude
    local rp = RaycastParams.new()
    rp.FilterType = Enum.RaycastFilterType.Blacklist
    rp.FilterDescendantsInstances = {LocalPlayer.Character}
    local res = workspace:Raycast(origin, dir * dist, rp)
    if res then
        if res.Instance and targetChar and res.Instance:IsDescendantOf(targetChar) then return false end
        return true
    end
    return false
end

-- Cari target TERDEKAT dari crosshair dalam FOV
local function FindTarget()
    local bestTarget = nil
    local bestDist = AimbotFOV
    local screenCenter = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
    local camPos = Camera.CFrame.Position

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local hum = player.Character:FindFirstChild("Humanoid")
            local root = player.Character:FindFirstChild("HumanoidRootPart")
            if hum and root and hum.Health > 0 then
                if not IsSameTeam(player) then
                    local part = player.Character:FindFirstChild(AimbotTargetPart) or root
                    local sp, on = Camera:WorldToViewportPoint(part.Position)
                    if on then
                        local d = (Vector2.new(sp.X, sp.Y) - screenCenter).Magnitude
                        if d < bestDist then
                            if not IsWallBetween(camPos, part.Position, player.Character) then
                                bestTarget = player
                                bestDist = d
                            end
                        end
                    end
                end
            end
        end
    end
    return bestTarget
end

-- Cek apakah target masih valid (belum mati, masih ada)
local function IsTargetValid(target)
    if not target then return false end
    if not target.Parent then return false end
    local char = target.Character
    if not char then return false end
    local hum = char:FindFirstChild("Humanoid")
    if not hum or hum.Health <= 0 then return false end
    return true
end

-- Aimbot utama - SIMPLE + STICKY
local function RunAimbot()
    if not AimbotEnabled or not IsLoggedIn then return end
    if AimbotKeybindEnabled and not AimbotKeyHeld then return end

    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end

    -- Kalau punya sticky target & masih valid → pakai itu (LENGKET!)
    if AimbotStickyTarget and IsTargetValid(AimbotStickyTarget) then
        -- tetap pakai target ini
    else
        -- cari target baru
        AimbotStickyTarget = FindTarget()
    end

    if not AimbotStickyTarget then return end
    if not IsTargetValid(AimbotStickyTarget) then 
        AimbotStickyTarget = nil
        return 
    end

    local targetChar = AimbotStickyTarget.Character
    local targetPart = targetChar:FindFirstChild(AimbotTargetPart) or targetChar:FindFirstChild("HumanoidRootPart")
    if not targetPart then return end

    -- Aim ke target
    local camPos = Camera.CFrame.Position
    local aimPos = targetPart.Position
    local dir = (aimPos - camPos).Unit
    local newCF = CFrame.new(camPos, camPos + dir)

    -- Smooth (makin kecil = makin lengket)
    local smooth = math.clamp(AimbotSmoothness, 1, 20)
    if smooth <= 1 then
        Camera.CFrame = newCF
    else
        Camera.CFrame = Camera.CFrame:Lerp(newCF, 1 / smooth)
    end
end

--==============================================================
-- 📡 RADAR BULAT
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
    RInfo.Text = "🔴 Musuh  🔵 Kamu"
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
        if not RadarEnabled or not IsLoggedIn then return end
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

        for _, d in pairs(RadarObjects) do
            pcall(function() d.dot:Remove() end)
            if d.name then pcall(function() d.name:Remove() end) end
        end
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

                        local isTeam = false
                        if LocalPlayer.Team and player.Team and LocalPlayer.Team == player.Team then
                            isTeam = true
                        end

                        local dot = Drawing.new("Circle")
                        dot.Visible = true
                        dot.Position = screenPos
                        dot.Radius = 3
                        dot.Thickness = 1
                        dot.Filled = true
                        dot.Transparency = 1
                        if isTeam then
                            dot.Color = Color3.fromRGB(0, 255, 100)
                        else
                            dot.Color = THEME.RedDot
                        end

                        local nameObj = nil
                        if RadarShowNames then
                            nameObj = Drawing.new("Text")
                            nameObj.Visible = true
                            nameObj.Position = screenPos + Vector2.new(5, -5)
                            nameObj.Text = player.Name
                            nameObj.Size = 9
                            nameObj.Color = THEME.TextColor
                            nameObj.Center = false
                            nameObj.Outline = true
                            nameObj.OutlineColor = Color3.fromRGB(0, 0, 0)
                        end

                        table.insert(RadarObjects, {dot = dot, name = nameObj})

                        if dist < nearestDist then
                            nearestDist = dist
                            nearestName = player.Name
                        end
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
    for _, d in pairs(RadarObjects) do
        pcall(function() d.dot:Remove() end)
        if d.name then pcall(function() d.name:Remove() end) end
    end
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
        if AimbotEnabled and IsLoggedIn then RunAimbot() end
        if SpeedHackEnabled and IsLoggedIn then ApplySpeedHack() end
        if FOVCircleEnabled then
            FOVCircle.Position = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
            FOVCircle.Radius = AimbotFOV
            FOVCircle.Visible = true
        else
            FOVCircle.Visible = false
        end
    end)
end)

--==============================================================
-- KEYBIND HANDLER
--==============================================================
UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if AimbotKeybindEnabled and input.KeyCode == AimbotKeybind then
        AimbotKeyHeld = true
    end
    if input.KeyCode == FlyVoidKeybind and IsLoggedIn then
        FlyVoidEnabled = not FlyVoidEnabled
        if FlyVoidEnabled then
            EnableFlyVoid()
            Notify("Fly-Void", "> ACTIVE", 2)
        else
            DisableFlyVoid()
            Notify("Fly-Void", "> OFF", 2)
        end
    end
end)

UserInputService.InputEnded:Connect(function(input, gp)
    if input.KeyCode == AimbotKeybind then
        AimbotKeyHeld = false
    end
end)

--==============================================================
-- UI BUILDER (RAPI)
--==============================================================
local function CreateUI()

    --===== LOADING =====
    local LoadingScreen = Instance.new("Frame")
    LoadingScreen.Size = UDim2.new(1, 0, 1, 0)
    LoadingScreen.BackgroundColor3 = Color3.fromRGB(5, 0, 0)
    LoadingScreen.BorderSizePixel = 0
    LoadingScreen.ZIndex = 300
    LoadingScreen.Visible = true
    LoadingScreen.Parent = ScreenGui

    local LoadingBg = Instance.new("Frame")
    LoadingBg.Size = UDim2.new(0, 360, 0, 240)
    LoadingBg.Position = UDim2.new(0.5, -180, 0.5, -120)
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
    LTitle.Text = "ZETGAMES-ADVANSERVER V3.9.1"
    LTitle.TextColor3 = THEME.TextColor
    LTitle.Font = Enum.Font.Code
    LTitle.TextSize = 16
    LTitle.ZIndex = 302
    LTitle.Parent = LoadingBg

    local LTag = Instance.new("TextLabel")
    LTag.Size = UDim2.new(1, -30, 0, 20)
    LTag.Position = UDim2.new(0, 15, 0, 50)
    LTag.BackgroundTransparency = 1
    LTag.Text = "[ V3.9.1 TESTING - FIXED ]"
    LTag.TextColor3 = Color3.fromRGB(255, 200, 0)
    LTag.Font = Enum.Font.Code
    LTag.TextSize = 11
    LTag.ZIndex = 302
    LTag.Parent = LoadingBg

    local LSub = Instance.new("TextLabel")
    LSub.Size = UDim2.new(1, -30, 0, 25)
    LSub.Position = UDim2.new(0, 15, 0, 75)
    LSub.BackgroundTransparency = 1
    LSub.Text = "> INITIALIZING V3.9.1..."
    LSub.TextColor3 = THEME.TextLight
    LSub.Font = Enum.Font.Code
    LSub.TextSize = 12
    LSub.TextXAlignment = Enum.TextXAlignment.Left
    LSub.ZIndex = 302
    LSub.Parent = LoadingBg

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
    LPercent.TextSize = 14
    LPercent.ZIndex = 302
    LPercent.Parent = LoadingBg

    local LStatus = Instance.new("TextLabel")
    LStatus.Size = UDim2.new(1, -30, 0, 20)
    LStatus.Position = UDim2.new(0, 15, 0, 170)
    LStatus.BackgroundTransparency = 1
    LStatus.Text = "> LOADING..."
    LStatus.TextColor3 = THEME.TextLight
    LStatus.Font = Enum.Font.Code
    LStatus.TextSize = 10
    LStatus.TextXAlignment = Enum.TextXAlignment.Left
    LStatus.ZIndex = 302
    LStatus.Parent = LoadingBg

    local LFooterLbl = Instance.new("TextLabel")
    LFooterLbl.Size = UDim2.new(1, -30, 0, 20)
    LFooterLbl.Position = UDim2.new(0, 15, 0, 195)
    LFooterLbl.BackgroundTransparency = 1
    LFooterLbl.Text = "> AIMBOT SIMPLE + STICKY 100%"
    LFooterLbl.TextColor3 = Color3.fromRGB(255, 200, 0)
    LFooterLbl.Font = Enum.Font.Code
    LFooterLbl.TextSize = 9
    LFooterLbl.ZIndex = 302
    LFooterLbl.Parent = LoadingBg

    --===== LOGIN =====
    local LoginFrame = Instance.new("Frame")
    LoginFrame.Size = UDim2.new(0, 340, 0, 450)
    LoginFrame.Position = UDim2.new(0.5, -170, 0.5, -225)
    LoginFrame.BackgroundColor3 = THEME.MainBG
    LoginFrame.BorderColor3 = THEME.AccentColor
    LoginFrame.BorderSizePixel = 2
    LoginFrame.ZIndex = 10
    LoginFrame.Visible = false
    LoginFrame.Parent = ScreenGui
    Instance.new("UICorner", LoginFrame).CornerRadius = UDim.new(0, 10)

    local LTop = Instance.new("Frame")
    LTop.Size = UDim2.new(1, 0, 0, 35)
    LTop.BackgroundColor3 = THEME.SectionBG
    LTop.BorderSizePixel = 0
    LTop.ZIndex = 11
    LTop.Parent = LoginFrame
    Instance.new("UICorner", LTop).CornerRadius = UDim.new(0, 10)

    local LTopTxt = Instance.new("TextLabel")
    LTopTxt.Size = UDim2.new(1, -16, 1, 0)
    LTopTxt.Position = UDim2.new(0, 8, 0, 0)
    LTopTxt.BackgroundTransparency = 1
    LTopTxt.Text = "● ZETGAMES-ADVANSERVER V3.9.1"
    LTopTxt.TextColor3 = THEME.TextColor
    LTopTxt.Font = Enum.Font.Code
    LTopTxt.TextSize = 10
    LTopTxt.TextXAlignment = Enum.TextXAlignment.Left
    LTopTxt.ZIndex = 12
    LTopTxt.Parent = LTop

    local LTitle2 = Instance.new("TextLabel")
    LTitle2.Size = UDim2.new(1, -30, 0, 30)
    LTitle2.Position = UDim2.new(0, 15, 0, 50)
    LTitle2.BackgroundTransparency = 1
    LTitle2.Text = "> ACCESS VERIFICATION"
    LTitle2.TextColor3 = THEME.TextColor
    LTitle2.Font = Enum.Font.Code
    LTitle2.TextSize = 16
    LTitle2.ZIndex = 12
    LTitle2.Parent = LoginFrame

    local LTag2 = Instance.new("TextLabel")
    LTag2.Size = UDim2.new(1, -30, 0, 20)
    LTag2.Position = UDim2.new(0, 15, 0, 85)
    LTag2.BackgroundTransparency = 1
    LTag2.Text = "[ TESTING - NO NIGHT LOCK ]"
    LTag2.TextColor3 = Color3.fromRGB(255, 200, 0)
    LTag2.Font = Enum.Font.Code
    LTag2.TextSize = 10
    LTag2.TextXAlignment = Enum.TextXAlignment.Left
    LTag2.ZIndex = 12
    LTag2.Parent = LoginFrame

    local KeyLbl = Instance.new("TextLabel")
    KeyLbl.Size = UDim2.new(1, -30, 0, 20)
    KeyLbl.Position = UDim2.new(0, 15, 0, 115)
    KeyLbl.BackgroundTransparency = 1
    KeyLbl.Text = "> KEY_INPUT:"
    KeyLbl.TextColor3 = THEME.TextColor
    KeyLbl.Font = Enum.Font.Code
    KeyLbl.TextSize = 12
    KeyLbl.TextXAlignment = Enum.TextXAlignment.Left
    KeyLbl.ZIndex = 12
    KeyLbl.Parent = LoginFrame

    local KeyInput = Instance.new("TextBox")
    KeyInput.Size = UDim2.new(1, -30, 0, 40)
    KeyInput.Position = UDim2.new(0, 15, 0, 140)
    KeyInput.BackgroundColor3 = THEME.PanelBG
    KeyInput.BorderColor3 = THEME.AccentColor
    KeyInput.BorderSizePixel = 2
    KeyInput.PlaceholderText = "> Type key..."
    KeyInput.PlaceholderColor3 = Color3.fromRGB(100, 50, 50)
    KeyInput.Text = ""
    KeyInput.TextColor3 = THEME.TextColor
    KeyInput.Font = Enum.Font.Code
    KeyInput.TextSize = 13
    KeyInput.ZIndex = 12
    KeyInput.Parent = LoginFrame
    Instance.new("UICorner", KeyInput).CornerRadius = UDim.new(0, 5)

    local LoginBtn = Instance.new("TextButton")
    LoginBtn.Size = UDim2.new(1, -30, 0, 45)
    LoginBtn.Position = UDim2.new(0, 15, 0, 195)
    LoginBtn.BackgroundColor3 = THEME.ButtonActive
    LoginBtn.BorderColor3 = THEME.AccentColor
    LoginBtn.BorderSizePixel = 2
    LoginBtn.Text = "> AUTHENTICATE"
    LoginBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    LoginBtn.Font = Enum.Font.Code
    LoginBtn.TextSize = 14
    LoginBtn.ZIndex = 12
    LoginBtn.Parent = LoginFrame
    Instance.new("UICorner", LoginBtn).CornerRadius = UDim.new(0, 5)

    local GetKeyBtn = Instance.new("TextButton")
    GetKeyBtn.Size = UDim2.new(1, -30, 0, 45)
    GetKeyBtn.Position = UDim2.new(0, 15, 0, 255)
    GetKeyBtn.BackgroundColor3 = Color3.fromRGB(60, 0, 0)
    GetKeyBtn.BorderColor3 = THEME.AccentDark
    GetKeyBtn.BorderSizePixel = 2
    GetKeyBtn.Text = "> GET KEY (COPY)"
    GetKeyBtn.TextColor3 = THEME.TextLight
    GetKeyBtn.Font = Enum.Font.Code
    GetKeyBtn.TextSize = 14
    GetKeyBtn.ZIndex = 12
    GetKeyBtn.Parent = LoginFrame
    Instance.new("UICorner", GetKeyBtn).CornerRadius = UDim.new(0, 5)

    local StatusTxt = Instance.new("TextLabel")
    StatusTxt.Size = UDim2.new(1, -30, 0, 25)
    StatusTxt.Position = UDim2.new(0, 15, 0, 315)
    StatusTxt.BackgroundTransparency = 1
    StatusTxt.Text = "> SYSTEM READY..."
    StatusTxt.TextColor3 = THEME.TextColor
    StatusTxt.Font = Enum.Font.Code
    StatusTxt.TextSize = 10
    StatusTxt.TextXAlignment = Enum.TextXAlignment.Left
    StatusTxt.ZIndex = 12
    StatusTxt.Parent = LoginFrame

    local Instr = Instance.new("TextLabel")
    Instr.Size = UDim2.new(1, -30, 0, 80)
    Instr.Position = UDim2.new(0, 15, 0, 350)
    Instr.BackgroundTransparency = 1
    Instr.Text = "> STEPS:\n> 1. Click GET KEY\n> 2. Generate key\n> 3. Enter key\n> 4. AUTHENTICATE"
    Instr.TextColor3 = THEME.TextLight
    Instr.Font = Enum.Font.Code
    Instr.TextSize = 9
    Instr.TextXAlignment = Enum.TextXAlignment.Left
    Instr.ZIndex = 12
    Instr.Parent = LoginFrame

    --===== MAIN HUB =====
    local MainHub = Instance.new("Frame")
    MainHub.Size = UDim2.new(0, 380, 0, 520)
    MainHub.Position = UDim2.new(0.5, -190, 0.5, -260)
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
    MainTitle2.Text = "● V3.9.1 TESTING [FIXED]"
    MainTitle2.TextColor3 = THEME.TextColor
    MainTitle2.Font = Enum.Font.Code
    MainTitle2.TextSize = 12
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
    ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
    ScrollFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y
    ScrollFrame.ZIndex = 11
    ScrollFrame.Parent = MainHub

    local ScrollContent = Instance.new("Frame")
    ScrollContent.Size = UDim2.new(1, 0, 0, 0)
    ScrollContent.BackgroundTransparency = 1
    ScrollContent.AutomaticSize = Enum.AutomaticSize.Y
    ScrollContent.ZIndex = 11
    ScrollContent.Parent = ScrollFrame

    -- Layout helper (VERTICAL PADDING)
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
        btn.Size = UDim2.new(1, -20, 0, 36)
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
        b.BackgroundColor3 = THEME.ButtonActive
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

    --==========================================================
    -- 🎯 AIMBOT SIMPLE (PALING ATAS - USER FRIENDLY)
    --==========================================================
    Section("=== 🎯 AIMBOT SIMPLE (STICKY 100%) ===", 10)

    local AimbotBtn = Instance.new("TextButton")
    AimbotBtn.Size = UDim2.new(1, -20, 0, 45)
    AimbotBtn.Position = UDim2.new(0, 10, 0, 42)
    AimbotBtn.BackgroundColor3 = THEME.ButtonBG
    AimbotBtn.BorderColor3 = THEME.AccentColor
    AimbotBtn.BorderSizePixel = 2
    AimbotBtn.Text = "> AIMBOT: OFF"
    AimbotBtn.TextColor3 = THEME.TextColor
    AimbotBtn.Font = Enum.Font.Code
    AimbotBtn.TextSize = 14
    AimbotBtn.ZIndex = 12
    AimbotBtn.Parent = ScrollContent
    Instance.new("UICorner", AimbotBtn).CornerRadius = UDim.new(0, 5)
    AimbotBtn.MouseButton1Click:Connect(function()
        AimbotEnabled = not AimbotEnabled
        if AimbotEnabled then
            AimbotBtn.Text = "> AIMBOT: ON"
            AimbotBtn.BackgroundColor3 = THEME.ButtonActive
        else
            AimbotBtn.Text = "> AIMBOT: OFF"
            AimbotBtn.BackgroundColor3 = THEME.ButtonBG
            AimbotStickyTarget = nil
        end
    end)

    local AimbotInfo = Instance.new("TextLabel")
    AimbotInfo.Size = UDim2.new(1, -20, 0, 30)
    AimbotInfo.Position = UDim2.new(0, 10, 0, 90)
    AimbotInfo.BackgroundTransparency = 1
    AimbotInfo.Text = "> Cukup ON, auto lock target\n> 100% lengket ke 1 target"
    AimbotInfo.TextColor3 = THEME.TextLight
    AimbotInfo.Font = Enum.Font.Code
    AimbotInfo.TextSize = 9
    AimbotInfo.TextXAlignment = Enum.TextXAlignment.Left
    AimbotInfo.ZIndex = 12
    AimbotInfo.Parent = ScrollContent

    -- Target Part
    local TargetLbl = Instance.new("TextLabel")
    TargetLbl.Size = UDim2.new(1, -20, 0, 18)
    TargetLbl.Position = UDim2.new(0, 10, 0, 125)
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
    TargetBtnFrame.Position = UDim2.new(0, 10, 0, 145)
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

    -- Smoothness
    local SmoothInput = Input("> Kekuatan (1-20) | 1=Paling Lengket", 185, tostring(AimbotSmoothness))
    SmoothInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local ns = tonumber(SmoothInput.Text)
            if ns then AimbotSmoothness = math.clamp(ns, 1, 20) end
            SmoothInput.Text = tostring(AimbotSmoothness)
        end
    end)

    -- FOV
    local FOVInput = Input("> FOV Radius (50-2000)", 222, tostring(AimbotFOV))
    FOVInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nf = tonumber(FOVInput.Text)
            if nf then AimbotFOV = math.clamp(nf, 50, 2000) end
            FOVInput.Text = tostring(AimbotFOV)
        end
    end)

    Toggle("> KEYBIND HOLD (E): OFF", 259, function(btn)
        AimbotKeybindEnabled = not AimbotKeybindEnabled
        btn.Text = AimbotKeybindEnabled and "> KEYBIND HOLD (E): ON" or "> KEYBIND HOLD (E): OFF"
        btn.BackgroundColor3 = AimbotKeybindEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    Half("> TEAM CHECK: OFF", 300, 0, function(btn)
        AimbotTeamCheck = not AimbotTeamCheck
        btn.Text = AimbotTeamCheck and "> TEAM: ON" or "> TEAM: OFF"
        btn.BackgroundColor3 = AimbotTeamCheck and THEME.ButtonActive or THEME.ButtonBG
    end)

    Half("> WALL CHECK: OFF", 300, 0.5, function(btn)
        AimbotWallCheck = not AimbotWallCheck
        btn.Text = AimbotWallCheck and "> WALL: ON" or "> WALL: OFF"
        btn.BackgroundColor3 = AimbotWallCheck and THEME.ButtonActive or THEME.ButtonBG
    end)

    Half("> FOV CIRCLE: OFF", 337, 0, function(btn)
        FOVCircleEnabled = not FOVCircleEnabled
        btn.Text = FOVCircleEnabled and "> FOV CIRCLE: ON" or "> FOV CIRCLE: OFF"
        btn.BackgroundColor3 = FOVCircleEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    Half("> RESET TARGET", 337, 0.5, function(btn)
        AimbotStickyTarget = nil
        Notify("Aimbot", "> TARGET RESET", 2)
    end)

    --==========================================================
    -- 📡 RADAR BULAT
    --==========================================================
    Section("=== 📡 RADAR BULAT ===", 385)

    local RadarBtn = Toggle("> RADAR: OFF", 417, function(btn)
        RadarEnabled = not RadarEnabled
        if RadarEnabled then
            btn.Text = "> RADAR: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            if RadarFrame then RadarFrame.Visible = true end
            StartRadar()
        else
            btn.Text = "> RADAR: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
            if RadarFrame then RadarFrame.Visible = false end
            StopRadar()
        end
    end)

    Half("> ROTATE: ON", 458, 0, function(btn)
        RadarRotateWithCamera = not RadarRotateWithCamera
        btn.Text = RadarRotateWithCamera and "> ROTATE: ON" or "> ROTATE: OFF"
        btn.BackgroundColor3 = RadarRotateWithCamera and THEME.ButtonActive or THEME.ButtonBG
    end)

    Half("> NAMES: OFF", 458, 0.5, function(btn)
        RadarShowNames = not RadarShowNames
        btn.Text = RadarShowNames and "> NAMES: ON" or "> NAMES: OFF"
        btn.BackgroundColor3 = RadarShowNames and THEME.ButtonActive or THEME.ButtonBG
    end)

    local RadarRadInput = Input("> Radius (100-1000)", 495, tostring(RadarRadius))
    RadarRadInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nr = tonumber(RadarRadInput.Text)
            if nr then RadarRadius = math.clamp(nr, 100, 1000) end
            RadarRadInput.Text = tostring(RadarRadius)
        end
    end)

    local RadarZoomInput = Input("> Zoom (0.5-3.0)", 532, tostring(RadarZoom))
    RadarZoomInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nz = tonumber(RadarZoomInput.Text)
            if nz then RadarZoom = math.clamp(nz, 0.5, 3.0) end
            RadarZoomInput.Text = tostring(RadarZoom)
        end
    end)

    --==========================================================
    -- 🕳️ FLY-VOID
    --==========================================================
    Section("=== 🕳️ FLY-VOID ===", 580)

    local FlyVoidBtn = Toggle("> FLY-VOID: OFF (V)", 612, function(btn)
        FlyVoidEnabled = not FlyVoidEnabled
        if FlyVoidEnabled then
            btn.Text = "> FLY-VOID: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            EnableFlyVoid()
            Notify("Fly-Void", "> ACTIVE (V)", 2)
        else
            btn.Text = "> FLY-VOID: OFF (V)"
            btn.BackgroundColor3 = THEME.ButtonBG
            DisableFlyVoid()
        end
    end)

    Half("> HIDE MODE: ON", 653, 0, function(btn)
        FlyVoidHideMode = not FlyVoidHideMode
        btn.Text = FlyVoidHideMode and "> HIDE: ON" or "> HIDE: OFF"
        btn.BackgroundColor3 = FlyVoidHideMode and THEME.ButtonActive or THEME.ButtonBG
    end)

    Half("> INFO", 653, 0.5, function(btn)
        Notify("Fly-Void", "> Jalan bawah map + tembus", 3)
    end)

    --==========================================================
    -- ESP
    --==========================================================
    Section("=== 👁️ ESP ===", 700)

    Toggle("> ESP MASTER: OFF", 732, function(btn)
        ESPEnabled = not ESPEnabled
        btn.Text = ESPEnabled and "> ESP MASTER: ON" or "> ESP MASTER: OFF"
        btn.BackgroundColor3 = ESPEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    Half("> BOX: ON", 773, 0, function(btn)
        ESPBoxEnabled = not ESPBoxEnabled
        btn.Text = ESPBoxEnabled and "> BOX: ON" or "> BOX: OFF"
        btn.BackgroundColor3 = ESPBoxEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    Half("> NAME: ON", 773, 0.5, function(btn)
        ESPNameEnabled = not ESPNameEnabled
        btn.Text = ESPNameEnabled and "> NAME: ON" or "> NAME: OFF"
        btn.BackgroundColor3 = ESPNameEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    Half("> DISTANCE: ON", 810, 0, function(btn)
        ESPDistanceEnabled = not ESPDistanceEnabled
        btn.Text = ESPDistanceEnabled and "> DISTANCE: ON" or "> DISTANCE: OFF"
        btn.BackgroundColor3 = ESPDistanceEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    Half("> HEALTH: ON", 810, 0.5, function(btn)
        ESPHealthEnabled = not ESPHealthEnabled
        btn.Text = ESPHealthEnabled and "> HEALTH: ON" or "> HEALTH: OFF"
        btn.BackgroundColor3 = ESPHealthEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    Toggle("> TRACER: ON", 847, function(btn)
        ESPTracerEnabled = not ESPTracerEnabled
        btn.Text = ESPTracerEnabled and "> TRACER: ON" or "> TRACER: OFF"
        btn.BackgroundColor3 = ESPTracerEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    --==========================================================
    -- MAIN FEATURES
    --==========================================================
    Section("=== ⚙️ MAIN FEATURES ===", 895)

    Toggle("> FPS BOOST: OFF", 927, function(btn)
        FPSBoostEnabled = not FPSBoostEnabled
        btn.Text = FPSBoostEnabled and "> FPS BOOST: ON" or "> FPS BOOST: OFF"
        btn.BackgroundColor3 = FPSBoostEnabled and THEME.ButtonActive or THEME.ButtonBG
        if FPSBoostEnabled then EnableFPSBoost() else DisableFPSBoost() end
    end)

    Toggle("> FULLBRIGHT: OFF", 968, function(btn)
        FullbrightEnabled = not FullbrightEnabled
        btn.Text = FullbrightEnabled and "> FULLBRIGHT: ON" or "> FULLBRIGHT: OFF"
        btn.BackgroundColor3 = FullbrightEnabled and THEME.ButtonActive or THEME.ButtonBG
        if FullbrightEnabled then EnableFullbright() else DisableFullbright() end
    end)

    Toggle("> SPEED HACK: OFF", 1009, function(btn)
        SpeedHackEnabled = not SpeedHackEnabled
        btn.Text = SpeedHackEnabled and "> SPEED HACK: ON" or "> SPEED HACK: OFF"
        btn.BackgroundColor3 = SpeedHackEnabled and THEME.ButtonActive or THEME.ButtonBG
        ApplySpeedHack()
    end)

    local SpeedInput = Input("> Speed (16-500)", 1046, tostring(SpeedMultiplier))
    SpeedInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local ns = tonumber(SpeedInput.Text)
            if ns then SpeedMultiplier = math.clamp(ns, 16, MaxSpeed) end
            SpeedInput.Text = tostring(SpeedMultiplier)
            if SpeedHackEnabled then ApplySpeedHack() end
        end
    end)

    Toggle("> INFINITE JUMP: OFF", 1083, function(btn)
        InfiniteJumpEnabled = not InfiniteJumpEnabled
        btn.Text = InfiniteJumpEnabled and "> INFINITE JUMP: ON" or "> INFINITE JUMP: OFF"
        btn.BackgroundColor3 = InfiniteJumpEnabled and THEME.ButtonActive or THEME.ButtonBG
        if InfiniteJumpEnabled then EnableInfiniteJump() else DisableInfiniteJump() end
    end)

    Toggle("> NOCLIP: OFF", 1124, function(btn)
        NoclipEnabled = not NoclipEnabled
        btn.Text = NoclipEnabled and "> NOCLIP: ON" or "> NOCLIP: OFF"
        btn.BackgroundColor3 = NoclipEnabled and THEME.ButtonActive or THEME.ButtonBG
        if NoclipEnabled then EnableNoclip() else DisableNoclip() end
    end)

    --==========================================================
    -- SURVIVAL
    --==========================================================
    Section("=== 🛡️ SURVIVAL ===", 1172)

    Toggle("> AUTO RESPAWN: OFF", 1204, function(btn)
        AutoRespawnEnabled = not AutoRespawnEnabled
        btn.Text = AutoRespawnEnabled and "> AUTO RESPAWN: ON" or "> AUTO RESPAWN: OFF"
        btn.BackgroundColor3 = AutoRespawnEnabled and THEME.ButtonActive or THEME.ButtonBG
        if AutoRespawnEnabled then StartAutoRespawn() else StopAutoRespawn() end
    end)

    Toggle("> ANTI-FLING: OFF", 1245, function(btn)
        AntiFlingEnabled = not AntiFlingEnabled
        btn.Text = AntiFlingEnabled and "> ANTI-FLING: ON" or "> ANTI-FLING: OFF"
        btn.BackgroundColor3 = AntiFlingEnabled and THEME.ButtonActive or THEME.ButtonBG
        if AntiFlingEnabled then StartAntiFling() else StopAntiFling() end
    end)

    --==========================================================
    -- SOUND & MUSIC
    --==========================================================
    Section("=== 🔊 SOUND & MUSIC ===", 1293)

    Toggle("> SOUND ESP: OFF", 1325, function(btn)
        SoundESPEnabled = not SoundESPEnabled
        btn.Text = SoundESPEnabled and "> SOUND ESP: ON" or "> SOUND ESP: OFF"
        btn.BackgroundColor3 = SoundESPEnabled and THEME.ButtonActive or THEME.ButtonBG
        if SoundESPEnabled then StartSoundESP() else StopSoundESP() end
    end)

    local SndInput = Input("> Sound Radius (10-500)", 1366, tostring(SoundESPRadius))
    SndInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nr = tonumber(SndInput.Text)
            if nr then SoundESPRadius = math.clamp(nr, 10, 500) end
            SndInput.Text = tostring(SoundESPRadius)
        end
    end)

    Toggle("> MUSIC PLAYER: OFF", 1403, function(btn)
        MusicPlayerEnabled = not MusicPlayerEnabled
        btn.Text = MusicPlayerEnabled and "> MUSIC PLAYER: ON" or "> MUSIC PLAYER: OFF"
        btn.BackgroundColor3 = MusicPlayerEnabled and THEME.ButtonActive or THEME.ButtonBG
        if MusicPlayerEnabled then StartMusic() else StopMusic() end
    end)

    local MusicInput = Input("> Sound ID", 1444, MusicID)
    MusicInput.FocusLost:Connect(function(enterPressed)
        if enterPressed and MusicInput.Text ~= "" then
            MusicID = MusicInput.Text
            UpdateMusic()
        end
    end)

    --==========================================================
    -- CHAT SPAM
    --==========================================================
    Section("=== 💬 CHAT SPAM ===", 1491)

    Toggle("> CHAT SPAM: OFF", 1523, function(btn)
        ChatSpamEnabled = not ChatSpamEnabled
        btn.Text = ChatSpamEnabled and "> CHAT SPAM: ON" or "> CHAT SPAM: OFF"
        btn.BackgroundColor3 = ChatSpamEnabled and THEME.ButtonActive or THEME.ButtonBG
        if ChatSpamEnabled then StartChatSpamLoop() end
    end)

    local ChatInput = Input("> Message spam", 1564, ChatSpamText)
    ChatInput.FocusLost:Connect(function(enterPressed)
        if enterPressed and ChatInput.Text ~= "" then ChatSpamText = ChatInput.Text end
    end)

    --==========================================================
    -- TELEPORT
    --==========================================================
    Section("=== 🌐 TELEPORT ===", 1611)

    Toggle("> TELEPORT TO MOUSE", 1643, function(btn)
        TeleportToMouse()
    end)

    Half("> SAVE LOC", 1684, 0, function(btn) SaveLocation() end)
    Half("> LOAD LOC", 1684, 0.5, function(btn) LoadLocation() end)

    --==========================================================
    -- USER INFORMATION
    --==========================================================
    Section("=== 👤 USER INFO ===", 1732)

    local UIF = Instance.new("Frame")
    UIF.Size = UDim2.new(1, -20, 0, 80)
    UIF.Position = UDim2.new(0, 10, 0, 1764)
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
    BuildLbl.Text = "> BUILD: V3.9.1 TESTING"
    BuildLbl.TextColor3 = Color3.fromRGB(255, 200, 0)
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

    --==========================================================
    -- TOGGLE MENU BUTTON
    --==========================================================
    local ToggleMenuButton = Instance.new("TextButton")
    ToggleMenuButton.Size = UDim2.new(0, 45, 0, 45)
    ToggleMenuButton.Position = UDim2.new(0, 8, 0.5, -22)
    ToggleMenuButton.BackgroundColor3 = THEME.ButtonActive
    ToggleMenuButton.BorderColor3 = THEME.AccentColor
    ToggleMenuButton.BorderSizePixel = 2
    ToggleMenuButton.Text = "≡"
    ToggleMenuButton.TextColor3 = THEME.TextColor
    ToggleMenuButton.Font = Enum.Font.Code
    ToggleMenuButton.TextSize = 22
    ToggleMenuButton.ZIndex = 15
    ToggleMenuButton.Visible = false
    ToggleMenuButton.Parent = ScreenGui
    Instance.new("UICorner", ToggleMenuButton).CornerRadius = UDim.new(0, 22)

    local function ToggleMenuFunc()
        if not IsLoggedIn then return end
        MenuVisible = not MenuVisible
        MainHub.Visible = MenuVisible
    end

    ToggleMenuButton.MouseButton1Click:Connect(ToggleMenuFunc)
    CloseBtn.MouseButton1Click:Connect(function()
        MenuVisible = false
        MainHub.Visible = false
    end)

    UserInputService.InputBegan:Connect(function(input, gp)
        if gp then return end
        if input.KeyCode == MenuKey and IsLoggedIn then
            ToggleMenuFunc()
        end
    end)

    -- Drag
    local function MakeDraggable(frame)
        local dragging = false
        local dragInput, dragStart, startPos
        frame.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = true
                dragStart = input.Position
                startPos = frame.Position
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

    MakeDraggable(LoginFrame)
    MakeDraggable(MainHub)

    -- LOGIN LOGIC
    LoginBtn.MouseButton1Click:Connect(function()
        local key = KeyInput.Text
        local keyData = ValidKeys[key]
        if keyData then
            if keyData.Expiry == 0 or os.time() < keyData.Expiry then
                IsLoggedIn = true
                LoginFrame.Visible = false
                MainHub.Visible = true
                ToggleMenuButton.Visible = true
                MenuVisible = true
                StatusTxt.Text = "> ACCESS GRANTED..."
                Notify("Success", "> WELCOME V3.9.1", 3)
                Notify("Fix", "> Aimbot Sticky 100%", 3)
            else
                StatusTxt.Text = "> ERROR: KEY EXPIRED"
                Notify("Failed", "> KEY EXPIRED", 2)
            end
        else
            StatusTxt.Text = "> ERROR: INVALID KEY"
            Notify("Failed", "> INVALID KEY", 2)
        end
    end)

    GetKeyBtn.MouseButton1Click:Connect(function()
        if setclipboard then
            setclipboard(KeyWebsite)
            Notify("Key", "> LINK COPIED", 2)
        else
            Notify("Key", "> " .. KeyWebsite, 3)
        end
    end)

    -- LOADING ANIMATION
    local loadingMessages = {
        "> LOADING V3.9.1...",
        "> INITIALIZING AIMBOT...",
        "> STICKY LOCK 100%...",
        "> LOADING RADAR...",
        "> INITIALIZING FLY-VOID...",
        "> LOADING ESP...",
        "> LOADING SOUND...",
        "> FINALIZING...",
        "> TESTING - NO NIGHT LOCK...",
        "> SYSTEM READY!"
    }

    task.spawn(function()
        for i = 1, 100 do
            task.wait(0.08)
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
        LoginFrame.Visible = true
        Notify("V3.9.1 TESTING", "> FIXED + STICKY 100%", 3)
        Notify("Login", "> ENTER ACCESS KEY", 3)
    end)

end

--==============================================================
-- RUN
--==============================================================
pcall(CreateUI)
pcall(CreateRadarUI)
