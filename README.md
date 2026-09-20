--[[
    ZetGames-AimLock-Advanserver V3.9 | TESTING BUILD
    Theme: Red & Black Hacker Style
    New: Aimbot V2 (Sticky+Stealth) + Radar Bulat + Fly-Void
    Night Lock: REMOVED (Testing Build)
    All Toggles Working - NO BUG
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
-- THEME COLORS (RED & BLACK)
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
local AimbotEnabled = false
local FOVRadius = 150
local MaxFOVRadius = 5000
local Smoothness = 2
local TargetPart = "Head"
local AimbotTarget = nil
local MenuVisible = true
local MenuKey = Enum.KeyCode.RightControl
local IsLoggedIn = false
local WallCheckEnabled = false
local TeamCheckEnabled = false
local PredictionEnabled = false
local PredictionAmount = 5
local FOVCircleEnabled = false
local AimbotMode = "Accurate"

-- 🆕 AIMBOT V2
local StickyLockEnabled = true
local MicroSmoothEnabled = true
local HumanCurveEnabled = true
local RandomJitterEnabled = true
local StealthDelayEnabled = true
local StealthDelayMs = 100
local AimbotKeybindEnabled = false
local AimbotKeybind = Enum.KeyCode.E
local AimbotKeyHeld = false
local StickyMemory = nil
local StickyMemoryTime = 0
local StickyMemoryDuration = 1.5
local MicroOffset = Vector2.new(0, 0)

local ESPEnabled = false
local ESPBoxEnabled = true
local ESPNameEnabled = true
local ESPDistanceEnabled = true
local ESPHealthEnabled = true
local ESPSkeletonEnabled = false
local ESPHeadDotEnabled = false
local ESPChamsEnabled = false
local ESPTracerEnabled = true
local ESPObjects = {}
local ChamsObjects = {}

local RainbowESPEnabled = false
local RainbowConnection = nil
local RainbowHue = 0

local TracerColor = Color3.fromRGB(255, 0, 0)
local TracerColorName = "MERAH"

local FullbrightEnabled = false
local OriginalLighting = {}

local ChatSpamEnabled = false
local ChatSpamText = "ZETGAMES-AIMLOCK-ADVANSERVER"
local ChatSpamDelay = 3

-- SOUND ESP
local SoundESPEnabled = false
local SoundESPRadius = 100
local SoundESPConnection = nil
local SoundESPBeep = nil
local LastBeepTime = 0
local SoundESPSoundID = "rbxassetid://4790566870"

-- MUSIC PLAYER
local MusicPlayerEnabled = false
local MusicSound = nil
local MusicID = "1837879082"
local MusicVolume = 1

-- SURVIVAL
local AutoRespawnEnabled = false
local AutoRespawnConnection = nil
local LookAtEnabled = false
local AntiFlingEnabled = false
local AntiFlingConnection = nil

-- 🆕 FLY-VOID
local FlyVoidEnabled = false
local FlyVoidConnection = nil
local FlyVoidHeight = -60
local FlyVoidHideMode = true
local FlyVoidWallShoot = true
local FlyVoidKeybind = Enum.KeyCode.V
local OriginalCollision = {}
local OriginalTransparency = {}

local TeleportTargetList = {}

local NoclipEnabled = false
local NoclipConnection = nil

local SpeedHackEnabled = false
local SpeedMultiplier = 100
local MaxSpeed = 500
local DefaultWalkSpeed = 16

local InfiniteJumpEnabled = false
local JumpConnection = nil

local SavedLocation = nil

local FPSBoostEnabled = false
local OriginalSettings = {}

-- 🆕 RADAR BULAT
local RadarEnabled = false
local RadarRadius = 300
local RadarZoom = 1.0
local RadarRotateWithCamera = true
local RadarShowNames = false
local RadarConnection = nil
local RadarObjects = {}
local RadarCenter = Vector2.new(0, 0)
local RadarSize = 200
local RadarFrame = nil
local RadarDrawingBg = nil
local RadarDrawingBorder = nil
local RadarDrawingCenterDot = nil
local RadarDrawingCompass = {}
local RadarNearestLabel = nil

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
ScreenGui.Name = "ZetGamesAimLockAdvanserver"
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
    OriginalLighting.FogStart = Lighting.FogStart
    OriginalLighting.GlobalShadows = Lighting.GlobalShadows
    Lighting.Ambient = Color3.fromRGB(255, 255, 255)
    Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
    Lighting.Brightness = 3
    Lighting.ClockTime = 12
    Lighting.FogEnd = 100000
    Lighting.FogStart = 0
    Lighting.GlobalShadows = false
    for _, child in pairs(Lighting:GetChildren()) do
        if child:IsA("Atmosphere") then
            OriginalLighting.Atmosphere = child
            child.Density = 0
            child.Haze = 0
            child.Glare = 0
        end
    end
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
        if OriginalLighting.Atmosphere then
            OriginalLighting.Atmosphere.Density = 0.3
        end
    end)
end

--==============================================================
-- SOUND ESP
--==============================================================
local function StartSoundESP()
    if SoundESPBeep then pcall(function() SoundESPBeep:Destroy() end) end
    SoundESPBeep = Instance.new("Sound")
    SoundESPBeep.SoundId = SoundESPSoundID
    SoundESPBeep.Volume = 1
    SoundESPBeep.Looped = false
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
                    local vol = math.clamp(1 - (nearestDist / SoundESPRadius) + 0.3, 0.3, 1)
                    SoundESPBeep.Volume = vol
                    SoundESPBeep.PlaybackSpeed = 1 + (1 - nearestDist / SoundESPRadius) * 0.5
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
-- MUSIC PLAYER
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
-- 🆕 FLY-VOID (Anti-Void Upgrade)
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

        -- Cek target Y
        local targetY = FlyVoidHeight
        local pos = root.Position

        -- Cek apakah ada map di bawah (raycast)
        local rayParams = RaycastParams.new()
        rayParams.FilterType = Enum.RaycastFilterType.Blacklist
        rayParams.FilterDescendantsInstances = {char}
        local ray = workspace:Raycast(Vector3.new(pos.X, 1000, pos.Z), Vector3.new(0, -2000, 0), rayParams)

        local floorY = -60
        if ray and ray.Position then
            -- Jika ada map, kita mau di bawah map
            floorY = ray.Position.Y - 5
        end

        -- Cek apakah kita sudah di bawah map
        local groundRay = workspace:Raycast(Vector3.new(pos.X, pos.Y, pos.Z), Vector3.new(0, -10, 0), rayParams)
        local isBelowMap = (groundRay == nil) or (pos.Y < floorY + 10)

        -- Anti-void: Kalau Y < -50 (jatuh), auto stuck
        if pos.Y < -30 then
            -- Auto naik ke posisi Y stabil
            pcall(function()
                root.CFrame = CFrame.new(pos.X, floorY, pos.Z)
                root.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            end)
        end

        -- Anti-kill void: HP tidak boleh 0 karena void
        if hum.Health <= 0 then
            pcall(function() LocalPlayer:LoadCharacter() end)
        end

        -- Hide mode: transparan saat di bawah map
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

        -- Anti-detect: stabilize posisi Y (tidak osilasi)
        if math.abs(root.AssemblyLinearVelocity.Y) > 50 then
            root.AssemblyLinearVelocity = Vector3.new(root.AssemblyLinearVelocity.X, 0, root.AssemblyLinearVelocity.Z)
        end
    end)
end

local function DisableFlyVoid()
    if FlyVoidConnection then FlyVoidConnection:Disconnect(); FlyVoidConnection = nil end
    -- Restore transparency
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
-- ANTI-FLING
--==============================================================
local function StartAntiFling()
    if AntiFlingConnection then AntiFlingConnection:Disconnect() end
    AntiFlingConnection = RunService.Heartbeat:Connect(function()
        if not AntiFlingEnabled or not IsLoggedIn then return end
        if LocalPlayer.Character then
            local root = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            if root then
                local vel = root.AssemblyLinearVelocity
                if vel.Magnitude > 500 then
                    pcall(function() root.AssemblyLinearVelocity = Vector3.new(0, 0, 0) end)
                end
            end
        end
    end)
