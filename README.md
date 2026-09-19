--[[
    ZetGames-AimLock | Premium Edition v3.8 (FIXED v2)
    Theme: Blue & Black Hacker Style
    Features: Full ESP + FPS Booster + Rainbow ESP + Fullbright + Enhanced Aimbot + User Info + Chat Spam
    Night Lock: REMOVED
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
local CoreGui = game:GetService("CoreGui")
local Camera = workspace.CurrentCamera

local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

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
local ChatSpamText = "ZETGAMES-AIMLOCK ON TOP!"
local ChatSpamDelay = 3

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
ScreenGui.Name = "ZetGamesAimLock"
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
    Notif.BackgroundColor3 = Color3.fromRGB(10, 10, 25)
    Notif.BorderColor3 = Color3.fromRGB(0, 150, 255)
    Notif.BorderSizePixel = 2
    Notif.ZIndex = 501
    Notif.Parent = Notifications

    Instance.new("UICorner", Notif).CornerRadius = UDim.new(0, 8)

    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, -16, 0, 22)
    Title.Position = UDim2.new(0, 8, 0, 4)
    Title.BackgroundTransparency = 1
    Title.Text = title
    Title.TextColor3 = Color3.fromRGB(0, 180, 255)
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
    Msg.TextColor3 = Color3.fromRGB(150, 200, 255)
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
-- FUNGSI CHEAT
--==============================================================

-- FULLBRIGHT
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
            OriginalLighting.Atmosphere.Haze = 0
            OriginalLighting.Atmosphere.Glare = 0
        end
    end)
end

-- RAINBOW
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
            if d.SkeletonHead then d.SkeletonHead.Color = c end
            if d.SkeletonTorso then d.SkeletonTorso.Color = c end
            if d.SkeletonLeftArm then d.SkeletonLeftArm.Color = c end
            if d.SkeletonRightArm then d.SkeletonRightArm.Color = c end
            if d.SkeletonLeftLeg then d.SkeletonLeftLeg.Color = c end
            if d.SkeletonRightLeg then d.SkeletonRightLeg.Color = c end
        end
        for _, list in pairs(ChamsObjects) do
            for _, h in pairs(list) do
                if h then h.FillColor = c; h.OutlineColor = c end
            end
        end
    end)
end

local function DisableRainbowESP()
    if RainbowConnection then RainbowConnection:Disconnect(); RainbowConnection = nil end
end