end

local function StopAntiFling()
    if AntiFlingConnection then AntiFlingConnection:Disconnect(); AntiFlingConnection = nil end
end

--==============================================================
-- RAINBOW
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

--==============================================================
-- 🆕 RADAR BULAT
--==============================================================
local function CreateRadarUI()
    -- Frame container (untuk drag + tampil label)
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

    -- Title
    local RTitle = Instance.new("TextLabel")
    RTitle.Size = UDim2.new(1, 0, 0, 22)
    RTitle.Position = UDim2.new(0, 0, 0, 0)
    RTitle.BackgroundColor3 = THEME.SectionBG
    RTitle.BorderSizePixel = 0
    RTitle.Text = "📡 RADAR"
    RTitle.TextColor3 = THEME.TextColor
    RTitle.Font = Enum.Font.Code
    RTitle.TextSize = 12
    RTitle.ZIndex = 21
    RTitle.Parent = RadarFrame
    Instance.new("UICorner", RTitle).CornerRadius = UDim.new(0, 10)

    -- Jarak terdekat
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

    -- Center dari radar akan dihitung di update

    -- Info bawah
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

    -- Drag handle
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
        local myRoot = LocalPlayer.Character.HumanoidRootPart

        -- Hitung center radar (di dalam frame)
        local radarCenterScreen = RadarFrame.AbsolutePosition + Vector2.new(RadarFrame.AbsoluteSize.X / 2, 140)
        RadarCenter = radarCenterScreen
        local r = 90 -- radius visual dalam pixel

        -- Border
        if RadarDrawingBorder then
            pcall(function() RadarDrawingBorder:Remove() end)
            RadarDrawingBorder = nil
        end
        RadarDrawingBorder = Drawing.new("Circle")
        RadarDrawingBorder.Visible = true
        RadarDrawingBorder.Position = RadarCenter
        RadarDrawingBorder.Radius = r
        RadarDrawingBorder.Thickness = 2
        RadarDrawingBorder.Color = THEME.AccentColor
        RadarDrawingBorder.Filled = false
        RadarDrawingBorder.Transparency = 1

        -- Background circle
        if RadarDrawingBg then
            pcall(function() RadarDrawingBg:Remove() end)
            RadarDrawingBg = nil
        end
        RadarDrawingBg = Drawing.new("Circle")
        RadarDrawingBg.Visible = true
        RadarDrawingBg.Position = RadarCenter
        RadarDrawingBg.Radius = r
        RadarDrawingBg.Thickness = 1
        RadarDrawingBg.Color = Color3.fromRGB(30, 0, 0)
        RadarDrawingBg.Filled = true
        RadarDrawingBg.Transparency = 0.8

        -- Center dot (kamu - biru)
        if RadarDrawingCenterDot then
            pcall(function() RadarDrawingCenterDot:Remove() end)
            RadarDrawingCenterDot = nil
        end
        RadarDrawingCenterDot = Drawing.new("Circle")
        RadarDrawingCenterDot.Visible = true
        RadarDrawingCenterDot.Position = RadarCenter
        RadarDrawingCenterDot.Radius = 4
        RadarDrawingCenterDot.Thickness = 1
        RadarDrawingCenterDot.Color = THEME.BlueDot
        RadarDrawingCenterDot.Filled = true
        RadarDrawingCenterDot.Transparency = 1

        -- Compass N
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
            txt.Position = RadarCenter + Vector2.new(nx, ny)
            txt.Text = letter
            txt.Size = 10
            txt.Color = THEME.TextColor
            txt.Center = true
            txt.Outline = true
            txt.OutlineColor = Color3.fromRGB(0, 0, 0)
            table.insert(RadarDrawingCompass, txt)
        end

        -- Camera rotation
        local camCF = Camera.CFrame
        local camYaw = math.atan2(camCF.LookVector.X, camCF.LookVector.Z)

        -- Hapus titik lama
        for _, d in pairs(RadarObjects) do
            pcall(function() d.dot:Remove() end)
            if d.name then pcall(function() d.name:Remove() end) end
        end
        RadarObjects = {}

        local nearestDist = math.huge
        local nearestName = "--"

        -- Loop player
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("Humanoid") then
                local hum = player.Character.Humanoid
                if hum.Health > 0 then
                    local tPos = player.Character.HumanoidRootPart.Position
                    local delta = tPos - myPos
                    local dist = Vector3.new(delta.X, 0, delta.Z).Magnitude

                    if dist <= RadarRadius then
                        -- Hitung sudut relatif terhadap kamera
                        local worldAng = math.atan2(delta.X, delta.Z)
                        local relAng = worldAng - camYaw
                        if RadarRotateWithCamera then
                            -- sudah pakai relAng
                        else
                            relAng = worldAng
                        end

                        -- Konversi ke posisi radar
                        local px = math.sin(relAng) * (dist / RadarRadius) * r * RadarZoom
                        local py = -math.cos(relAng) * (dist / RadarRadius) * r * RadarZoom
                        local screenPos = RadarCenter + Vector2.new(px, py)

                        -- Cek tim
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

        -- Update label
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
-- CHAT SPAM
--==============================================================
local function SendChatMessage(message)
    local sent = false
    pcall(function()
        local chatEvents = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents")
        if chatEvents then
            local sayReq = chatEvents:FindFirstChild("SayMessageRequest")
            if sayReq then sayReq:FireServer(message, "All"); sent = true end
        end
    end)
    if not sent then
        pcall(function()
            local channels = TextChatService:FindFirstChild("TextChannels")
            if channels then
                local gen = channels:FindFirstChild("RBXGeneral")
                if gen then gen:SendAsync(message); sent = true end
            end
        end)
    end
    return sent
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
-- FPS BOOSTER
--==============================================================
local function EnableFPSBoost()
    OriginalSettings.Shadows = Lighting.GlobalShadows
    OriginalSettings.FogEnd = Lighting.FogEnd
    OriginalSettings.Brightness = Lighting.Brightness
    OriginalSettings.EnvironmentDiffuseScale = Lighting.EnvironmentDiffuseScale
    OriginalSettings.EnvironmentSpecularScale = Lighting.EnvironmentSpecularScale
    OriginalSettings.Outlines = Lighting.Outlines
    Lighting.GlobalShadows = false
    Lighting.FogEnd = 100000
    Lighting.Brightness = 2
    Lighting.EnvironmentDiffuseScale = 0
    Lighting.EnvironmentSpecularScale = 0
    Lighting.Outlines = false
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
        if OriginalSettings.EnvironmentDiffuseScale ~= nil then Lighting.EnvironmentDiffuseScale = OriginalSettings.EnvironmentDiffuseScale end
        if OriginalSettings.EnvironmentSpecularScale ~= nil then Lighting.EnvironmentSpecularScale = OriginalSettings.EnvironmentSpecularScale end
        if OriginalSettings.Outlines ~= nil then Lighting.Outlines = OriginalSettings.Outlines end
    end)
end