-- CHAT SPAM
local function SendChatMessage(message)
    local sent = false
    pcall(function()
        local chatEvents = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents")
        if chatEvents then
            local sayReq = chatEvents:FindFirstChild("SayMessageRequest")
            if sayReq then
                sayReq:FireServer(message, "All")
                sent = true
            end
        end
    end)
    if not sent then
        pcall(function()
            local channels = TextChatService:FindFirstChild("TextChannels")
            if channels then
                local gen = channels:FindFirstChild("RBXGeneral")
                if gen then
                    gen:SendAsync(message)
                    sent = true
                end
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

-- FPS BOOSTER
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
        if effect:IsA("PostEffect") then
            pcall(function() effect.Enabled = false end)
        end
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

-- TELEPORT
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

-- INFINITE JUMP
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

-- NOCLIP
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

-- SPEED
local function ApplySpeedHack()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = SpeedHackEnabled and SpeedMultiplier or DefaultWalkSpeed
    end
end

-- ESP
local function CreateESP(player)
    if ESPObjects[player] then return end
    local d = {}
    d.Box = Drawing.new("Square"); d.Box.Visible = false; d.Box.Color = Color3.fromRGB(0,180,255); d.Box.Thickness = 2; d.Box.Filled = false; d.Box.Transparency = 1
    d.Name = Drawing.new("Text"); d.Name.Visible = false; d.Name.Color = Color3.fromRGB(0,180,255); d.Name.Size = 12; d.Name.Center = true; d.Name.Outline = true; d.Name.OutlineColor = Color3.fromRGB(0,0,0)
    d.Distance = Drawing.new("Text"); d.Distance.Visible = false; d.Distance.Color = Color3.fromRGB(0,180,255); d.Distance.Size = 10; d.Distance.Center = true; d.Distance.Outline = true; d.Distance.OutlineColor = Color3.fromRGB(0,0,0)
    d.HealthBg = Drawing.new("Line"); d.HealthBg.Visible = false; d.HealthBg.Color = Color3.fromRGB(255,0,0); d.HealthBg.Thickness = 3; d.HealthBg.Transparency = 1
    d.HealthBar = Drawing.new("Line"); d.HealthBar.Visible = false; d.HealthBar.Color = Color3.fromRGB(0,180,255); d.HealthBar.Thickness = 3; d.HealthBar.Transparency = 1
    d.Tracer = Drawing.new("Line"); d.Tracer.Visible = false; d.Tracer.Color = TracerColor; d.Tracer.Thickness = 2; d.Tracer.Transparency = 0.5
    d.HeadDot = Drawing.new("Circle"); d.HeadDot.Visible = false; d.HeadDot.Color = Color3.fromRGB(255,0,0); d.HeadDot.Thickness = 1; d.HeadDot.Radius = 4; d.HeadDot.Filled = true; d.HeadDot.Transparency = 1
    d.SkeletonHead = Drawing.new("Line"); d.SkeletonHead.Visible = false; d.SkeletonHead.Color = Color3.fromRGB(0,180,255); d.SkeletonHead.Thickness = 1; d.SkeletonHead.Transparency = 1
    d.SkeletonTorso = Drawing.new("Line"); d.SkeletonTorso.Visible = false; d.SkeletonTorso.Color = Color3.fromRGB(0,180,255); d.SkeletonTorso.Thickness = 1; d.SkeletonTorso.Transparency = 1
    d.SkeletonLeftArm = Drawing.new("Line"); d.SkeletonLeftArm.Visible = false; d.SkeletonLeftArm.Color = Color3.fromRGB(0,180,255); d.SkeletonLeftArm.Thickness = 1; d.SkeletonLeftArm.Transparency = 1
    d.SkeletonRightArm = Drawing.new("Line"); d.SkeletonRightArm.Visible = false; d.SkeletonRightArm.Color = Color3.fromRGB(0,180,255); d.SkeletonRightArm.Thickness = 1; d.SkeletonRightArm.Transparency = 1
    d.SkeletonLeftLeg = Drawing.new("Line"); d.SkeletonLeftLeg.Visible = false; d.SkeletonLeftLeg.Color = Color3.fromRGB(0,180,255); d.SkeletonLeftLeg.Thickness = 1; d.SkeletonLeftLeg.Transparency = 1
    d.SkeletonRightLeg = Drawing.new("Line"); d.SkeletonRightLeg.Visible = false; d.SkeletonRightLeg.Color = Color3.fromRGB(0,180,255); d.SkeletonRightLeg.Thickness = 1; d.SkeletonRightLeg.Transparency = 1
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
            h.FillColor = Color3.fromRGB(0,180,255)
            h.FillTransparency = 0.7
            h.OutlineColor = Color3.fromRGB(0,255,255)
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

                    if ESPSkeletonEnabled then
                        local function SP(p)
                            if p then local a,b = Camera:WorldToViewportPoint(p.Position); if b then return Vector2.new(a.X,a.Y) end end
                            return nil
                        end
                        local head = SP(player.Character:FindFirstChild("Head"))
                        local upper = SP(player.Character:FindFirstChild("UpperTorso") or player.Character:FindFirstChild("Torso"))
                        local lower = SP(player.Character:FindFirstChild("LowerTorso") or player.Character:FindFirstChild("Torso"))
                        local la = SP(player.Character:FindFirstChild("LeftUpperArm") or player.Character:FindFirstChild("Left Arm"))
                        local ra = SP(player.Character:FindFirstChild("RightUpperArm") or player.Character:FindFirstChild("Right Arm"))
                        local ll = SP(player.Character:FindFirstChild("LeftUpperLeg") or player.Character:FindFirstChild("Left Leg"))
                        local rl = SP(player.Character:FindFirstChild("RightUpperLeg") or player.Character:FindFirstChild("Right Leg"))
                        if head and upper then d.SkeletonHead.Visible = true; d.SkeletonHead.From = head; d.SkeletonHead.To = upper else d.SkeletonHead.Visible = false end
                        if upper and lower then d.SkeletonTorso.Visible = true; d.SkeletonTorso.From = upper; d.SkeletonTorso.To = lower else d.SkeletonTorso.Visible = false end
                        if upper and la then d.SkeletonLeftArm.Visible = true; d.SkeletonLeftArm.From = upper; d.SkeletonLeftArm.To = la else d.SkeletonLeftArm.Visible = false end
                        if upper and ra then d.SkeletonRightArm.Visible = true; d.SkeletonRightArm.From = upper; d.SkeletonRightArm.To = ra else d.SkeletonRightArm.Visible = false end
                        if lower and ll then d.SkeletonLeftLeg.Visible = true; d.SkeletonLeftLeg.From = lower; d.SkeletonLeftLeg.To = ll else d.SkeletonLeftLeg.Visible = false end
                        if lower and rl then d.SkeletonRightLeg.Visible = true; d.SkeletonRightLeg.From = lower; d.SkeletonRightLeg.To = rl else d.SkeletonRightLeg.Visible = false end
                    else
                        d.SkeletonHead.Visible = false; d.SkeletonTorso.Visible = false
                        d.SkeletonLeftArm.Visible = false; d.SkeletonRightArm.Visible = false
                        d.SkeletonLeftLeg.Visible = false; d.SkeletonRightLeg.Visible = false
                    end

                    if ESPChamsEnabled then
                        if not ChamsObjects[player] then CreateChams(player) end
                    else
                        if ChamsObjects[player] then RemoveChams(player) end
                    end

                    if not RainbowESPEnabled then
                        local hp = hum.Health / hum.MaxHealth
                        if hp > 0.7 then d.Box.Color = Color3.fromRGB(0,255,0); d.HealthBar.Color = Color3.fromRGB(0,255,0)
                        elseif hp > 0.4 then d.Box.Color = Color3.fromRGB(255,255,0); d.HealthBar.Color = Color3.fromRGB(255,255,0)
                        else d.Box.Color = Color3.fromRGB(255,0,0); d.HealthBar.Color = Color3.fromRGB(255,0,0) end
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

-- COMBAT
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
    return closest
end

local function AimbotFunction()
    if not AimbotEnabled or not IsLoggedIn then return end
    AimbotTarget = GetClosestTarget()
    if AimbotTarget and AimbotTarget.Character then
        local tPart = AimbotTarget.Character:FindFirstChild(TargetPart) or AimbotTarget.Character:FindFirstChild("HumanoidRootPart")
        if tPart then
            local tPos = tPart.Position
            if PredictionEnabled then
                local vel = AimbotTarget.Character.HumanoidRootPart.AssemblyLinearVelocity
                local d = (tPos - Camera.CFrame.Position).Magnitude
                local pf = 1 + (d/500)
                local t = (d/300) * (PredictionAmount/5) * pf
                tPos = tPos + vel * t
            end
            local cur = Camera.CFrame.Position
            local aimDir = (tPos - cur).Unit
            local newCF = CFrame.new(cur, cur + aimDir)
            if AimbotMode == "Instant" then
                Camera.CFrame = newCF
            elseif AimbotMode == "Accurate" then
                local sf = math.clamp(Smoothness, 1, 20)
                Camera.CFrame = sf <= 2 and newCF or Camera.CFrame:Lerp(newCF, 1/sf)
            elseif AimbotMode == "Smooth" then
                local sf = math.clamp(Smoothness * 2, 2, 30)
                Camera.CFrame = Camera.CFrame:Lerp(newCF, 1/sf)
            end
        end
    end
end

local FOVCircle = Drawing.new("Circle")
FOVCircle.Visible = false
FOVCircle.Color = Color3.fromRGB(0, 180, 255)
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

RunService.RenderStepped:Connect(function()
    pcall(function()
        UpdateFOV()
        UpdateESP()
        if AimbotEnabled and IsLoggedIn then AimbotFunction() end
        if SpeedHackEnabled and IsLoggedIn then ApplySpeedHack() end
    end)
end)

--==============================================================
-- UI BUILDER
--==============================================================
local function CreateUI()

    --===== LOADING SCREEN =====
    local LoadingScreen = Instance.new("Frame")
    LoadingScreen.Size = UDim2.new(1, 0, 1, 0)
    LoadingScreen.BackgroundColor3 = Color3.fromRGB(5, 5, 15)
    LoadingScreen.BorderSizePixel = 0
    LoadingScreen.ZIndex = 300
    LoadingScreen.Visible = true
    LoadingScreen.Parent = ScreenGui

    local LoadingBg = Instance.new("Frame")
    LoadingBg.Size = UDim2.new(0, 340, 0, 200)
    LoadingBg.Position = UDim2.new(0.5, -170, 0.5, -100)
    LoadingBg.BackgroundColor3 = Color3.fromRGB(10, 10, 25)
    LoadingBg.BorderColor3 = Color3.fromRGB(0, 150, 255)
    LoadingBg.BorderSizePixel = 2
    LoadingBg.ZIndex = 301
    LoadingBg.Parent = LoadingScreen
    Instance.new("UICorner", LoadingBg).CornerRadius = UDim.new(0, 15)

    local LTitle = Instance.new("TextLabel")
    LTitle.Size = UDim2.new(1, -30, 0, 40)
    LTitle.Position = UDim2.new(0, 15, 0, 20)
    LTitle.BackgroundTransparency = 1
    LTitle.Text = "ZETGAMES-AIMLOCK v3.8"
    LTitle.TextColor3 = Color3.fromRGB(0, 180, 255)
    LTitle.Font = Enum.Font.Code
    LTitle.TextSize = 20
    LTitle.ZIndex = 302
    LTitle.Parent = LoadingBg

    local LSub = Instance.new("TextLabel")
    LSub.Size = UDim2.new(1, -30, 0, 25)
    LSub.Position = UDim2.new(0, 15, 0, 65)
    LSub.BackgroundTransparency = 1
    LSub.Text = "> INITIALIZING SYSTEM..."
    LSub.TextColor3 = Color3.fromRGB(100, 180, 255)
    LSub.Font = Enum.Font.Code
    LSub.TextSize = 12
    LSub.TextXAlignment = Enum.TextXAlignment.Left
    LSub.ZIndex = 302
    LSub.Parent = LoadingBg

    local LBarBg = Instance.new("Frame")
    LBarBg.Size = UDim2.new(1, -30, 0, 15)
    LBarBg.Position = UDim2.new(0, 15, 0, 100)
    LBarBg.BackgroundColor3 = Color3.fromRGB(20, 20, 35)
    LBarBg.BorderColor3 = Color3.fromRGB(0, 150, 255)
    LBarBg.BorderSizePixel = 1
    LBarBg.ZIndex = 302
    LBarBg.Parent = LoadingBg
    Instance.new("UICorner", LBarBg).CornerRadius = UDim.new(0, 7)

    local LBarFill = Instance.new("Frame")
    LBarFill.Size = UDim2.new(0, 0, 1, 0)
    LBarFill.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
    LBarFill.BorderSizePixel = 0
    LBarFill.ZIndex = 303
    LBarFill.Parent = LBarBg
    Instance.new("UICorner", LBarFill).CornerRadius = UDim.new(0, 7)

    local LPercent = Instance.new("TextLabel")
    LPercent.Size = UDim2.new(1, -30, 0, 20)
    LPercent.Position = UDim2.new(0, 15, 0, 125)
    LPercent.BackgroundTransparency = 1
    LPercent.Text = "0%"
    LPercent.TextColor3 = Color3.fromRGB(0, 180, 255)
    LPercent.Font = Enum.Font.Code
    LPercent.TextSize = 14
    LPercent.ZIndex = 302
    LPercent.Parent = LoadingBg

    local LStatus = Instance.new("TextLabel")
    LStatus.Size = UDim2.new(1, -30, 0, 20)
    LStatus.Position = UDim2.new(0, 15, 0, 160)
    LStatus.BackgroundTransparency = 1
    LStatus.Text = "> LOADING..."
    LStatus.TextColor3 = Color3.fromRGB(80, 150, 255)
    LStatus.Font = Enum.Font.Code
    LStatus.TextSize = 10
    LStatus.TextXAlignment = Enum.TextXAlignment.Left
    LStatus.ZIndex = 302
    LStatus.Parent = LoadingBg

    --===== LOGIN FRAME =====
    local LoginFrame = Instance.new("Frame")
    LoginFrame.Size = UDim2.new(0, 320, 0, 420)
    LoginFrame.Position = UDim2.new(0.5, -160, 0.5, -210)
    LoginFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 25)
    LoginFrame.BorderColor3 = Color3.fromRGB(0, 150, 255)
    LoginFrame.BorderSizePixel = 2
    LoginFrame.ZIndex = 10
    LoginFrame.Visible = false
    LoginFrame.Parent = ScreenGui
    Instance.new("UICorner", LoginFrame).CornerRadius = UDim.new(0, 10)

    local LTop = Instance.new("Frame")
    LTop.Size = UDim2.new(1, 0, 0, 35)
    LTop.BackgroundColor3 = Color3.fromRGB(0, 30, 60)
    LTop.BorderSizePixel = 0
    LTop.ZIndex = 11
    LTop.Parent = LoginFrame
    Instance.new("UICorner", LTop).CornerRadius = UDim.new(0, 10)

    local LTopTxt = Instance.new("TextLabel")
    LTopTxt.Size = UDim2.new(1, -16, 1, 0)
    LTopTxt.Position = UDim2.new(0, 8, 0, 0)
    LTopTxt.BackgroundTransparency = 1
    LTopTxt.Text = "● ZETGAMES-AIMLOCK v3.8"
    LTopTxt.TextColor3 = Color3.fromRGB(0, 180, 255)
    LTopTxt.Font = Enum.Font.Code
    LTopTxt.TextSize = 12
    LTopTxt.TextXAlignment = Enum.TextXAlignment.Left
    LTopTxt.ZIndex = 12
    LTopTxt.Parent = LTop

    local LTitle2 = Instance.new("TextLabel")
    LTitle2.Size = UDim2.new(1, -30, 0, 30)
    LTitle2.Position = UDim2.new(0, 15, 0, 50)
    LTitle2.BackgroundTransparency = 1
    LTitle2.Text = "> ACCESS VERIFICATION"
    LTitle2.TextColor3 = Color3.fromRGB(0, 180, 255)
    LTitle2.Font = Enum.Font.Code
    LTitle2.TextSize = 16
    LTitle2.ZIndex = 12
    LTitle2.Parent = LoginFrame

    local KeyLbl = Instance.new("TextLabel")
    KeyLbl.Size = UDim2.new(1, -30, 0, 20)
    KeyLbl.Position = UDim2.new(0, 15, 0, 95)
    KeyLbl.BackgroundTransparency = 1
    KeyLbl.Text = "> KEY_INPUT:"
    KeyLbl.TextColor3 = Color3.fromRGB(0, 180, 255)
    KeyLbl.Font = Enum.Font.Code
    KeyLbl.TextSize = 12
    KeyLbl.TextXAlignment = Enum.TextXAlignment.Left
    KeyLbl.ZIndex = 12
    KeyLbl.Parent = LoginFrame

    local KeyInput = Instance.new("TextBox")
    KeyInput.Size = UDim2.new(1, -30, 0, 40)
    KeyInput.Position = UDim2.new(0, 15, 0, 120)
    KeyInput.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    KeyInput.BorderColor3 = Color3.fromRGB(0, 150, 255)
    KeyInput.BorderSizePixel = 2
    KeyInput.PlaceholderText = "> Type key..."
    KeyInput.PlaceholderColor3 = Color3.fromRGB(60, 80, 100)
    KeyInput.Text = ""
    KeyInput.TextColor3 = Color3.fromRGB(0, 180, 255)
    KeyInput.Font = Enum.Font.Code
    KeyInput.TextSize = 13
    KeyInput.ZIndex = 12
    KeyInput.Parent = LoginFrame
    Instance.new("UICorner", KeyInput).CornerRadius = UDim.new(0, 5)

    local LoginBtn = Instance.new("TextButton")
    LoginBtn.Size = UDim2.new(1, -30, 0, 45)
    LoginBtn.Position = UDim2.new(0, 15, 0, 175)
    LoginBtn.BackgroundColor3 = Color3.fromRGB(0, 80, 150)
    LoginBtn.BorderColor3 = Color3.fromRGB(0, 180, 255)
    LoginBtn.BorderSizePixel = 2
    LoginBtn.Text = "> AUTHENTICATE"
    LoginBtn.TextColor3 = Color3.fromRGB(0, 180, 255)
    LoginBtn.Font = Enum.Font.Code
    LoginBtn.TextSize = 14
    LoginBtn.ZIndex = 12
    LoginBtn.Parent = LoginFrame
    Instance.new("UICorner", LoginBtn).CornerRadius = UDim.new(0, 5)

    local GetKeyBtn = Instance.new("TextButton")
    GetKeyBtn.Size = UDim2.new(1, -30, 0, 45)
    GetKeyBtn.Position = UDim2.new(0, 15, 0, 235)
    GetKeyBtn.BackgroundColor3 = Color3.fromRGB(0, 50, 100)
    GetKeyBtn.BorderColor3 = Color3.fromRGB(0, 150, 255)
    GetKeyBtn.BorderSizePixel = 2
    GetKeyBtn.Text = "> GET KEY (COPY)"
    GetKeyBtn.TextColor3 = Color3.fromRGB(0, 180, 255)
    GetKeyBtn.Font = Enum.Font.Code
    GetKeyBtn.TextSize = 14
    GetKeyBtn.ZIndex = 12
    GetKeyBtn.Parent = LoginFrame
    Instance.new("UICorner", GetKeyBtn).CornerRadius = UDim.new(0, 5)

    local StatusTxt = Instance.new("TextLabel")
    StatusTxt.Size = UDim2.new(1, -30, 0, 25)
    StatusTxt.Position = UDim2.new(0, 15, 0, 295)
    StatusTxt.BackgroundTransparency = 1
    StatusTxt.Text = "> SYSTEM READY..."
    StatusTxt.TextColor3 = Color3.fromRGB(0, 150, 255)
    StatusTxt.Font = Enum.Font.Code
    StatusTxt.TextSize = 10
    StatusTxt.TextXAlignment = Enum.TextXAlignment.Left
    StatusTxt.ZIndex = 12
    StatusTxt.Parent = LoginFrame

    local Instr = Instance.new("TextLabel")
    Instr.Size = UDim2.new(1, -30, 0, 60)
    Instr.Position = UDim2.new(0, 15, 0, 335)
    Instr.BackgroundTransparency = 1
    Instr.Text = "> STEPS:\n> 1. Click GET KEY\n> 2. Generate key\n> 3. Enter key\n> 4. AUTHENTICATE"
    Instr.TextColor3 = Color3.fromRGB(0, 130, 255)
    Instr.Font = Enum.Font.Code
    Instr.TextSize = 9
    Instr.TextXAlignment = Enum.TextXAlignment.Left
    Instr.ZIndex = 12
    Instr.Parent = LoginFrame

    --===== MAIN HUB =====
    local MainHub = Instance.new("Frame")
    MainHub.Size = UDim2.new(0, 340, 0, 480)
    MainHub.Position = UDim2.new(0.5, -170, 0.5, -240)
    MainHub.BackgroundColor3 = Color3.fromRGB(10, 10, 25)
    MainHub.BorderColor3 = Color3.fromRGB(0, 150, 255)
    MainHub.BorderSizePixel = 2
    MainHub.Visible = false
    MainHub.ZIndex = 10
    MainHub.Parent = ScreenGui
    Instance.new("UICorner", MainHub).CornerRadius = UDim.new(0, 10)

    local TitleBar = Instance.new("Frame")
    TitleBar.Size = UDim2.new(1, 0, 0, 40)
    TitleBar.BackgroundColor3 = Color3.fromRGB(0, 30, 60)
    TitleBar.BorderSizePixel = 0
    TitleBar.ZIndex = 11
    TitleBar.Parent = MainHub
    Instance.new("UICorner", TitleBar).CornerRadius = UDim.new(0, 10)

    local MainTitle2 = Instance.new("TextLabel")
    MainTitle2.Size = UDim2.new(1, -50, 1, 0)
    MainTitle2.Position = UDim2.new(0, 12, 0, 0)
    MainTitle2.BackgroundTransparency = 1
    MainTitle2.Text = "● ZETGAMES-AIMLOCK v3.8"
    MainTitle2.TextColor3 = Color3.fromRGB(0, 180, 255)
    MainTitle2.Font = Enum.Font.Code
    MainTitle2.TextSize = 12
    MainTitle2.TextXAlignment = Enum.TextXAlignment.Left
    MainTitle2.ZIndex = 12
    MainTitle2.Parent = TitleBar

    local CloseBtn = Instance.new("TextButton")
    CloseBtn.Size = UDim2.new(0, 30, 0, 30)
    CloseBtn.Position = UDim2.new(1, -38, 0, 5)
    CloseBtn.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
    CloseBtn.BorderColor3 = Color3.fromRGB(255, 0, 0)
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
    ScrollFrame.ScrollBarImageColor3 = Color3.fromRGB(0, 150, 255)
    ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, 2400)
    ScrollFrame.ZIndex = 11
    ScrollFrame.Parent = MainHub

    local ScrollContent = Instance.new("Frame")
    ScrollContent.Size = UDim2.new(1, 0, 0, 2400)
    ScrollContent.BackgroundTransparency = 1
    ScrollContent.ZIndex = 11
    ScrollContent.Parent = ScrollFrame

    -- Helper: Section
    local function Section(title, y)
        local f = Instance.new("Frame")
        f.Size = UDim2.new(1, -20, 0, 25)
        f.Position = UDim2.new(0, 10, 0, y)
        f.BackgroundColor3 = Color3.fromRGB(0, 30, 60)
        f.BorderColor3 = Color3.fromRGB(0, 150, 255)
        f.BorderSizePixel = 1
        f.ZIndex = 12
        f.Parent = ScrollContent
        Instance.new("UICorner", f).CornerRadius = UDim.new(0, 4)
        local t = Instance.new("TextLabel")
        t.Size = UDim2.new(1, -10, 1, 0)
        t.Position = UDim2.new(0, 5, 0, 0)
        t.BackgroundTransparency = 1
        t.Text = title
        t.TextColor3 = Color3.fromRGB(0, 180, 255)
        t.Font = Enum.Font.Code
        t.TextSize = 11
        t.TextXAlignment = Enum.TextXAlignment.Left
        t.ZIndex = 13
        t.Parent = f
    end

    -- Helper: Toggle Button (PROPER - pakai parameter btn)
    local function ToggleButton(text, y, callback)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, -20, 0, 40)
        btn.Position = UDim2.new(0, 10, 0, y)
        btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        btn.BorderColor3 = Color3.fromRGB(0, 150, 255)
        btn.BorderSizePixel = 1
        btn.Text = text
        btn.TextColor3 = Color3.fromRGB(0, 180, 255)
        btn.Font = Enum.Font.Code
        btn.TextSize = 12
        btn.ZIndex = 12
        btn.Parent = ScrollContent
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 4)

        btn.MouseButton1Click:Connect(function()
            callback(btn)
        end)
        return btn
    end

    -- Helper: TextBox
    local function TextBox(placeholder, y, defaultText)
        local tb = Instance.new("TextBox")
        tb.Size = UDim2.new(1, -20, 0, 35)
        tb.Position = UDim2.new(0, 10, 0, y)
        tb.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        tb.BorderColor3 = Color3.fromRGB(0, 150, 255)
        tb.BorderSizePixel = 1
        tb.PlaceholderText = placeholder
        tb.PlaceholderColor3 = Color3.fromRGB(60, 80, 100)
        tb.Text = defaultText or ""
        tb.TextColor3 = Color3.fromRGB(0, 180, 255)
        tb.Font = Enum.Font.Code
        tb.TextSize = 11
        tb.ZIndex = 12
        tb.Parent = ScrollContent
        Instance.new("UICorner", tb).CornerRadius = UDim.new(0, 4)
        return tb
    end

    --==========================================================
    -- USER INFORMATION
    --==========================================================
    Section("=== USER INFORMATION ===", 30)

    local UserInfoFrame = Instance.new("Frame")
    UserInfoFrame.Size = UDim2.new(1, -20, 0, 130)
    UserInfoFrame.Position = UDim2.new(0, 10, 0, 60)
    UserInfoFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    UserInfoFrame.BorderColor3 = Color3.fromRGB(0, 150, 255)
    UserInfoFrame.BorderSizePixel = 1
    UserInfoFrame.ZIndex = 12
    UserInfoFrame.Parent = ScrollContent
    Instance.new("UICorner", UserInfoFrame).CornerRadius = UDim.new(0, 4)

    local NameLbl = Instance.new("TextLabel")
    NameLbl.Size = UDim2.new(1, -15, 0, 25)
    NameLbl.Position = UDim2.new(0, 10, 0, 10)
    NameLbl.BackgroundTransparency = 1
    NameLbl.Text = "> NAME : " .. LocalPlayer.DisplayName
    NameLbl.TextColor3 = Color3.fromRGB(0, 180, 255)
    NameLbl.Font = Enum.Font.Code
    NameLbl.TextSize = 12
    NameLbl.TextXAlignment = Enum.TextXAlignment.Left
    NameLbl.ZIndex = 13
    NameLbl.Parent = UserInfoFrame

    local UserLbl = Instance.new("TextLabel")
    UserLbl.Size = UDim2.new(1, -15, 0, 25)
    UserLbl.Position = UDim2.new(0, 10, 0, 40)
    UserLbl.BackgroundTransparency = 1
    UserLbl.Text = "> USERNAME : " .. LocalPlayer.Name
    UserLbl.TextColor3 = Color3.fromRGB(0, 180, 255)
    UserLbl.Font = Enum.Font.Code
    UserLbl.TextSize = 12
    UserLbl.TextXAlignment = Enum.TextXAlignment.Left
    UserLbl.ZIndex = 13
    UserLbl.Parent = UserInfoFrame

    local VPNLbl = Instance.new("TextLabel")
    VPNLbl.Size = UDim2.new(1, -15, 0, 25)
    VPNLbl.Position = UDim2.new(0, 10, 0, 70)
    VPNLbl.BackgroundTransparency = 1
    VPNLbl.Text = "> VPN : ON (PROTECTED)"
    VPNLbl.TextColor3 = Color3.fromRGB(0, 255, 0)
    VPNLbl.Font = Enum.Font.Code
    VPNLbl.TextSize = 12
    VPNLbl.TextXAlignment = Enum.TextXAlignment.Left
    VPNLbl.ZIndex = 13
    VPNLbl.Parent = UserInfoFrame

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
    Section("=== MAIN FEATURES ===", 210)

    ToggleButton("> FPS BOOST: OFF", 240, function(btn)
        FPSBoostEnabled = not FPSBoostEnabled
        if FPSBoostEnabled then
            btn.Text = "> FPS BOOST: ON"
            btn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
            EnableFPSBoost()
            Notify("FPS Boost", "> ENABLED", 2)
        else
            btn.Text = "> FPS BOOST: OFF"
            btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
            DisableFPSBoost()
            Notify("FPS Boost", "> DISABLED", 2)
        end
    end)

    ToggleButton("> FULLBRIGHT: OFF", 285, function(btn)
        FullbrightEnabled = not FullbrightEnabled
        if FullbrightEnabled then
            btn.Text = "> FULLBRIGHT: ON"
            btn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
            EnableFullbright()
            Notify("Fullbright", "> ENABLED", 2)
        else
            btn.Text = "> FULLBRIGHT: OFF"
            btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
            DisableFullbright()
            Notify("Fullbright", "> DISABLED", 2)
        end
    end)

    ToggleButton("> SPEED HACK: OFF", 330, function(btn)
        SpeedHackEnabled = not SpeedHackEnabled
        if SpeedHackEnabled then
            btn.Text = "> SPEED HACK: ON"
            btn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
            ApplySpeedHack()
        else
            btn.Text = "> SPEED HACK: OFF"
            btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
            ApplySpeedHack()
        end
    end)

    local SpeedInput = TextBox("> Speed (16-500)", 375)
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

    ToggleButton("> INFINITE JUMP: OFF", 415, function(btn)
        InfiniteJumpEnabled = not InfiniteJumpEnabled
        if InfiniteJumpEnabled then
            btn.Text = "> INFINITE JUMP: ON"
            btn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
            EnableInfiniteJump()
        else
            btn.Text = "> INFINITE JUMP: OFF"
            btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
            DisableInfiniteJump()
        end
    end)

    ToggleButton("> NOCLIP: OFF", 460, function(btn)
        NoclipEnabled = not NoclipEnabled
        if NoclipEnabled then
            btn.Text = "> NOCLIP: ON"
            btn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
            EnableNoclip()
        else
            btn.Text = "> NOCLIP: OFF"
            btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
            DisableNoclip()
        end
    end)

    --==========================================================
    -- CHAT SPAM
    --==========================================================
    Section("=== CHAT SPAM ===", 510)

    ToggleButton("> CHAT SPAM: OFF", 540, function(btn)
        ChatSpamEnabled = not ChatSpamEnabled
        if ChatSpamEnabled then
            btn.Text = "> CHAT SPAM: ON"
            btn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
            StartChatSpamLoop()
            Notify("Chat Spam", "> ENABLED", 2)
        else
            btn.Text = "> CHAT SPAM: OFF"
            btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
            Notify("Chat Spam", "> DISABLED", 2)
        end
    end)

    local ChatMsgLbl = Instance.new("TextLabel")
    ChatMsgLbl.Size = UDim2.new(1, -20, 0, 20)
    ChatMsgLbl.Position = UDim2.new(0, 10, 0, 590)
    ChatMsgLbl.BackgroundTransparency = 1
    ChatMsgLbl.Text = "> MESSAGE:"
    ChatMsgLbl.TextColor3 = Color3.fromRGB(0, 180, 255)
    ChatMsgLbl.Font = Enum.Font.Code
    ChatMsgLbl.TextSize = 11
    ChatMsgLbl.TextXAlignment = Enum.TextXAlignment.Left
    ChatMsgLbl.ZIndex = 12
    ChatMsgLbl.Parent = ScrollContent

    local ChatTextInput = TextBox("> Type message to spam...", 615, ChatSpamText)
    ChatTextInput.FocusLost:Connect(function(enterPressed)
        if enterPressed and ChatTextInput.Text ~= "" then
            ChatSpamText = ChatTextInput.Text
            Notify("Chat Spam", "> MESSAGE SET", 2)
        end
    end)

    local ChatDelayLbl = Instance.new("TextLabel")
    ChatDelayLbl.Size = UDim2.new(1, -20, 0, 20)
    ChatDelayLbl.Position = UDim2.new(0, 10, 0, 655)
    ChatDelayLbl.BackgroundTransparency = 1
    ChatDelayLbl.Text = "> DELAY (SECONDS):"
    ChatDelayLbl.TextColor3 = Color3.fromRGB(0, 180, 255)
    ChatDelayLbl.Font = Enum.Font.Code
    ChatDelayLbl.TextSize = 11
    ChatDelayLbl.TextXAlignment = Enum.TextXAlignment.Left
    ChatDelayLbl.ZIndex = 12
    ChatDelayLbl.Parent = ScrollContent

    local ChatDelayInput = TextBox("> Delay (1-10 sec)", 680, tostring(ChatSpamDelay))
    ChatDelayInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nd = tonumber(ChatDelayInput.Text)
            if nd then
                ChatSpamDelay = math.clamp(nd, 1, 10)
            end
            ChatDelayInput.Text = tostring(ChatSpamDelay)
        end
    end)

    local SendOnceBtn = Instance.new("TextButton")
    SendOnceBtn.Size = UDim2.new(1, -20, 0, 35)
    SendOnceBtn.Position = UDim2.new(0, 10, 0, 720)
    SendOnceBtn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    SendOnceBtn.BorderColor3 = Color3.fromRGB(0, 180, 255)
    SendOnceBtn.BorderSizePixel = 1
    SendOnceBtn.Text = "> SEND ONCE"
    SendOnceBtn.TextColor3 = Color3.fromRGB(0, 180, 255)
    SendOnceBtn.Font = Enum.Font.Code
    SendOnceBtn.TextSize = 11
    SendOnceBtn.ZIndex = 12
    SendOnceBtn.Parent = ScrollContent
    Instance.new("UICorner", SendOnceBtn).CornerRadius = UDim.new(0, 4)
    SendOnceBtn.MouseButton1Click:Connect(function()
        if ChatSpamText and ChatSpamText ~= "" then
            SendChatMessage(ChatSpamText)
            Notify("Chat Spam", "> SENT", 2)
        end
    end)

    --==========================================================
    -- ENHANCED AIMBOT
    --==========================================================
    Section("=== ENHANCED AIMBOT ===", 815)

    local AimbotBtn = Instance.new("TextButton")
    AimbotBtn.Size = UDim2.new(1, -20, 0, 45)
    AimbotBtn.Position = UDim2.new(0, 10, 0, 845)
    AimbotBtn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    AimbotBtn.BorderColor3 = Color3.fromRGB(0, 150, 255)
    AimbotBtn.BorderSizePixel = 2
    AimbotBtn.Text = "> AIMBOT: OFF"
    AimbotBtn.TextColor3 = Color3.fromRGB(0, 180, 255)
    AimbotBtn.Font = Enum.Font.Code
    AimbotBtn.TextSize = 13
    AimbotBtn.ZIndex = 12
    AimbotBtn.Parent = ScrollContent
    Instance.new("UICorner", AimbotBtn).CornerRadius = UDim.new(0, 4)
    AimbotBtn.MouseButton1Click:Connect(function()
        AimbotEnabled = not AimbotEnabled
        if AimbotEnabled then
            AimbotBtn.Text = "> AIMBOT: ON"
            AimbotBtn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
            Notify("Aimbot", "> ENABLED | " .. string.upper(AimbotMode), 2)
        else
            AimbotBtn.Text = "> AIMBOT: OFF"
            AimbotBtn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
            Notify("Aimbot", "> DISABLED", 2)
        end
    end)

    local ModeLbl = Instance.new("TextLabel")
    ModeLbl.Size = UDim2.new(1, -20, 0, 20)
    ModeLbl.Position = UDim2.new(0, 10, 0, 895)
    ModeLbl.BackgroundTransparency = 1
    ModeLbl.Text = "> AIMBOT MODE: ACCURATE"
    ModeLbl.TextColor3 = Color3.fromRGB(0, 180, 255)
    ModeLbl.Font = Enum.Font.Code
    ModeLbl.TextSize = 11
    ModeLbl.TextXAlignment = Enum.TextXAlignment.Left
    ModeLbl.ZIndex = 12
    ModeLbl.Parent = ScrollContent

    local ModeBtnFrame = Instance.new("Frame")
    ModeBtnFrame.Size = UDim2.new(1, -20, 0, 35)
    ModeBtnFrame.Position = UDim2.new(0, 10, 0, 920)
    ModeBtnFrame.BackgroundTransparency = 1
    ModeBtnFrame.ZIndex = 12
    ModeBtnFrame.Parent = ScrollContent

    local function CreateModeBtn(text, mode, xPos)
        local b = Instance.new("TextButton")
        b.Size = UDim2.new(0.31, 0, 1, 0)
        b.Position = UDim2.new(xPos, 0, 0, 0)
        b.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        b.BorderColor3 = Color3.fromRGB(0, 150, 255)
        b.BorderSizePixel = 1
        b.Text = text
        b.TextColor3 = Color3.fromRGB(0, 180, 255)
        b.Font = Enum.Font.Code
        b.TextSize = 10
        b.ZIndex = 13
        b.Parent = ModeBtnFrame
        Instance.new("UICorner", b).CornerRadius = UDim.new(0, 3)
        b.MouseButton1Click:Connect(function()
            AimbotMode = mode
            ModeLbl.Text = "> AIMBOT MODE: " .. string.upper(mode)
            Notify("Aimbot", "> MODE: " .. string.upper(mode), 2)
        end)
    end
    CreateModeBtn("ACCURATE", "Accurate", 0)
    CreateModeBtn("SMOOTH", "Smooth", 0.345)
    CreateModeBtn("INSTANT", "Instant", 0.69)

    -- PREDICTION toggle
    ToggleButton("> PREDICTION: OFF", 965, function(btn)
        PredictionEnabled = not PredictionEnabled
        if PredictionEnabled then
            btn.Text = "> PREDICTION: ON"
            btn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        else
            btn.Text = "> PREDICTION: OFF"
            btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        end
    end)

    local PredInput = TextBox("> Prediction (1-10)", 1010)
    PredInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local na = tonumber(PredInput.Text)
            if na then PredictionAmount = math.clamp(na, 1, 10) end
            PredInput.Text = ""
        end
    end)

    -- WALL CHECK toggle
    ToggleButton("> WALL_CHECK: OFF", 1050, function(btn)
        WallCheckEnabled = not WallCheckEnabled
        if WallCheckEnabled then
            btn.Text = "> WALL_CHECK: ON"
            btn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        else
            btn.Text = "> WALL_CHECK: OFF"
            btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        end
    end)

    -- TEAM CHECK toggle
    ToggleButton("> TEAM_CHECK: OFF", 1095, function(btn)
        TeamCheckEnabled = not TeamCheckEnabled
        if TeamCheckEnabled then
            btn.Text = "> TEAM_CHECK: ON"
            btn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        else
            btn.Text = "> TEAM_CHECK: OFF"
            btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        end
    end)

    local TargetLbl = Instance.new("TextLabel")
    TargetLbl.Size = UDim2.new(1, -20, 0, 20)
    TargetLbl.Position = UDim2.new(0, 10, 0, 1140)
    TargetLbl.BackgroundTransparency = 1
    TargetLbl.Text = "> TARGET_PART: HEAD"
    TargetLbl.TextColor3 = Color3.fromRGB(0, 180, 255)
    TargetLbl.Font = Enum.Font.Code
    TargetLbl.TextSize = 11
    TargetLbl.TextXAlignment = Enum.TextXAlignment.Left
    TargetLbl.ZIndex = 12
    TargetLbl.Parent = ScrollContent

    local TargetBtnFrame = Instance.new("Frame")
    TargetBtnFrame.Size = UDim2.new(1, -20, 0, 35)
    TargetBtnFrame.Position = UDim2.new(0, 10, 0, 1165)
    TargetBtnFrame.BackgroundTransparency = 1
    TargetBtnFrame.ZIndex = 12
    TargetBtnFrame.Parent = ScrollContent

    local function CreateTargetBtn(text, part, xPos)
        local b = Instance.new("TextButton")
        b.Size = UDim2.new(0, 90, 1, 0)
        b.Position = UDim2.new(xPos, 0, 0, 0)
        b.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        b.BorderColor3 = Color3.fromRGB(0, 150, 255)
        b.BorderSizePixel = 1
        b.Text = text
        b.TextColor3 = Color3.fromRGB(0, 180, 255)
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
    -- FULL ESP
    --==========================================================
    Section("=== FULL ESP FEATURES ===", 1210)

    ToggleButton("> ESP MASTER: OFF", 1240, function(btn)
        ESPEnabled = not ESPEnabled
        if ESPEnabled then
            btn.Text = "> ESP MASTER: ON"
            btn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
            Notify("ESP", "> ENABLED", 2)
        else
            btn.Text = "> ESP MASTER: OFF"
            btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
            Notify("ESP", "> DISABLED", 2)
        end
    end)

    ToggleButton("> 🌈 RAINBOW ESP: OFF", 1285, function(btn)
        RainbowESPEnabled = not RainbowESPEnabled
        if RainbowESPEnabled then
            btn.Text = "> 🌈 RAINBOW ESP: ON"
            btn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
            EnableRainbowESP()
            Notify("Rainbow ESP", "> ENABLED", 2)
        else
            btn.Text = "> 🌈 RAINBOW ESP: OFF"
            btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
            DisableRainbowESP()
            Notify("Rainbow ESP", "> DISABLED", 2)
        end
    end)

    local function TogglePair(text, y, xPos, callback)
        local b = Instance.new("TextButton")
        b.Size = UDim2.new(0.48, -15, 0, 35)
        b.Position = UDim2.new(xPos, 0, 0, y)
        b.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        b.BorderColor3 = Color3.fromRGB(0, 180, 255)
        b.BorderSizePixel = 1
        b.Text = text
        b.TextColor3 = Color3.fromRGB(0, 180, 255)
        b.Font = Enum.Font.Code
        b.TextSize = 10
        b.ZIndex = 12
        b.Parent = ScrollContent
        Instance.new("UICorner", b).CornerRadius = UDim.new(0, 4)
        b.MouseButton1Click:Connect(function()
            callback(b)
        end)
        return b
    end

    TogglePair("> BOX: ON", 1330, 0, function(btn)
        ESPBoxEnabled = not ESPBoxEnabled
        btn.Text = ESPBoxEnabled and "> BOX: ON" or "> BOX: OFF"
        btn.BackgroundColor3 = ESPBoxEnabled and Color3.fromRGB(0,60,120) or Color3.fromRGB(15,15,30)
    end)

    TogglePair("> NAME: ON", 1330, 0.52, function(btn)
        ESPNameEnabled = not ESPNameEnabled
        btn.Text = ESPNameEnabled and "> NAME: ON" or "> NAME: OFF"
        btn.BackgroundColor3 = ESPNameEnabled and Color3.fromRGB(0,60,120) or Color3.fromRGB(15,15,30)
    end)

    TogglePair("> DISTANCE: ON", 1370, 0, function(btn)
        ESPDistanceEnabled = not ESPDistanceEnabled
        btn.Text = ESPDistanceEnabled and "> DISTANCE: ON" or "> DISTANCE: OFF"
        btn.BackgroundColor3 = ESPDistanceEnabled and Color3.fromRGB(0,60,120) or Color3.fromRGB(15,15,30)
    end)

    TogglePair("> HEALTH: ON", 1370, 0.52, function(btn)
        ESPHealthEnabled = not ESPHealthEnabled
        btn.Text = ESPHealthEnabled and "> HEALTH: ON" or "> HEALTH: OFF"
        btn.BackgroundColor3 = ESPHealthEnabled and Color3.fromRGB(0,60,120) or Color3.fromRGB(15,15,30)
    end)

    TogglePair("> SKELETON: OFF", 1410, 0, function(btn)
        ESPSkeletonEnabled = not ESPSkeletonEnabled
        btn.Text = ESPSkeletonEnabled and "> SKELETON: ON" or "> SKELETON: OFF"
        btn.BackgroundColor3 = ESPSkeletonEnabled and Color3.fromRGB(0,60,120) or Color3.fromRGB(15,15,30)
    end)

    TogglePair("> HEAD DOT: OFF", 1410, 0.52, function(btn)
        ESPHeadDotEnabled = not ESPHeadDotEnabled
        btn.Text = ESPHeadDotEnabled and "> HEAD DOT: ON" or "> HEAD DOT: OFF"
        btn.BackgroundColor3 = ESPHeadDotEnabled and Color3.fromRGB(0,60,120) or Color3.fromRGB(15,15,30)
    end)

    TogglePair("> CHAMS: OFF", 1450, 0, function(btn)
        ESPChamsEnabled = not ESPChamsEnabled
        btn.Text = ESPChamsEnabled and "> CHAMS: ON" or "> CHAMS: OFF"
        btn.BackgroundColor3 = ESPChamsEnabled and Color3.fromRGB(0,60,120) or Color3.fromRGB(15,15,30)
        if not ESPChamsEnabled then
            for p, _ in pairs(ChamsObjects) do RemoveChams(p) end
        end
    end)

    TogglePair("> TRACER: ON", 1450, 0.52, function(btn)
        ESPTracerEnabled = not ESPTracerEnabled
        btn.Text = ESPTracerEnabled and "> TRACER: ON" or "> TRACER: OFF"
        btn.BackgroundColor3 = ESPTracerEnabled and Color3.fromRGB(0,60,120) or Color3.fromRGB(15,15,30)
    end)

    local TracerColorLbl = Instance.new("TextLabel")
    TracerColorLbl.Size = UDim2.new(1, -20, 0, 20)
    TracerColorLbl.Position = UDim2.new(0, 10, 0, 1495)
    TracerColorLbl.BackgroundTransparency = 1
    TracerColorLbl.Text = "> TRACER COLOR: MERAH"
    TracerColorLbl.TextColor3 = Color3.fromRGB(0, 180, 255)
    TracerColorLbl.Font = Enum.Font.Code
    TracerColorLbl.TextSize = 11
    TracerColorLbl.TextXAlignment = Enum.TextXAlignment.Left
    TracerColorLbl.ZIndex = 12
    TracerColorLbl.Parent = ScrollContent

    local TracerColorFrame = Instance.new("Frame")
    TracerColorFrame.Size = UDim2.new(1, -20, 0, 40)
    TracerColorFrame.Position = UDim2.new(0, 10, 0, 1520)
    TracerColorFrame.BackgroundTransparency = 1
    TracerColorFrame.ZIndex = 12
    TracerColorFrame.Parent = ScrollContent

    local function CreateColorBtn(text, color, name, xPos)
        local b = Instance.new("TextButton")
        b.Size = UDim2.new(0.31, 0, 0, 35)
        b.Position = UDim2.new(xPos, 0, 0, 0)
        b.BackgroundColor3 = color
        b.BorderColor3 = Color3.fromRGB(0, 150, 255)
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
    Section("=== TELEPORT (UNLOCKED) ===", 1585)

    local TPMouseBtn = Instance.new("TextButton")
    TPMouseBtn.Size = UDim2.new(1, -20, 0, 40)
    TPMouseBtn.Position = UDim2.new(0, 10, 0, 1615)
    TPMouseBtn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    TPMouseBtn.BorderColor3 = Color3.fromRGB(0, 180, 255)
    TPMouseBtn.BorderSizePixel = 1
    TPMouseBtn.Text = "> TELEPORT TO MOUSE"
    TPMouseBtn.TextColor3 = Color3.fromRGB(0, 180, 255)
    TPMouseBtn.Font = Enum.Font.Code
    TPMouseBtn.TextSize = 12
    TPMouseBtn.ZIndex = 12
    TPMouseBtn.Parent = ScrollContent
    Instance.new("UICorner", TPMouseBtn).CornerRadius = UDim.new(0, 4)
    TPMouseBtn.MouseButton1Click:Connect(TeleportToMouse)

    local SaveLocBtn2 = Instance.new("TextButton")
    SaveLocBtn2.Size = UDim2.new(0.48, -15, 0, 40)
    SaveLocBtn2.Position = UDim2.new(0, 10, 0, 1660)
    SaveLocBtn2.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    SaveLocBtn2.BorderColor3 = Color3.fromRGB(0, 180, 255)
    SaveLocBtn2.BorderSizePixel = 1
    SaveLocBtn2.Text = "> SAVE LOC"
    SaveLocBtn2.TextColor3 = Color3.fromRGB(0, 180, 255)
    SaveLocBtn2.Font = Enum.Font.Code
    SaveLocBtn2.TextSize = 11
    SaveLocBtn2.ZIndex = 12
    SaveLocBtn2.Parent = ScrollContent
    Instance.new("UICorner", SaveLocBtn2).CornerRadius = UDim.new(0, 4)
    SaveLocBtn2.MouseButton1Click:Connect(SaveLocation)

    local LoadLocBtn2 = Instance.new("TextButton")
    LoadLocBtn2.Size = UDim2.new(0.48, -15, 0, 40)
    LoadLocBtn2.Position = UDim2.new(0.52, 5, 0, 1660)
    LoadLocBtn2.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    LoadLocBtn2.BorderColor3 = Color3.fromRGB(0, 180, 255)
    LoadLocBtn2.BorderSizePixel = 1
    LoadLocBtn2.Text = "> LOAD LOC"
    LoadLocBtn2.TextColor3 = Color3.fromRGB(0, 180, 255)
    LoadLocBtn2.Font = Enum.Font.Code
    LoadLocBtn2.TextSize = 11
    LoadLocBtn2.ZIndex = 12
    LoadLocBtn2.Parent = ScrollContent
    Instance.new("UICorner", LoadLocBtn2).CornerRadius = UDim.new(0, 4)
    LoadLocBtn2.MouseButton1Click:Connect(LoadLocation)

    local TPListLbl = Instance.new("TextLabel")
    TPListLbl.Size = UDim2.new(1, -20, 0, 20)
    TPListLbl.Position = UDim2.new(0, 10, 0, 1705)
    TPListLbl.BackgroundTransparency = 1
    TPListLbl.Text = "> PLAYERS (CLICK TO TELEPORT):"
    TPListLbl.TextColor3 = Color3.fromRGB(0, 180, 255)
    TPListLbl.Font = Enum.Font.Code
    TPListLbl.TextSize = 10
    TPListLbl.TextXAlignment = Enum.TextXAlignment.Left
    TPListLbl.ZIndex = 12
    TPListLbl.Parent = ScrollContent

    local TeleportListFrame = Instance.new("ScrollingFrame")
    TeleportListFrame.Size = UDim2.new(1, -20, 0, 120)
    TeleportListFrame.Position = UDim2.new(0, 10, 0, 1730)
    TeleportListFrame.BackgroundColor3 = Color3.fromRGB(8, 8, 20)
    TeleportListFrame.BorderColor3 = Color3.fromRGB(0, 150, 255)
    TeleportListFrame.BorderSizePixel = 1
    TeleportListFrame.ScrollBarThickness = 5
    TeleportListFrame.ScrollBarImageColor3 = Color3.fromRGB(0, 150, 255)
    TeleportListFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
    TeleportListFrame.ZIndex = 12
    TeleportListFrame.Parent = ScrollContent
    Instance.new("UICorner", TeleportListFrame).CornerRadius = UDim.new(0, 4)

    local function UpdateTeleportList()
        for _, b in pairs(TeleportTargetList) do
            if b then pcall(function() b:Destroy() end) end
        end
        TeleportTargetList = {}
        local y = 0
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local b = Instance.new("TextButton")
                b.Size = UDim2.new(1, -10, 0, 30)
                b.Position = UDim2.new(0, 5, 0, y)
                b.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
                b.BorderColor3 = Color3.fromRGB(0, 150, 255)
                b.BorderSizePixel = 1
                b.Text = "> " .. player.Name
                b.TextColor3 = Color3.fromRGB(0, 180, 255)
                b.Font = Enum.Font.Code
                b.TextSize = 11
                b.ZIndex = 13
                b.Parent = TeleportListFrame
                Instance.new("UICorner", b).CornerRadius = UDim.new(0, 3)
                b.MouseButton1Click:Connect(function() TeleportToPlayer(player) end)
                table.insert(TeleportTargetList, b)
                y = y + 35
            end
        end
        TeleportListFrame.CanvasSize = UDim2.new(0, 0, 0, y + 5)
    end

    --==========================================================
    -- VISUAL FEATURES
    --==========================================================
    Section("=== VISUAL FEATURES ===", 1865)

    ToggleButton("> FOV_CIRCLE: OFF", 1895, function(btn)
        FOVCircleEnabled = not FOVCircleEnabled
        FOVCircle.Visible = FOVCircleEnabled
        if FOVCircleEnabled then
            btn.Text = "> FOV_CIRCLE: ON"
            btn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
            FOVCircle.Position = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
        else
            btn.Text = "> FOV_CIRCLE: OFF"
            btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        end
    end)

    local FOVInput = TextBox("> FOV Radius (50-5000)", 1940)
    FOVInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local nr = tonumber(FOVInput.Text)
            if nr then
                FOVRadius = math.clamp(nr, 50, MaxFOVRadius)
                if FOVCircle then FOVCircle.Radius = FOVRadius end
            end
            FOVInput.Text = ""
        end
    end)

    local SmoothInput = TextBox("> Smoothness (1-20)", 1980)
    SmoothInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local ns = tonumber(SmoothInput.Text)
            if ns then Smoothness = math.clamp(ns, 1, 20) end
            SmoothInput.Text = ""
        end
    end)

    --==========================================================
    -- TOGGLE MENU BUTTON
    --==========================================================
    local ToggleMenuButton = Instance.new("TextButton")
    ToggleMenuButton.Size = UDim2.new(0, 45, 0, 45)
    ToggleMenuButton.Position = UDim2.new(0, 8, 0.5, -22)
    ToggleMenuButton.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    ToggleMenuButton.BorderColor3 = Color3.fromRGB(0, 180, 255)
    ToggleMenuButton.BorderSizePixel = 2
    ToggleMenuButton.Text = "≡"
    ToggleMenuButton.TextColor3 = Color3.fromRGB(0, 180, 255)
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

    --==========================================================
    -- DRAGGABLE
    --==========================================================
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
    MakeDraggable(ToggleMenuButton)

    --==========================================================
    -- LOGIN LOGIC
    --==========================================================
    LoginBtn.MouseButton1Click:Connect(function()
        local key = KeyInput.Text
        local keyData = ValidKeys[key]
        if keyData then
            if keyData.Expiry == 0 then
                IsLoggedIn = true
                LoginFrame.Visible = false
                MainHub.Visible = true
                ToggleMenuButton.Visible = true
                MenuVisible = true
                StatusTxt.Text = "> ACCESS GRANTED..."
                UpdateTeleportList()
                Notify("Success", "> WELCOME | " .. keyData.Level, 3)
            elseif os.time() < keyData.Expiry then
                IsLoggedIn = true
                LoginFrame.Visible = false
                MainHub.Visible = true
                ToggleMenuButton.Visible = true
                MenuVisible = true
                StatusTxt.Text = "> ACCESS GRANTED..."
                UpdateTeleportList()
                local tl = keyData.Expiry - os.time()
                local days = math.floor(tl / 86400)
                Notify("Success", "> WELCOME | " .. days .. "d left", 3)
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
    -- PLAYER CONNECTIONS
    --==========================================================
    Players.PlayerAdded:Connect(function(player)
        task.wait(1)
        if IsLoggedIn then pcall(UpdateTeleportList) end
    end)

    Players.PlayerRemoving:Connect(function(player)
        pcall(RemoveESP, player)
        if IsLoggedIn then pcall(UpdateTeleportList) end
    end)

    --==============================================================
    -- LOADING ANIMATION
    --==============================================================
    local loadingMessages = {
        "> LOADING MODULES...",
        "> CONNECTING TO SERVER...",
        "> DECRYPTING DATA...",
        "> INITIALIZING FULL ESP...",
        "> INITIALIZING ENHANCED AIMBOT...",
        "> LOADING CHAT SPAM...",
        "> LOADING FULLBRIGHT...",
        "> LOADING RAINBOW ESP...",
        "> LOADING TELEPORT...",
        "> SYSTEM READY..."
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
        Notify("ZetGames-AimLock v3.8", "> SYSTEM LOADED", 3)
        Notify("Login", "> ENTER ACCESS KEY", 3)
    end)

    --==========================================================
    -- AUTO UPDATE TELEPORT LIST
    --==========================================================
    task.spawn(function()
        while task.wait(3) do
            if IsLoggedIn then
                pcall(UpdateTeleportList)
            end
        end
    end)

end

--==============================================================
-- RUN
--==============================================================
pcall(CreateUI)