--==============================================================
-- TELEPORT
--==============================================================
local function TeleportToPlayer(targetPlayer)
    if not targetPlayer or not targetPlayer.Character then
        Notify("Teleport", "> TARGET INVALID", 2); return
    end
    local tR = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
    local lR = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not tR or not lR then Notify("Teleport", "> ROOT NOT FOUND", 2); return end
    pcall(function()
        lR.CFrame = CFrame.new(tR.Position + Vector3.new(0, 3, 0))
        Notify("Teleport", "> TO: " .. targetPlayer.Name, 2)
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
-- NOCLIP
--==============================================================
local function EnableNoclip()
    if NoclipConnection then NoclipConnection:Disconnect() end
    NoclipConnection = RunService.Stepped:Connect(function()
        if NoclipEnabled and LocalPlayer.Character then
            for _, p in pairs(LocalPlayer.Character:GetDescendants()) do
                if p:IsA("BasePart") and p.CanCollide then p.CanCollide = false end
            end
        end
    end)
end

local function DisableNoclip()
    if NoclipConnection then NoclipConnection:Disconnect(); NoclipConnection = nil end
    if LocalPlayer.Character then
        for _, p in pairs(LocalPlayer.Character:GetDescendants()) do
            if p:IsA("BasePart") then p.CanCollide = true end
        end
    end
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
-- ESP
--==============================================================
local function CreateESP(player)
    if ESPObjects[player] then return end
    local d = {}
    d.Box = Drawing.new("Square"); d.Box.Visible = false; d.Box.Color = THEME.AccentColor; d.Box.Thickness = 2; d.Box.Filled = false; d.Box.Transparency = 1
    d.Name = Drawing.new("Text"); d.Name.Visible = false; d.Name.Color = THEME.AccentColor; d.Name.Size = 12; d.Name.Center = true; d.Name.Outline = true; d.Name.OutlineColor = Color3.fromRGB(0,0,0)
    d.Distance = Drawing.new("Text"); d.Distance.Visible = false; d.Distance.Color = THEME.AccentColor; d.Distance.Size = 10; d.Distance.Center = true; d.Distance.Outline = true; d.Distance.OutlineColor = Color3.fromRGB(0,0,0)
    d.HealthBg = Drawing.new("Line"); d.HealthBg.Visible = false; d.HealthBg.Color = Color3.fromRGB(80,0,0); d.HealthBg.Thickness = 3; d.HealthBg.Transparency = 1
    d.HealthBar = Drawing.new("Line"); d.HealthBar.Visible = false; d.HealthBar.Color = THEME.AccentColor; d.HealthBar.Thickness = 3; d.HealthBar.Transparency = 1
    d.Tracer = Drawing.new("Line"); d.Tracer.Visible = false; d.Tracer.Color = TracerColor; d.Tracer.Thickness = 2; d.Tracer.Transparency = 0.5
    d.HeadDot = Drawing.new("Circle"); d.HeadDot.Visible = false; d.HeadDot.Color = Color3.fromRGB(255,0,0); d.HeadDot.Thickness = 1; d.HeadDot.Radius = 4; d.HeadDot.Filled = true; d.HeadDot.Transparency = 1
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
                        if not RainbowESPEnabled then d.Tracer.Color = TracerColor end
                        d.Tracer.From = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y)
                        d.Tracer.To = Vector2.new(sp.X, sp.Y)
                    end
                    d.HeadDot.Visible = false
                    if ESPHeadDotEnabled then
                        local head = player.Character:FindFirstChild("Head")
                        if head then
                            local hp, ho = Camera:WorldToViewportPoint(head.Position)
                            if ho then d.HeadDot.Visible = true; d.HeadDot.Position = Vector2.new(hp.X, hp.Y) end
                        end
                    end
                    if ESPChamsEnabled then
                        if not ChamsObjects[player] then CreateChams(player) end
                    else
                        if ChamsObjects[player] then RemoveChams(player) end
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
-- COMBAT
--==============================================================
local function IsSameTeam(player)
    if not TeamCheckEnabled then return false end
    if LocalPlayer.Team and player.Team and LocalPlayer.Team == player.Team then return true end
    if LocalPlayer.TeamColor and player.TeamColor and LocalPlayer.TeamColor == player.TeamColor then return true end
    return false
end

local function IsWallBetween(origin, target, targetChar)
    if not WallCheckEnabled then return false end
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

--==============================================================
-- 🆕 AIMBOT V2 - STICKY + STEALTH
--==============================================================
local function GetClosestTarget()
    local closest, closestDist = nil, FOVRadius
    local screenCenter = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
    local camPos = Camera.CFrame.Position
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
            if IsSameTeam(player) then continue end
            local tPart = player.Character:FindFirstChild(TargetPart) or player.Character:FindFirstChild("HumanoidRootPart")
            if tPart then
                local tPos = tPart.Position
                if PredictionEnabled then
                    local vel = player.Character.HumanoidRootPart.AssemblyLinearVelocity
                    local d = (tPos - camPos).Magnitude
                    local pf = 1 + (d/500)
                    local t = (d/300) * (PredictionAmount/5) * pf
                    tPos = tPos + vel * t
                end
                local sp, on = Camera:WorldToViewportPoint(tPos)
                local d = (Vector2.new(sp.X, sp.Y) - screenCenter).Magnitude
                if on and d < closestDist then
                    if WallCheckEnabled then
                        if not IsWallBetween(camPos, tPart.Position, player.Character) then
                            closest = player; closestDist = d
                        end
                    else
                        closest = player; closestDist = d
                    end
                end
            end
        end
    end
    return closest, closestDist
end

local function GetTargetPosition(player)
    if not player or not player.Character then return nil end
    local tPart = player.Character:FindFirstChild(TargetPart) or player.Character:FindFirstChild("HumanoidRootPart")
    if not tPart then return nil end
    local tPos = tPart.Position
    if PredictionEnabled then
        local root = player.Character:FindFirstChild("HumanoidRootPart")
        if root then
            local vel = root.AssemblyLinearVelocity
            local d = (tPos - Camera.CFrame.Position).Magnitude
            local pf = 1 + (d/500)
            local t = (d/300) * (PredictionAmount/5) * pf
            tPos = tPos + vel * t
        end
    end
    return tPos
end

local function AimbotFunction()
    if not AimbotEnabled or not IsLoggedIn then return end

    -- Keybind check
    if AimbotKeybindEnabled and not AimbotKeyHeld then
        return
    end

    -- Sticky Lock Logic
    local target = nil
    if StickyLockEnabled and StickyMemory then
        -- Cek apakah masih valid
        local memChar = StickyMemory.Character
        if memChar and memChar:FindFirstChild("Humanoid") and memChar.Humanoid.Health > 0 then
            local memRoot = memChar:FindFirstChild(TargetPart) or memChar:FindFirstChild("HumanoidRootPart")
            if memRoot then
                local sp, on = Camera:WorldToViewportPoint(memRoot.Position)
                if on then
                    local screenCenter = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
                    local d = (Vector2.new(sp.X, sp.Y) - screenCenter).Magnitude
                    if d < FOVRadius * 1.5 then
                        target = StickyMemory
                        StickyMemoryTime = tick()
                    end
                end
            end
        end

        -- Cek memory expiry
        if not target and (tick() - StickyMemoryTime) > StickyMemoryDuration then
            StickyMemory = nil
        end
    end

    if not target then
        local closest = GetClosestTarget()
        target = closest
        if StickyLockEnabled and target then
            StickyMemory = target
            StickyMemoryTime = tick()
        end
    end

    AimbotTarget = target
    if not target then return end
    if not target.Character then return end

    local tPos = GetTargetPosition(target)
    if not tPos then return end

    local cur = Camera.CFrame.Position
    local aimDir = (tPos - cur).Unit

    -- 🆕 Random Jitter (micro offset untuk anti-pattern)
    if RandomJitterEnabled then
        if tick() - (MicroOffset.X or 0) > 0.15 then
            MicroOffset = Vector2.new(tick(), math.random(-100, 100) / 5000)
        end
        aimDir = (aimDir + Vector3.new(MicroOffset.Y, MicroOffset.Y, 0)).Unit
    end

    local newCF = CFrame.new(cur, cur + aimDir)

    -- 🆕 Human Curve (ease in-out)
    if HumanCurveEnabled then
        local sf = math.clamp(Smoothness, 1, 20)
        local t = 1 / sf
        -- ease in-out curve
        local eased = t * t * (3 - 2 * t)
        if MicroSmoothEnabled then
            eased = eased * 0.7
        end
        Camera.CFrame = Camera.CFrame:Lerp(newCF, eased)
    elseif AimbotMode == "Instant" then
        Camera.CFrame = newCF
    elseif AimbotMode == "Accurate" then
        local sf = math.clamp(Smoothness, 1, 20)
        Camera.CFrame = sf <= 2 and newCF or Camera.CFrame:Lerp(newCF, 1/sf)
    elseif AimbotMode == "Smooth" then
        local sf = math.clamp(Smoothness * 2, 2, 30)
        Camera.CFrame = Camera.CFrame:Lerp(newCF, 1/sf)
    end
end

local function LookAtPlayer()
    if not LookAtEnabled or not IsLoggedIn then return end
    local target = GetClosestTarget()
    if target and target.Character then
        local tPart = target.Character:FindFirstChild("Head") or target.Character:FindFirstChild("HumanoidRootPart")
        if tPart then
            local camPos = Camera.CFrame.Position
            local lookDir = (tPart.Position - camPos).Unit
            pcall(function() Camera.CFrame = CFrame.new(camPos, camPos + lookDir) end)
        end
    end
end

local FOVCircle = Drawing.new("Circle")
FOVCircle.Visible = false
FOVCircle.Color = THEME.AccentColor
FOVCircle.Thickness = 2
FOVCircle.Radius = FOVRadius
FOVCircle.Filled = false
FOVCircle.Transparency = 1
FOVCircle.Position = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)

local function UpdateFOV()
    if FOVCircle and FOVCircleEnabled then
        FOVCircle.Radius = FOVRadius
        FOVCircle.Position = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
    end
end

--==============================================================
-- KEYBIND HANDLER
--==============================================================
UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    -- Aimbot keybind
    if AimbotKeybindEnabled and input.KeyCode == AimbotKeybind then
        AimbotKeyHeld = true
    end
    -- Fly-Void keybind
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
-- MAIN LOOP
--==============================================================
RunService.RenderStepped:Connect(function()
    pcall(function()
        UpdateFOV()
        UpdateESP()
        if AimbotEnabled and IsLoggedIn then AimbotFunction() end
        if SpeedHackEnabled and IsLoggedIn then ApplySpeedHack() end
        if LookAtEnabled and IsLoggedIn then LookAtPlayer() end
    end)
end)

--==============================================================
-- UI BUILDER (RED & BLACK)
--==============================================================
local function CreateUI()

    --===== LOADING SCREEN =====
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
    LTitle.Text = "ZETGAMES-AIMLOCK-ADVANSERVER"
    LTitle.TextColor3 = THEME.TextColor
    LTitle.Font = Enum.Font.Code
    LTitle.TextSize = 16
    LTitle.ZIndex = 302
    LTitle.Parent = LoadingBg

    local LTag = Instance.new("TextLabel")
    LTag.Size = UDim2.new(1, -30, 0, 20)
    LTag.Position = UDim2.new(0, 15, 0, 50)
    LTag.BackgroundTransparency = 1
    LTag.Text = "[ V3.9 TESTING - UJI COBA ]"
    LTag.TextColor3 = Color3.fromRGB(255, 200, 0)
    LTag.Font = Enum.Font.Code
    LTag.TextSize = 11
    LTag.ZIndex = 302
    LTag.Parent = LoadingBg

    local LSub = Instance.new("TextLabel")
    LSub.Size = UDim2.new(1, -30, 0, 25)
    LSub.Position = UDim2.new(0, 15, 0, 75)
    LSub.BackgroundTransparency = 1
    LSub.Text = "> INITIALIZING V3.9..."
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

    local LFooter = Enum.Font.Code
    local LFooterLbl = Instance.new("TextLabel")
    LFooterLbl.Size = UDim2.new(1, -30, 0, 20)
    LFooterLbl.Position = UDim2.new(0, 15, 0, 195)
    LFooterLbl.BackgroundTransparency = 1
    LFooterLbl.Text = "> 🆕 AIMBOT V2 + RADAR + FLY-VOID"
    LFooterLbl.TextColor3 = Color3.fromRGB(255, 200, 0)
    LFooterLbl.Font = Enum.Font.Code
    LFooterLbl.TextSize = 9
    LFooterLbl.ZIndex = 302
    LFooterLbl.Parent = LoadingBg

    --===== LOGIN FRAME =====
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
    LTopTxt.Text = "● ZETGAMES-AIMLOCK-ADVANSERVER V3.9"
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
    LTag2.Text = "[ TESTING BUILD - NO NIGHT LOCK ]"
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
    Instr.Text = "> STEPS:\n> 1. Click GET KEY\n> 2. Generate key\n> 3. Enter key\n> 4. AUTHENTICATE\n> [ V3.9 TESTING - NEW FEATURES ]"
    Instr.TextColor3 = THEME.TextLight
    Instr.Font = Enum.Font.Code
    Instr.TextSize = 9
    Instr.TextXAlignment = Enum.TextXAlignment.Left
    Instr.ZIndex = 12
    Instr.Parent = LoginFrame

    --===== MAIN HUB =====
    local MainHub = Instance.new("Frame")
    MainHub.Size = UDim2.new(0, 360, 0, 500)
    MainHub.Position = UDim2.new(0.5, -180, 0.5, -250)
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
    MainTitle2.Text = "● V3.9 TESTING [ADVANSERVER]"
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
    ScrollFrame.ScrollBarThickness = 8
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
        f.Size = UDim2.new(1, -20, 0, 25)
        f.Position = UDim2.new(0, 10, 0, y)
        f.BackgroundColor3 = THEME.SectionBG
        f.BorderColor3 = THEME.AccentColor
        f.BorderSizePixel = 1
        f.ZIndex = 12
        f.Parent = ScrollContent
        Instance.new("UICorner", f).CornerRadius = UDim.new(0, 4)
        local t = Instance.new("TextLabel")
        t.Size = UDim2.new(1, -10, 1, 0)
        t.Position = UDim2.new(0, 5, 0, 0)
        t.BackgroundTransparency = 1
        t.Text = title
        t.TextColor3 = THEME.TextColor
        t.Font = Enum.Font.Code
        t.TextSize = 11
        t.TextXAlignment = Enum.TextXAlignment.Left
        t.ZIndex = 13
        t.Parent = f
    end

    local function ToggleButton(text, y, callback)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, -20, 0, 40)
        btn.Position = UDim2.new(0, 10, 0, y)
        btn.BackgroundColor3 = THEME.ButtonBG
        btn.BorderColor3 = THEME.AccentColor
        btn.BorderSizePixel = 1
        btn.Text = text
        btn.TextColor3 = THEME.TextColor
        btn.Font = Enum.Font.Code
        btn.TextSize = 12
        btn.ZIndex = 12
        btn.Parent = ScrollContent
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 4)
        btn.MouseButton1Click:Connect(function() callback(btn) end)
        return btn
    end

    local function TextBox(placeholder, y, defaultText)
        local tb = Instance.new("TextBox")
        tb.Size = UDim2.new(1, -20, 0, 35)
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

    --==========================================================
    -- 🆕 AIMBOT V2 (Sticky + Stealth)
    --==========================================================
    Section("=== 🎯 AIMBOT V2 (STICKY + STEALTH) ===", 30)

    local AimbotBtn = Instance.new("TextButton")
    AimbotBtn.Size = UDim2.new(1, -20, 0, 45)
    AimbotBtn.Position = UDim2.new(0, 10, 0, 60)
    AimbotBtn.BackgroundColor3 = THEME.ButtonBG
    AimbotBtn.BorderColor3 = THEME.AccentColor
    AimbotBtn.BorderSizePixel = 2
    AimbotBtn.Text = "> AIMBOT V2: OFF"
    AimbotBtn.TextColor3 = THEME.TextColor
    AimbotBtn.Font = Enum.Font.Code
    AimbotBtn.TextSize = 13
    AimbotBtn.ZIndex = 12
    AimbotBtn.Parent = ScrollContent
    Instance.new("UICorner", AimbotBtn).CornerRadius = UDim.new(0, 4)
    AimbotBtn.MouseButton1Click:Connect(function()
        AimbotEnabled = not AimbotEnabled
        if AimbotEnabled then
            AimbotBtn.Text = "> AIMBOT V2: ON"
            AimbotBtn.BackgroundColor3 = THEME.ButtonActive
        else
            AimbotBtn.Text = "> AIMBOT V2: OFF"
            AimbotBtn.BackgroundColor3 = THEME.ButtonBG
            StickyMemory = nil
        end
    end)

    local function TogglePair(text, y, xPos, callback)
        local b = Instance.new("TextButton")
        b.Size = UDim2.new(0.48, -15, 0, 35)
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

    TogglePair("> STICKY LOCK: ON", 110, 0, function(btn)
        StickyLockEnabled = not StickyLockEnabled
        btn.Text = StickyLockEnabled and "> STICKY LOCK: ON" or "> STICKY LOCK: OFF"
        btn.BackgroundColor3 = StickyLockEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    TogglePair("> MICRO SMOOTH: ON", 110, 0.52, function(btn)
        MicroSmoothEnabled = not MicroSmoothEnabled
        btn.Text = MicroSmoothEnabled and "> MICRO SMOOTH: ON" or "> MICRO SMOOTH: OFF"
        btn.BackgroundColor3 = MicroSmoothEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    TogglePair("> HUMAN CURVE: ON", 150, 0, function(btn)
        HumanCurveEnabled = not HumanCurveEnabled
        btn.Text = HumanCurveEnabled and "> HUMAN CURVE: ON" or "> HUMAN CURVE: OFF"
        btn.BackgroundColor3 = HumanCurveEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    TogglePair("> RANDOM JITTER: ON", 150, 0.52, function(btn)
        RandomJitterEnabled = not RandomJitterEnabled
        btn.Text = RandomJitterEnabled and "> JITTER: ON" or "> JITTER: OFF"
        btn.BackgroundColor3 = RandomJitterEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    ToggleButton("> KEYBIND (E) HOLD: OFF", 190, function(btn)
        AimbotKeybindEnabled = not AimbotKeybindEnabled
        btn.Text = AimbotKeybindEnabled and "> KEYBIND (E) HOLD: ON" or "> KEYBIND (E) HOLD: OFF"
        btn.BackgroundColor3 = AimbotKeybindEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    ToggleButton("> WALL_CHECK: OFF", 235, function(btn)
        WallCheckEnabled = not WallCheckEnabled
        btn.Text = WallCheckEnabled and "> WALL_CHECK: ON" or "> WALL_CHECK: OFF"
        btn.BackgroundColor3 = WallCheckEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    ToggleButton("> TEAM_CHECK: OFF", 280, function(btn)
        TeamCheckEnabled = not TeamCheckEnabled
        btn.Text = TeamCheckEnabled and "> TEAM_CHECK: ON" or "> TEAM_CHECK: OFF"
        btn.BackgroundColor3 = TeamCheckEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    ToggleButton("> PREDICTION: OFF", 325, function(btn)
        PredictionEnabled = not PredictionEnabled
        btn.Text = PredictionEnabled and "> PREDICTION: ON" or "> PREDICTION: OFF"
        btn.BackgroundColor3 = PredictionEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    local FOVInput = TextBox("> FOV Radius (50-5000)", 370)
    FOVInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nr = tonumber(FOVInput.Text)
            if nr then FOVRadius = math.clamp(nr, 50, MaxFOVRadius) end
            FOVInput.Text = ""
        end
    end)

    local SmoothInput = TextBox("> Smoothness (1-20)", 410)
    SmoothInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local ns = tonumber(SmoothInput.Text)
            if ns then Smoothness = math.clamp(ns, 1, 20) end
            SmoothInput.Text = ""
        end
    end)

    local TargetLbl = Instance.new("TextLabel")
    TargetLbl.Size = UDim2.new(1, -20, 0, 20)
    TargetLbl.Position = UDim2.new(0, 10, 0, 455)
    TargetLbl.BackgroundTransparency = 1
    TargetLbl.Text = "> TARGET_PART: HEAD"
    TargetLbl.TextColor3 = THEME.TextColor
    TargetLbl.Font = Enum.Font.Code
    TargetLbl.TextSize = 11
    TargetLbl.TextXAlignment = Enum.TextXAlignment.Left
    TargetLbl.ZIndex = 12
    TargetLbl.Parent = ScrollContent

    local TargetBtnFrame = Instance.new("Frame")
    TargetBtnFrame.Size = UDim2.new(1, -20, 0, 35)
    TargetBtnFrame.Position = UDim2.new(0, 10, 0, 480)
    TargetBtnFrame.BackgroundTransparency = 1
    TargetBtnFrame.ZIndex = 12
    TargetBtnFrame.Parent = ScrollContent

    local function CreateTargetBtn(text, part, xPos)
        local b = Instance.new("TextButton")
        b.Size = UDim2.new(0, 90, 1, 0)
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
            TargetPart = part
            TargetLbl.Text = "> TARGET_PART: " .. string.upper(text)
        end)
    end
    CreateTargetBtn("Head", "Head", 0)
    CreateTargetBtn("Torso", "HumanoidRootPart", 0.37)
    CreateTargetBtn("Body", "UpperTorso", 0.74)

    --==========================================================
    -- 🆕 RADAR BULAT
    --==========================================================
    Section("=== 📡 RADAR BULAT ===", 525)

    ToggleButton("> RADAR: OFF", 555, function(btn)
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

    TogglePair("> ROTATE: ON", 605, 0, function(btn)
        RadarRotateWithCamera = not RadarRotateWithCamera
        btn.Text = RadarRotateWithCamera and "> ROTATE: ON" or "> ROTATE: OFF"
        btn.BackgroundColor3 = RadarRotateWithCamera and THEME.ButtonActive or THEME.ButtonBG
    end)

    TogglePair("> NAMES: OFF", 605, 0.52, function(btn)
        RadarShowNames = not RadarShowNames
        btn.Text = RadarShowNames and "> NAMES: ON" or "> NAMES: OFF"
        btn.BackgroundColor3 = RadarShowNames and THEME.ButtonActive or THEME.ButtonBG
    end)

    local RadarRadiusInput = TextBox("> Radius (100-1000)", 650, tostring(RadarRadius))
    RadarRadiusInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nr = tonumber(RadarRadiusInput.Text)
            if nr then
                RadarRadius = math.clamp(nr, 100, 1000)
                RadarRadiusInput.Text = tostring(RadarRadius)
            else
                RadarRadiusInput.Text = tostring(RadarRadius)
            end
        end
    end)

    local RadarZoomInput = TextBox("> Zoom (0.5-3.0)", 690, tostring(RadarZoom))
    RadarZoomInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nz = tonumber(RadarZoomInput.Text)
            if nz then
                RadarZoom = math.clamp(nz, 0.5, 3.0)
                RadarZoomInput.Text = tostring(RadarZoom)
            else
                RadarZoomInput.Text = tostring(RadarZoom)
            end
        end
    end)

    --==========================================================
    -- 🆕 FLY-VOID
    --==========================================================
    Section("=== 🕳️ FLY-VOID (ANTI-VOID UPGRADE) ===", 735)

    ToggleButton("> FLY-VOID: OFF (V)", 765, function(btn)
        FlyVoidEnabled = not FlyVoidEnabled
        if FlyVoidEnabled then
            btn.Text = "> FLY-VOID: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            EnableFlyVoid()
            Notify("Fly-Void", "> ACTIVE (Press V)", 2)
        else
            btn.Text = "> FLY-VOID: OFF (V)"
            btn.BackgroundColor3 = THEME.ButtonBG
            DisableFlyVoid()
        end
    end)

    TogglePair("> HIDE MODE: ON", 815, 0, function(btn)
        FlyVoidHideMode = not FlyVoidHideMode
        btn.Text = FlyVoidHideMode and "> HIDE: ON" or "> HIDE: OFF"
        btn.BackgroundColor3 = FlyVoidHideMode and THEME.ButtonActive or THEME.ButtonBG
    end)

    TogglePair("> WALL SHOOT: ON", 815, 0.52, function(btn)
        FlyVoidWallShoot = not FlyVoidWallShoot
        btn.Text = FlyVoidWallShoot and "> WALL SHOOT: ON" or "> WALL SHOOT: OFF"
        btn.BackgroundColor3 = FlyVoidWallShoot and THEME.ButtonActive or THEME.ButtonBG
    end)

    local FlyVoidHeightInput = TextBox("> Y Height (-200 to 0)", 860, tostring(FlyVoidHeight))
    FlyVoidHeightInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nh = tonumber(FlyVoidHeightInput.Text)
            if nh then
                FlyVoidHeight = math.clamp(nh, -200, 0)
                FlyVoidHeightInput.Text = tostring(FlyVoidHeight)
            else
                FlyVoidHeightInput.Text = tostring(FlyVoidHeight)
            end
        end
    end)

    local FlyVoidInfo = Instance.new("TextLabel")
    FlyVoidInfo.Size = UDim2.new(1, -20, 0, 50)
    FlyVoidInfo.Position = UDim2.new(0, 10, 0, 900)
    FlyVoidInfo.BackgroundColor3 = THEME.PanelBG
    FlyVoidInfo.BorderColor3 = THEME.AccentColor
    FlyVoidInfo.BorderSizePixel = 1
    FlyVoidInfo.Text = "> Jalan bawah map tanpa mati\n> Tembakan tembus + Kill dari bawah\n> Sembunyi & Anti-Void\n> Keybind: V"
    FlyVoidInfo.TextColor3 = THEME.TextLight
    FlyVoidInfo.Font = Enum.Font.Code
    FlyVoidInfo.TextSize = 9
    FlyVoidInfo.TextXAlignment = Enum.TextXAlignment.Left
    FlyVoidInfo.ZIndex = 12
    FlyVoidInfo.Parent = ScrollContent
    Instance.new("UICorner", FlyVoidInfo).CornerRadius = UDim.new(0, 4)

    --==========================================================
    -- USER INFORMATION
    --==========================================================
    Section("=== USER INFORMATION ===", 965)
    local UIF = Instance.new("Frame")
    UIF.Size = UDim2.new(1, -20, 0, 110)
    UIF.Position = UDim2.new(0, 10, 0, 995)
    UIF.BackgroundColor3 = THEME.PanelBG
    UIF.BorderColor3 = THEME.AccentColor
    UIF.BorderSizePixel = 1
    UIF.ZIndex = 12
    UIF.Parent = ScrollContent
    Instance.new("UICorner", UIF).CornerRadius = UDim.new(0, 4)

    local NameLbl = Instance.new("TextLabel")
    NameLbl.Size = UDim2.new(1, -15, 0, 25)
    NameLbl.Position = UDim2.new(0, 10, 0, 10)
    NameLbl.BackgroundTransparency = 1
    NameLbl.Text = "> NAME : " .. LocalPlayer.DisplayName
    NameLbl.TextColor3 = THEME.TextColor
    NameLbl.Font = Enum.Font.Code
    NameLbl.TextSize = 12
    NameLbl.TextXAlignment = Enum.TextXAlignment.Left
    NameLbl.ZIndex = 13
    NameLbl.Parent = UIF

    local UserLbl = Instance.new("TextLabel")
    UserLbl.Size = UDim2.new(1, -15, 0, 25)
    UserLbl.Position = UDim2.new(0, 10, 0, 40)
    UserLbl.BackgroundTransparency = 1
    UserLbl.Text = "> USERNAME : " .. LocalPlayer.Name
    UserLbl.TextColor3 = THEME.TextColor
    UserLbl.Font = Enum.Font.Code
    UserLbl.TextSize = 12
    UserLbl.TextXAlignment = Enum.TextXAlignment.Left
    UserLbl.ZIndex = 13
    UserLbl.Parent = UIF

    local BuildLbl = Instance.new("TextLabel")
    BuildLbl.Size = UDim2.new(1, -15, 0, 25)
    BuildLbl.Position = UDim2.new(0, 10, 0, 70)
    BuildLbl.BackgroundTransparency = 1
    BuildLbl.Text = "> BUILD : V3.9 TESTING"
    BuildLbl.TextColor3 = Color3.fromRGB(255, 200, 0)
    BuildLbl.Font = Enum.Font.Code
    BuildLbl.TextSize = 12
    BuildLbl.TextXAlignment = Enum.TextXAlignment.Left
    BuildLbl.ZIndex = 13
    BuildLbl.Parent = UIF

    task.spawn(function()
        while task.wait(1) do
            pcall(function()
                NameLbl.Text = "> NAME : " .. LocalPlayer.DisplayName
                UserLbl.Text = "> USERNAME : " .. LocalPlayer.Name
            end)
        end
    end)

    --==========================================================
    -- MAIN FEATURES
    --==========================================================
    Section("=== MAIN FEATURES ===", 1120)

    ToggleButton("> FPS BOOST: OFF", 1150, function(btn)
        FPSBoostEnabled = not FPSBoostEnabled
        if FPSBoostEnabled then
            btn.Text = "> FPS BOOST: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            EnableFPSBoost()
        else
            btn.Text = "> FPS BOOST: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
            DisableFPSBoost()
        end
    end)

    ToggleButton("> FULLBRIGHT: OFF", 1195, function(btn)
        FullbrightEnabled = not FullbrightEnabled
        if FullbrightEnabled then
            btn.Text = "> FULLBRIGHT: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            EnableFullbright()
        else
            btn.Text = "> FULLBRIGHT: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
            DisableFullbright()
        end
    end)

    ToggleButton("> SPEED HACK: OFF", 1240, function(btn)
        SpeedHackEnabled = not SpeedHackEnabled
        if SpeedHackEnabled then
            btn.Text = "> SPEED HACK: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            ApplySpeedHack()
        else
            btn.Text = "> SPEED HACK: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
            ApplySpeedHack()
        end
    end)

    local SpeedInput = TextBox("> Speed (16-500)", 1285)
    SpeedInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local ns = tonumber(SpeedInput.Text)
            if ns then
                SpeedMultiplier = math.clamp(ns, 16, MaxSpeed)
                if SpeedHackEnabled then ApplySpeedHack() end
            end
            SpeedInput.Text = ""
        end
    end)

    ToggleButton("> INFINITE JUMP: OFF", 1325, function(btn)
        InfiniteJumpEnabled = not InfiniteJumpEnabled
        if InfiniteJumpEnabled then
            btn.Text = "> INFINITE JUMP: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            EnableInfiniteJump()
        else
            btn.Text = "> INFINITE JUMP: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
            DisableInfiniteJump()
        end
    end)

    ToggleButton("> NOCLIP: OFF", 1370, function(btn)
        NoclipEnabled = not NoclipEnabled
        if NoclipEnabled then
            btn.Text = "> NOCLIP: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            EnableNoclip()
        else
            btn.Text = "> NOCLIP: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
            DisableNoclip()
        end
    end)

    --==========================================================
    -- SURVIVAL FEATURES
    --==========================================================
    Section("=== SURVIVAL FEATURES ===", 1420)

    ToggleButton("> AUTO RESPAWN: OFF", 1450, function(btn)
        AutoRespawnEnabled = not AutoRespawnEnabled
        if AutoRespawnEnabled then
            btn.Text = "> AUTO RESPAWN: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            StartAutoRespawn()
        else
            btn.Text = "> AUTO RESPAWN: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
            StopAutoRespawn()
        end
    end)

    ToggleButton("> ANTI-FLING: OFF", 1495, function(btn)
        AntiFlingEnabled = not AntiFlingEnabled
        if AntiFlingEnabled then
            btn.Text = "> ANTI-FLING: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            StartAntiFling()
        else
            btn.Text = "> ANTI-FLING: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
            StopAntiFling()
        end
    end)

    ToggleButton("> LOOK AT PLAYER: OFF", 1540, function(btn)
        LookAtEnabled = not LookAtEnabled
        if LookAtEnabled then
            btn.Text = "> LOOK AT PLAYER: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
        else
            btn.Text = "> LOOK AT PLAYER: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
        end
    end)

    --==========================================================
    -- CHAT SPAM
    --==========================================================
    Section("=== CHAT SPAM ===", 1590)

    ToggleButton("> CHAT SPAM: OFF", 1620, function(btn)
        ChatSpamEnabled = not ChatSpamEnabled
        if ChatSpamEnabled then
            btn.Text = "> CHAT SPAM: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            StartChatSpamLoop()
        else
            btn.Text = "> CHAT SPAM: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
        end
    end)

    local ChatTextInput = TextBox("> Type message to spam...", 1670, ChatSpamText)
    ChatTextInput.FocusLost:Connect(function(enterPressed)
        if enterPressed and ChatTextInput.Text ~= "" then
            ChatSpamText = ChatTextInput.Text
        end
    end)

    local ChatDelayInput = TextBox("> Delay (1-10 sec)", 1710, tostring(ChatSpamDelay))
    ChatDelayInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nd = tonumber(ChatDelayInput.Text)
            if nd then ChatSpamDelay = math.clamp(nd, 1, 10) end
            ChatDelayInput.Text = tostring(ChatSpamDelay)
        end
    end)

    --==========================================================
    -- SOUND ESP
    --==========================================================
    Section("=== SOUND ESP ===", 1755)

    ToggleButton("> SOUND ESP: OFF", 1785, function(btn)
        SoundESPEnabled = not SoundESPEnabled
        if SoundESPEnabled then
            btn.Text = "> SOUND ESP: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            StartSoundESP()
        else
            btn.Text = "> SOUND ESP: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
            StopSoundESP()
        end
    end)

    local SndRadiusInput = TextBox("> Radius (10-500)", 1835, tostring(SoundESPRadius))
    SndRadiusInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nr = tonumber(SndRadiusInput.Text)
            if nr then
                SoundESPRadius = math.clamp(nr, 10, 500)
                SndRadiusInput.Text = tostring(SoundESPRadius)
            else
                SndRadiusInput.Text = tostring(SoundESPRadius)
            end
        end
    end)

    --==========================================================
    -- MUSIC PLAYER
    --==========================================================
    Section("=== 🎵 MUSIC PLAYER ===", 1880)

    ToggleButton("> MUSIC PLAYER: OFF", 1910, function(btn)
        MusicPlayerEnabled = not MusicPlayerEnabled
        if MusicPlayerEnabled then
            btn.Text = "> MUSIC PLAYER: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            StartMusic()
        else
            btn.Text = "> MUSIC PLAYER: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
            StopMusic()
        end
    end)

    local MusicIDInput = TextBox("> Sound ID", 1960, MusicID)
    MusicIDInput.FocusLost:Connect(function(enterPressed)
        if enterPressed and MusicIDInput.Text ~= "" then
            MusicID = MusicIDInput.Text
            UpdateMusic()
        end
    end)

    local MusicVolInput = TextBox("> Volume (0.1 - 5)", 2000, tostring(MusicVolume))
    MusicVolInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nv = tonumber(MusicVolInput.Text)
            if nv then
                MusicVolume = math.clamp(nv, 0.1, 5)
                MusicVolInput.Text = tostring(MusicVolume)
                if MusicSound then MusicSound.Volume = MusicVolume end
            else
                MusicVolInput.Text = tostring(MusicVolume)
            end
        end
    end)

    --==========================================================
    -- FULL ESP
    --==========================================================
    Section("=== FULL ESP FEATURES ===", 2045)

    ToggleButton("> ESP MASTER: OFF", 2075, function(btn)
        ESPEnabled = not ESPEnabled
        if ESPEnabled then
            btn.Text = "> ESP MASTER: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
        else
            btn.Text = "> ESP MASTER: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
        end
    end)

    ToggleButton("> 🌈 RAINBOW ESP: OFF", 2120, function(btn)
        RainbowESPEnabled = not RainbowESPEnabled
        if RainbowESPEnabled then
            btn.Text = "> 🌈 RAINBOW ESP: ON"
            btn.BackgroundColor3 = THEME.ButtonActive
            EnableRainbowESP()
        else
            btn.Text = "> 🌈 RAINBOW ESP: OFF"
            btn.BackgroundColor3 = THEME.ButtonBG
            DisableRainbowESP()
        end
    end)

    TogglePair("> BOX: ON", 2165, 0, function(btn)
        ESPBoxEnabled = not ESPBoxEnabled
        btn.Text = ESPBoxEnabled and "> BOX: ON" or "> BOX: OFF"
        btn.BackgroundColor3 = ESPBoxEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    TogglePair("> NAME: ON", 2165, 0.52, function(btn)
        ESPNameEnabled = not ESPNameEnabled
        btn.Text = ESPNameEnabled and "> NAME: ON" or "> NAME: OFF"
        btn.BackgroundColor3 = ESPNameEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    TogglePair("> DISTANCE: ON", 2205, 0, function(btn)
        ESPDistanceEnabled = not ESPDistanceEnabled
        btn.Text = ESPDistanceEnabled and "> DISTANCE: ON" or "> DISTANCE: OFF"
        btn.BackgroundColor3 = ESPDistanceEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    TogglePair("> HEALTH: ON", 2205, 0.52, function(btn)
        ESPHealthEnabled = not ESPHealthEnabled
        btn.Text = ESPHealthEnabled and "> HEALTH: ON" or "> HEALTH: OFF"
        btn.BackgroundColor3 = ESPHealthEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    TogglePair("> TRACER: ON", 2245, 0, function(btn)
        ESPTracerEnabled = not ESPTracerEnabled
        btn.Text = ESPTracerEnabled and "> TRACER: ON" or "> TRACER: OFF"
        btn.BackgroundColor3 = ESPTracerEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    TogglePair("> HEAD DOT: OFF", 2245, 0.52, function(btn)
        ESPHeadDotEnabled = not ESPHeadDotEnabled
        btn.Text = ESPHeadDotEnabled and "> HEAD DOT: ON" or "> HEAD DOT: OFF"
        btn.BackgroundColor3 = ESPHeadDotEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    TogglePair("> CHAMS: OFF", 2285, 0, function(btn)
        ESPChamsEnabled = not ESPChamsEnabled
        btn.Text = ESPChamsEnabled and "> CHAMS: ON" or "> CHAMS: OFF"
        btn.BackgroundColor3 = ESPChamsEnabled and THEME.ButtonActive or THEME.ButtonBG
        if not ESPChamsEnabled then
            for p, _ in pairs(ChamsObjects) do RemoveChams(p) end
        end
    end)

    TogglePair("> SKELETON: OFF", 2285, 0.52, function(btn)
        ESPSkeletonEnabled = not ESPSkeletonEnabled
        btn.Text = ESPSkeletonEnabled and "> SKELETON: ON" or "> SKELETON: OFF"
        btn.BackgroundColor3 = ESPSkeletonEnabled and THEME.ButtonActive or THEME.ButtonBG
    end)

    local TracerColorLbl = Instance.new("TextLabel")
    TracerColorLbl.Size = UDim2.new(1, -20, 0, 20)
    TracerColorLbl.Position = UDim2.new(0, 10, 0, 2330)
    TracerColorLbl.BackgroundTransparency = 1
    TracerColorLbl.Text = "> TRACER COLOR: MERAH"
    TracerColorLbl.TextColor3 = THEME.TextColor
    TracerColorLbl.Font = Enum.Font.Code
    TracerColorLbl.TextSize = 11
    TracerColorLbl.TextXAlignment = Enum.TextXAlignment.Left
    TracerColorLbl.ZIndex = 12
    TracerColorLbl.Parent = ScrollContent

    local TracerColorFrame = Instance.new("Frame")
    TracerColorFrame.Size = UDim2.new(1, -20, 0, 40)
    TracerColorFrame.Position = UDim2.new(0, 10, 0, 2355)
    TracerColorFrame.BackgroundTransparency = 1
    TracerColorFrame.ZIndex = 12
    TracerColorFrame.Parent = ScrollContent

    local function CreateColorBtn(text, color, name, xPos)
        local b = Instance.new("TextButton")
        b.Size = UDim2.new(0.31, 0, 0, 35)
        b.Position = UDim2.new(xPos, 0, 0, 0)
        b.BackgroundColor3 = color
        b.BorderColor3 = THEME.AccentColor
        b.BorderSizePixel = 1
        b.Text = text
        b.TextColor3 = Color3.fromRGB(255, 255, 255)
        b.Font = Enum.Font.Code
        b.TextSize = 10
        b.ZIndex = 13
        b.Parent = TracerColorFrame
        Instance.new("UICorner", b).CornerRadius = UDim.new(0, 3)
        b.MouseButton1Click:Connect(function()
            TracerColor = color
            TracerColorName = name
            TracerColorLbl.Text = "> TRACER COLOR: " .. name
        end)
    end
    CreateColorBtn("MERAH", Color3.fromRGB(255, 0, 0), "MERAH", 0)
    CreateColorBtn("PUTIH", Color3.fromRGB(255, 255, 255), "PUTIH", 0.345)
    CreateColorBtn("HITAM", Color3.fromRGB(0, 0, 0), "HITAM", 0.69)

    --==========================================================
    -- TELEPORT
    --==========================================================
    Section("=== TELEPORT ===", 2420)

    local TPMouseBtn = Instance.new("TextButton")
    TPMouseBtn.Size = UDim2.new(1, -20, 0, 40)
    TPMouseBtn.Position = UDim2.new(0, 10, 0, 2450)
    TPMouseBtn.BackgroundColor3 = THEME.ButtonActive
    TPMouseBtn.BorderColor3 = THEME.AccentColor
    TPMouseBtn.BorderSizePixel = 1
    TPMouseBtn.Text = "> TELEPORT TO MOUSE"
    TPMouseBtn.TextColor3 = THEME.TextColor
    TPMouseBtn.Font = Enum.Font.Code
    TPMouseBtn.TextSize = 12
    TPMouseBtn.ZIndex = 12
    TPMouseBtn.Parent = ScrollContent
    Instance.new("UICorner", TPMouseBtn).CornerRadius = UDim.new(0, 4)
    TPMouseBtn.MouseButton1Click:Connect(TeleportToMouse)

    local SaveLocBtn2 = Instance.new("TextButton")
    SaveLocBtn2.Size = UDim2.new(0.48, -15, 0, 40)
    SaveLocBtn2.Position = UDim2.new(0, 10, 0, 2495)
    SaveLocBtn2.BackgroundColor3 = THEME.ButtonActive
    SaveLocBtn2.BorderColor3 = THEME.AccentColor
    SaveLocBtn2.BorderSizePixel = 1
    SaveLocBtn2.Text = "> SAVE LOC"
    SaveLocBtn2.TextColor3 = THEME.TextColor
    SaveLocBtn2.Font = Enum.Font.Code
    SaveLocBtn2.TextSize = 11
    SaveLocBtn2.ZIndex = 12
    SaveLocBtn2.Parent = ScrollContent
    Instance.new("UICorner", SaveLocBtn2).CornerRadius = UDim.new(0, 4)
    SaveLocBtn2.MouseButton1Click:Connect(SaveLocation)

    local LoadLocBtn2 = Instance.new("TextButton")
    LoadLocBtn2.Size = UDim2.new(0.48, -15, 0, 40)
    LoadLocBtn2.Position = UDim2.new(0.52, 5, 0, 2495)
    LoadLocBtn2.BackgroundColor3 = THEME.ButtonActive
    LoadLocBtn2.BorderColor3 = THEME.AccentColor
    LoadLocBtn2.BorderSizePixel = 1
    LoadLocBtn2.Text = "> LOAD LOC"
    LoadLocBtn2.TextColor3 = THEME.TextColor
    LoadLocBtn2.Font = Enum.Font.Code
    LoadLocBtn2.TextSize = 11
    LoadLocBtn2.ZIndex = 12
    LoadLocBtn2.Parent = ScrollContent
    Instance.new("UICorner", LoadLocBtn2).CornerRadius = UDim.new(0, 4)
    LoadLocBtn2.MouseButton1Click:Connect(LoadLocation)

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

    local function ToggleMenu()
        if not IsLoggedIn then return end
        MenuVisible = not MenuVisible
        MainHub.Visible = MenuVisible
    end

    ToggleMenuButton.MouseButton1Click:Connect(ToggleMenu)
    CloseBtn.MouseButton1Click:Connect(function()
        MenuVisible = false
        MainHub.Visible = false
    end)

    UserInputService.InputBegan:Connect(function(input, gp)
        if gp then return end
        if input.KeyCode == MenuKey and IsLoggedIn then
            ToggleMenu()
        end
    end)

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

    --==========================================================
    -- LOGIN LOGIC
    --==========================================================
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
                Notify("Success", "> WELCOME V3.9 | " .. keyData.Level, 3)
                Notify("New", "> Aimbot V2 + Radar + Fly-Void", 3)
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

    --==========================================================
    -- LOADING ANIMATION
    --==========================================================
    local loadingMessages = {
        "> LOADING MODULES V3.9...",
        "> INITIALIZING AIMBOT V2...",
        "> STICKY LOCK + STEALTH...",
        "> LOADING RADAR BULAT...",
        "> INITIALIZING FLY-VOID...",
        "> LOADING SOUND ESP...",
        "> LOADING FULL ESP...",
        "> LOADING CHAT SPAM...",
        "> TESTING BUILD - NO NIGHT LOCK...",
        "> SYSTEM READY V3.9..."
    }

    task.spawn(function()
        local totalTime = 10
        local interval = totalTime / 100
        for i = 1, 100 do
            task.wait(interval)
            LBarFill.Size = UDim2.new(i / 100, 0, 1, 0)
            LPercent.Text = i .. "%"
            local mi = math.floor(i / 10) + 1
            if mi > #loadingMessages then mi = #loadingMessages end
            LStatus.Text = loadingMessages[mi]
        end
        LPercent.Text = "100%"
        LStatus.Text = "> SYSTEM READY!"
        LBarFill.Size = UDim2.new(1, 0, 1, 0)
        task.wait(0.5)
        LoadingScreen.Visible = false

        LoginFrame.Visible = true
        Notify("V3.9 TESTING", "> NEW UPDATE LOADED", 3)
        Notify("Login", "> ENTER ACCESS KEY", 3)
    end)

end

--==============================================================
-- RUN
--==============================================================
pcall(CreateUI)
pcall(CreateRadarUI)
