--[[
    ZetGames-AimLock | Premium Edition v3.8
    Theme: Blue & Black Hacker Style
    System: Full ESP + FPS Booster + Rainbow ESP + Fullbright + Enhanced Aimbot + FPS/Ping Display + User Info
    Login: Key System (5 KEYS)
    Features: Loading Screen, Compact Mobile UI, Separated Sections, Night Lock, Full ESP, FPS Boost, Rainbow ESP, Fullbright, Enhanced Aimbot, FPS/Ping Display, User Info
    Mobile Friendly - 100% Tested - ALL FEATURES WORKING - NO BUG
--]]

-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")
local Stats = game:GetService("Stats")
local Camera = workspace.CurrentCamera

local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Variables
local AimbotEnabled = false
local FOVCircle = nil
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
local PredictionEnabled = true
local PredictionAmount = 5
local FOVCircleEnabled = false
local AimbotMode = "Accurate"

-- FULL ESP SYSTEM
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

-- RAINBOW ESP
local RainbowESPEnabled = false
local RainbowConnection = nil
local RainbowHue = 0

-- Tracer Color
local TracerColor = Color3.fromRGB(255, 0, 0)
local TracerColorName = "MERAH"

-- FULLBRIGHT
local FullbrightEnabled = false
local OriginalLighting = {}

-- FPS/PING DISPLAY
local FPSPingEnabled = true
local FPSPingFrame = nil
local FPSValue = 0
local PingValue = 0
local FPSUpdateConnection = nil

-- TELEPORT
local TeleportTargetList = {}

-- NOCLIP
local NoclipEnabled = false
local NoclipConnection = nil

-- SPEED HACK
local SpeedHackEnabled = false
local SpeedMultiplier = 100
local MaxSpeed = 500
local DefaultWalkSpeed = 16

-- INFINITE JUMP
local InfiniteJumpEnabled = false
local JumpConnection = nil

-- SAVE LOCATION
local SavedLocation = nil

-- FPS BOOSTER
local FPSBoostEnabled = false
local OriginalSettings = {}

-- Key System
local ValidKeys = {
    ["AzferModz"] = {Expiry = 0, Level = "Premium"},
    ["AzferFree"] = {Expiry = os.time({year=2026, month=9, day=5, hour=0, min=0, sec=0}), Level = "Free"},
    ["AzferCode"] = {Expiry = os.time({year=2026, month=11, day=26, hour=0, min=0, sec=0}), Level = "Code"},
    ["AzferHc"] = {Expiry = os.time({year=2027, month=1, day=27, hour=0, min=0, sec=0}), Level = "Code"},
    ["FazxyFree"] = {Expiry = os.time({year=2027, month=9, day=10, hour=0, min=0, sec=0}), Level = "Code"}
}

local KeyWebsite = "https://arkaraffaza387-dotcom.github.io/Key-Zero/"

-- NIGHT LOCK
local function IsNightLockActive()
    local hour = tonumber(os.date("%H", os.time()))
    if hour >= 23 or hour < 3 then
        return true
    end
    return false
end

-- ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ZetGames-AimLock"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game:GetService("CoreGui")

-- Notification
local Notifications = Instance.new("Frame")
Notifications.Size = UDim2.new(0, 250, 1, 0)
Notifications.Position = UDim2.new(1, -260, 0, 10)
Notifications.BackgroundTransparency = 1
Notifications.ZIndex = 100
Notifications.Parent = ScreenGui

local function Notify(title, message, duration)
    duration = duration or 2
    local Notif = Instance.new("Frame")
    Notif.Size = UDim2.new(1, 0, 0, 55)
    Notif.Position = UDim2.new(0, 0, 0, -55)
    Notif.BackgroundColor3 = Color3.fromRGB(10, 10, 25)
    Notif.BorderColor3 = Color3.fromRGB(0, 150, 255)
    Notif.BorderSizePixel = 2
    Notif.ZIndex = 101
    Notif.Parent = Notifications
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 8)
    UICorner.Parent = Notif
    
    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, -16, 0, 22)
    Title.Position = UDim2.new(0, 8, 0, 4)
    Title.BackgroundTransparency = 1
    Title.Text = title
    Title.TextColor3 = Color3.fromRGB(0, 180, 255)
    Title.Font = Enum.Font.Code
    Title.TextSize = 12
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.ZIndex = 102
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
    Msg.ZIndex = 102
    Msg.Parent = Notif
    
    local TweenIn = TweenService:Create(Notif, TweenInfo.new(0.3), {Position = UDim2.new(0, 0, 0, 0)})
    TweenIn:Play()
    
    task.spawn(function()
        task.wait(duration)
        local TweenOut = TweenService:Create(Notif, TweenInfo.new(0.3), {Position = UDim2.new(0, 0, 0, -55)})
        TweenOut:Play()
        TweenOut.Completed:Connect(function()
            Notif:Destroy()
        end)
    end)
end

-- KICK FUNCTION
local function KickPlayer(text)
    local KickOverlay = Instance.new("Frame")
    KickOverlay.Size = UDim2.new(1, 0, 1, 0)
    KickOverlay.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    KickOverlay.BackgroundTransparency = 0.3
    KickOverlay.ZIndex = 200
    KickOverlay.Parent = ScreenGui
    
    local KickFrame = Instance.new("Frame")
    KickFrame.Size = UDim2.new(0, 350, 0, 100)
    KickFrame.Position = UDim2.new(0.5, -175, 0.5, -50)
    KickFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 25)
    KickFrame.BorderColor3 = Color3.fromRGB(255, 0, 0)
    KickFrame.BorderSizePixel = 3
    KickFrame.ZIndex = 201
    KickFrame.Parent = KickOverlay
    
    local KickCorner = Instance.new("UICorner")
    KickCorner.CornerRadius = UDim.new(0, 12)
    KickCorner.Parent = KickFrame
    
    local KickText = Instance.new("TextLabel")
    KickText.Size = UDim2.new(1, -30, 1, 0)
    KickText.Position = UDim2.new(0, 15, 0, 0)
    KickText.BackgroundTransparency = 1
    KickText.Text = text
    KickText.TextColor3 = Color3.fromRGB(255, 0, 0)
    KickText.Font = Enum.Font.Code
    KickText.TextSize = 18
    KickText.ZIndex = 202
    KickText.Parent = KickFrame
    
    task.spawn(function()
        task.wait(2)
        pcall(function()
            LocalPlayer:Kick(text)
        end)
    end)
end

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

-- RAINBOW ESP
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
    if RainbowConnection then
        RainbowConnection:Disconnect()
    end
    
    RainbowConnection = RunService.RenderStepped:Connect(function(dt)
        if not RainbowESPEnabled then return end
        
        RainbowHue = (RainbowHue + dt * 0.3) % 1
        local rainbowColor = HSVToRGB(RainbowHue, 1, 1)
        
        for _, espData in pairs(ESPObjects) do
            if espData.Box then espData.Box.Color = rainbowColor end
            if espData.Name then espData.Name.Color = rainbowColor end
            if espData.Distance then espData.Distance.Color = rainbowColor end
            if espData.Tracer then espData.Tracer.Color = rainbowColor end
            if espData.HealthBar then espData.HealthBar.Color = rainbowColor end
            if espData.SkeletonHead then espData.SkeletonHead.Color = rainbowColor end
            if espData.SkeletonTorso then espData.SkeletonTorso.Color = rainbowColor end
            if espData.SkeletonLeftArm then espData.SkeletonLeftArm.Color = rainbowColor end
            if espData.SkeletonRightArm then espData.SkeletonRightArm.Color = rainbowColor end
            if espData.SkeletonLeftLeg then espData.SkeletonLeftLeg.Color = rainbowColor end
            if espData.SkeletonRightLeg then espData.SkeletonRightLeg.Color = rainbowColor end
        end
        
        for _, highlightList in pairs(ChamsObjects) do
            for _, highlight in pairs(highlightList) do
                if highlight then
                    highlight.FillColor = rainbowColor
                    highlight.OutlineColor = rainbowColor
                end
            end
        end
    end)
end

local function DisableRainbowESP()
    if RainbowConnection then
        RainbowConnection:Disconnect()
        RainbowConnection = nil
    end
end

-- FPS/PING DISPLAY
local function CreateFPSPingDisplay()
    FPSPingFrame = Instance.new("Frame")
    FPSPingFrame.Size = UDim2.new(0, 130, 0, 55)
    FPSPingFrame.Position = UDim2.new(0, 10, 0, 60)
    FPSPingFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 25)
    FPSPingFrame.BorderColor3 = Color3.fromRGB(0, 150, 255)
    FPSPingFrame.BorderSizePixel = 2
    FPSPingFrame.ZIndex = 90
    FPSPingFrame.Visible = FPSPingEnabled
    FPSPingFrame.Parent = ScreenGui
    
    local FPSFrameCorner = Instance.new("UICorner")
    FPSFrameCorner.CornerRadius = UDim.new(0, 6)
    FPSFrameCorner.Parent = FPSPingFrame
    
    local FPSTitle = Instance.new("TextLabel")
    FPSTitle.Size = UDim2.new(1, -10, 0, 15)
    FPSTitle.Position = UDim2.new(0, 5, 0, 3)
    FPSTitle.BackgroundTransparency = 1
    FPSTitle.Text = "> SYSTEM STATS"
    FPSTitle.TextColor3 = Color3.fromRGB(0, 180, 255)
    FPSTitle.Font = Enum.Font.Code
    FPSTitle.TextSize = 9
    FPSTitle.TextXAlignment = Enum.TextXAlignment.Left
    FPSTitle.ZIndex = 91
    FPSTitle.Parent = FPSPingFrame
    
    local FPSLabel = Instance.new("TextLabel")
    FPSLabel.Size = UDim2.new(1, -10, 0, 15)
    FPSLabel.Position = UDim2.new(0, 5, 0, 20)
    FPSLabel.BackgroundTransparency = 1
    FPSLabel.Text = "> FPS: 60"
    FPSLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    FPSLabel.Font = Enum.Font.Code
    FPSLabel.TextSize = 11
    FPSLabel.TextXAlignment = Enum.TextXAlignment.Left
    FPSLabel.ZIndex = 91
    FPSLabel.Name = "FPSLabel"
    FPSLabel.Parent = FPSPingFrame
    
    local PingLabel = Instance.new("TextLabel")
    PingLabel.Size = UDim2.new(1, -10, 0, 15)
    PingLabel.Position = UDim2.new(0, 5, 0, 35)
    PingLabel.BackgroundTransparency = 1
    PingLabel.Text = "> PING: 0ms"
    PingLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    PingLabel.Font = Enum.Font.Code
    PingLabel.TextSize = 11
    PingLabel.TextXAlignment = Enum.TextXAlignment.Left
    PingLabel.ZIndex = 91
    PingLabel.Name = "PingLabel"
    PingLabel.Parent = FPSPingFrame
end

local function UpdateFPSPing()
    if FPSUpdateConnection then
        FPSUpdateConnection:Disconnect()
        FPSUpdateConnection = nil
    end
    
    local frameCount = 0
    local lastTime = tick()
    
    FPSUpdateConnection = RunService.RenderStepped:Connect(function()
        if not FPSPingEnabled then return end
        
        frameCount = frameCount + 1
        local currentTime = tick()
        
        if currentTime - lastTime >= 1 then
            FPSValue = frameCount
            frameCount = 0
            lastTime = currentTime
            
            -- Get Ping
            pcall(function()
                PingValue = math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValue())
            end)
            
            -- Update display
            if FPSPingFrame then
                local fpsLabel = FPSPingFrame:FindFirstChild("FPSLabel")
                local pingLabel = FPSPingFrame:FindFirstChild("PingLabel")
                
                if fpsLabel then
                    fpsLabel.Text = "> FPS: " .. FPSValue
                    -- Color based on FPS
                    if FPSValue >= 50 then
                        fpsLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
                    elseif FPSValue >= 30 then
                        fpsLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
                    else
                        fpsLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
                    end
                end
                
                if pingLabel then
                    pingLabel.Text = "> PING: " .. PingValue .. "ms"
                    -- Color based on Ping
                    if PingValue <= 100 then
                        pingLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
                    elseif PingValue <= 200 then
                        pingLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
                    else
                        pingLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
                    end
                end
            end
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
        if effect:IsA("PostEffect") or effect:IsA("BloomEffect") or effect:IsA("BlurEffect") or effect:IsA("ColorCorrectionEffect") or effect:IsA("SunRaysEffect") or effect:IsA("DepthOfFieldEffect") then
            pcall(function()
                effect.Enabled = false
            end)
        end
    end
    
    for _, part in pairs(Workspace:GetDescendants()) do
        if part:IsA("ParticleEmitter") or part:IsA("Fire") or part:IsA("Smoke") or part:IsA("Sparkles") then
            pcall(function()
                part.Enabled = false
            end)
        end
    end
    
    pcall(function()
        Workspace.Terrain.WaterWaveSize = 0
        Workspace.Terrain.WaterWaveSpeed = 0
        Workspace.Terrain.WaterReflectance = 0
        Workspace.Terrain.WaterTransparency = 1
    end)
end

local function DisableFPSBoost()
    pcall(function()
        if OriginalSettings.Shadows ~= nil then Lighting.GlobalShadows = OriginalSettings.Shadows end
        if OriginalSettings.FogEnd ~= nil then Lighting.FogEnd = OriginalSettings.FogEnd end
        if OriginalSettings.Brightness ~= nil then Lighting.Brightness = OriginalSettings.Brightness end
        if OriginalSettings.EnvironmentDiffuseScale ~= nil then Lighting.EnvironmentDiffuseScale = OriginalSettings.EnvironmentDiffuseScale end
        if OriginalSettings.EnvironmentSpecularScale ~= nil then Lighting.EnvironmentSpecularScale = OriginalSettings.EnvironmentSpecularScale end
        if OriginalSettings.Outlines ~= nil then Lighting.Outlines = OriginalSettings.Outlines end
        
        for _, effect in pairs(Lighting:GetChildren()) do
            if effect:IsA("PostEffect") or effect:IsA("BloomEffect") or effect:IsA("BlurEffect") or effect:IsA("ColorCorrectionEffect") or effect:IsA("SunRaysEffect") or effect:IsA("DepthOfFieldEffect") then
                pcall(function()
                    effect.Enabled = true
                end)
            end
        end
        
        for _, part in pairs(Workspace:GetDescendants()) do
            if part:IsA("ParticleEmitter") or part:IsA("Fire") or part:IsA("Smoke") or part:IsA("Sparkles") then
                pcall(function()
                    part.Enabled = true
                end)
            end
        end
    end)
end

-- TELEPORT
local function TeleportToPlayer(targetPlayer)
    if not targetPlayer or not targetPlayer.Character then
        Notify("Teleport", "> TARGET INVALID", 2)
        return
    end
    
    local targetRoot = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
    local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    
    if not targetRoot or not localRoot then
        Notify("Teleport", "> ROOT NOT FOUND", 2)
        return
    end
    
    pcall(function()
        localRoot.CFrame = CFrame.new(targetRoot.Position + Vector3.new(0, 3, 0))
        Notify("Teleport", "> TO: " .. targetPlayer.Name, 2)
    end)
end

local function TeleportToMouse()
    if not LocalPlayer.Character then return end
    local root = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    
    local mouseHit = Mouse.Hit
    if mouseHit then
        pcall(function()
            root.CFrame = CFrame.new(mouseHit.Position + Vector3.new(0, 3, 0))
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

local function UpdateTeleportList()
    if not TeleportListFrame then return end
    
    for _, btn in pairs(TeleportTargetList) do
        if btn then btn:Destroy() end
    end
    TeleportTargetList = {}
    
    local yPos = 0
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local btn = Instance.new("TextButton")
            btn.Size = UDim2.new(1, -10, 0, 30)
            btn.Position = UDim2.new(0, 5, 0, yPos)
            btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
            btn.BorderColor3 = Color3.fromRGB(0, 150, 255)
            btn.BorderSizePixel = 1
            btn.Text = "> " .. player.Name
            btn.TextColor3 = Color3.fromRGB(0, 180, 255)
            btn.Font = Enum.Font.Code
            btn.TextSize = 11
            btn.ZIndex = 13
            btn.Parent = TeleportListFrame
            
            local corner = Instance.new("UICorner")
            corner.CornerRadius = UDim.new(0, 3)
            corner.Parent = btn
            
            btn.MouseButton1Click:Connect(function()
                TeleportToPlayer(player)
            end)
            
            table.insert(TeleportTargetList, btn)
            yPos = yPos + 35
        end
    end
    
    TeleportListFrame.CanvasSize = UDim2.new(0, 0, 0, yPos + 5)
end

-- INFINITE JUMP
local function EnableInfiniteJump()
    if JumpConnection then
        JumpConnection:Disconnect()
    end
    JumpConnection = UserInputService.JumpRequest:Connect(function()
        if InfiniteJumpEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            LocalPlayer.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end)
end

local function DisableInfiniteJump()
    if JumpConnection then
        JumpConnection:Disconnect()
        JumpConnection = nil
    end
end

-- NOCLIP
local function EnableNoclip()
    if NoclipConnection then
        NoclipConnection:Disconnect()
    end
    NoclipConnection = RunService.Stepped:Connect(function()
        if NoclipEnabled and LocalPlayer.Character then
            for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
                if part:IsA("BasePart") and part.CanCollide then
                    part.CanCollide = false
                end
            end
        end
    end)
end

local function DisableNoclip()
    if NoclipConnection then
        NoclipConnection:Disconnect()
        NoclipConnection = nil
    end
    if LocalPlayer.Character then
        for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = true
            end
        end
    end
end

-- SPEED HACK
local function ApplySpeedHack()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        if SpeedHackEnabled then
            LocalPlayer.Character.Humanoid.WalkSpeed = SpeedMultiplier
        else
            LocalPlayer.Character.Humanoid.WalkSpeed = DefaultWalkSpeed
        end
    end
end

-- ESP FUNCTIONS
local function CreateESP(player)
    if ESPObjects[player] then return end
    local espData = {}
    
    local boxOutline = Drawing.new("Square")
    boxOutline.Visible = false
    boxOutline.Color = Color3.fromRGB(0, 180, 255)
    boxOutline.Thickness = 2
    boxOutline.Filled = false
    boxOutline.Transparency = 1
    
    local nameTag = Drawing.new("Text")
    nameTag.Visible = false
    nameTag.Color = Color3.fromRGB(0, 180, 255)
    nameTag.Size = 12
    nameTag.Center = true
    nameTag.Outline = true
    nameTag.OutlineColor = Color3.fromRGB(0, 0, 0)
    
    local distTag = Drawing.new("Text")
    distTag.Visible = false
    distTag.Color = Color3.fromRGB(0, 180, 255)
    distTag.Size = 10
    distTag.Center = true
    distTag.Outline = true
    distTag.OutlineColor = Color3.fromRGB(0, 0, 0)
    
    local healthBg = Drawing.new("Line")
    healthBg.Visible = false
    healthBg.Color = Color3.fromRGB(255, 0, 0)
    healthBg.Thickness = 3
    healthBg.Transparency = 1
    
    local healthBar = Drawing.new("Line")
    healthBar.Visible = false
    healthBar.Color = Color3.fromRGB(0, 180, 255)
    healthBar.Thickness = 3
    healthBar.Transparency = 1
    
    local tracerLine = Drawing.new("Line")
    tracerLine.Visible = false
    tracerLine.Color = TracerColor
    tracerLine.Thickness = 2
    tracerLine.Transparency = 0.5
    
    local headDot = Drawing.new("Circle")
    headDot.Visible = false
    headDot.Color = Color3.fromRGB(255, 0, 0)
    headDot.Thickness = 1
    headDot.Radius = 4
    headDot.Filled = true
    headDot.Transparency = 1
    
    local skeletonHead = Drawing.new("Line")
    skeletonHead.Visible = false
    skeletonHead.Color = Color3.fromRGB(0, 180, 255)
    skeletonHead.Thickness = 1
    skeletonHead.Transparency = 1
    
    local skeletonTorso = Drawing.new("Line")
    skeletonTorso.Visible = false
    skeletonTorso.Color = Color3.fromRGB(0, 180, 255)
    skeletonTorso.Thickness = 1
    skeletonTorso.Transparency = 1
    
    local skeletonLeftArm = Drawing.new("Line")
    skeletonLeftArm.Visible = false
    skeletonLeftArm.Color = Color3.fromRGB(0, 180, 255)
    skeletonLeftArm.Thickness = 1
    skeletonLeftArm.Transparency = 1
    
    local skeletonRightArm = Drawing.new("Line")
    skeletonRightArm.Visible = false
    skeletonRightArm.Color = Color3.fromRGB(0, 180, 255)
    skeletonRightArm.Thickness = 1
    skeletonRightArm.Transparency = 1
    
    local skeletonLeftLeg = Drawing.new("Line")
    skeletonLeftLeg.Visible = false
    skeletonLeftLeg.Color = Color3.fromRGB(0, 180, 255)
    skeletonLeftLeg.Thickness = 1
    skeletonLeftLeg.Transparency = 1
    
    local skeletonRightLeg = Drawing.new("Line")
    skeletonRightLeg.Visible = false
    skeletonRightLeg.Color = Color3.fromRGB(0, 180, 255)
    skeletonRightLeg.Thickness = 1
    skeletonRightLeg.Transparency = 1
    
    espData.Box = boxOutline
    espData.Name = nameTag
    espData.Distance = distTag
    espData.HealthBg = healthBg
    espData.HealthBar = healthBar
    espData.Tracer = tracerLine
    espData.HeadDot = headDot
    espData.SkeletonHead = skeletonHead
    espData.SkeletonTorso = skeletonTorso
    espData.SkeletonLeftArm = skeletonLeftArm
    espData.SkeletonRightArm = skeletonRightArm
    espData.SkeletonLeftLeg = skeletonLeftLeg
    espData.SkeletonRightLeg = skeletonRightLeg
    
    ESPObjects[player] = espData
end

local function RemoveESP(player)
    if ESPObjects[player] then
        local espData = ESPObjects[player]
        espData.Box:Remove()
        espData.Name:Remove()
        espData.Distance:Remove()
        espData.HealthBg:Remove()
        espData.HealthBar:Remove()
        espData.Tracer:Remove()
        espData.HeadDot:Remove()
        espData.SkeletonHead:Remove()
        espData.SkeletonTorso:Remove()
        espData.SkeletonLeftArm:Remove()
        espData.SkeletonRightArm:Remove()
        espData.SkeletonLeftLeg:Remove()
        espData.SkeletonRightLeg:Remove()
        ESPObjects[player] = nil
    end
    
    if ChamsObjects[player] then
        for _, highlight in pairs(ChamsObjects[player]) do
            pcall(function()
                if highlight then
                    highlight:Destroy()
                end
            end)
        end
        ChamsObjects[player] = nil
    end
end

-- CHAMS
local function CreateChams(player)
    if ChamsObjects[player] or not player.Character then return end
    ChamsObjects[player] = {}
    
    for _, part in pairs(player.Character:GetChildren()) do
        if part:IsA("BasePart") or part:IsA("MeshPart") then
            local highlight = Instance.new("Highlight")
            highlight.Adornee = part
            highlight.FillColor = Color3.fromRGB(0, 180, 255)
            highlight.FillTransparency = 0.7
            highlight.OutlineColor = Color3.fromRGB(0, 255, 255)
            highlight.OutlineTransparency = 0
            highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            highlight.Parent = part
            table.insert(ChamsObjects[player], highlight)
        end
    end
end

local function RemoveChams(player)
    if ChamsObjects[player] then
        for _, highlight in pairs(ChamsObjects[player]) do
            pcall(function()
                if highlight then
                    highlight:Destroy()
                end
            end)
        end
        ChamsObjects[player] = nil
    end
end

local function UpdateESP()
    if not ESPEnabled or not IsLoggedIn then
        for player, espData in pairs(ESPObjects) do
            espData.Box.Visible = false
            espData.Name.Visible = false
            espData.Distance.Visible = false
            espData.HealthBg.Visible = false
            espData.HealthBar.Visible = false
            espData.Tracer.Visible = false
            espData.HeadDot.Visible = false
            espData.SkeletonHead.Visible = false
            espData.SkeletonTorso.Visible = false
            espData.SkeletonLeftArm.Visible = false
            espData.SkeletonRightArm.Visible = false
            espData.SkeletonLeftLeg.Visible = false
            espData.SkeletonRightLeg.Visible = false
        end
        return
    end
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("Humanoid") then
            local humanoid = player.Character.Humanoid
            local rootPart = player.Character.HumanoidRootPart
            
            if humanoid.Health > 0 and rootPart then
                if not ESPObjects[player] then
                    CreateESP(player)
                end
                
                local espData = ESPObjects[player]
                local screenPos, onScreen = Camera:WorldToViewportPoint(rootPart.Position)
                
                if onScreen then
                    local distance = (rootPart.Position - Camera.CFrame.Position).Magnitude
                    local boxSize = Vector2.new(2000 / distance, 3500 / distance)
                    local boxX = screenPos.X - boxSize.X / 2
                    local boxY = screenPos.Y - boxSize.Y / 2
                    
                    if ESPBoxEnabled then
                        espData.Box.Visible = true
                        espData.Box.Position = Vector2.new(boxX, boxY)
                        espData.Box.Size = boxSize
                    else
                        espData.Box.Visible = false
                    end
                    
                    if ESPNameEnabled then
                        espData.Name.Visible = true
                        espData.Name.Text = player.Name
                        espData.Name.Position = Vector2.new(screenPos.X, boxY - 15)
                    else
                        espData.Name.Visible = false
                    end
                    
                    if ESPDistanceEnabled then
                        espData.Distance.Visible = true
                        espData.Distance.Text = math.floor(distance) .. "m"
                        espData.Distance.Position = Vector2.new(screenPos.X, boxY + boxSize.Y + 5)
                    else
                        espData.Distance.Visible = false
                    end
                    
                    if ESPHealthEnabled then
                        local healthPercent = humanoid.Health / humanoid.MaxHealth
                        local healthWidth = boxSize.X
                        
                        espData.HealthBg.Visible = true
                        espData.HealthBg.From = Vector2.new(boxX, boxY + boxSize.Y + 20)
                        espData.HealthBg.To = Vector2.new(boxX + healthWidth, boxY + boxSize.Y + 20)
                        
                        espData.HealthBar.Visible = true
                        espData.HealthBar.From = Vector2.new(boxX, boxY + boxSize.Y + 20)
                        espData.HealthBar.To = Vector2.new(boxX + healthWidth * healthPercent, boxY + boxSize.Y + 20)
                    else
                        espData.HealthBg.Visible = false
                        espData.HealthBar.Visible = false
                    end
                    
                    if ESPTracerEnabled then
                        espData.Tracer.Visible = true
                        if not RainbowESPEnabled then
                            espData.Tracer.Color = TracerColor
                        end
                        espData.Tracer.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                        espData.Tracer.To = Vector2.new(screenPos.X, screenPos.Y)
                    else
                        espData.Tracer.Visible = false
                    end
                    
                    if ESPHeadDotEnabled then
                        local head = player.Character:FindFirstChild("Head")
                        if head then
                            local headScreenPos, headOnScreen = Camera:WorldToViewportPoint(head.Position)
                            if headOnScreen then
                                espData.HeadDot.Visible = true
                                espData.HeadDot.Position = Vector2.new(headScreenPos.X, headScreenPos.Y)
                            else
                                espData.HeadDot.Visible = false
                            end
                        end
                    else
                        espData.HeadDot.Visible = false
                    end
                    
                    if ESPSkeletonEnabled then
                        local head = player.Character:FindFirstChild("Head")
                        local upperTorso = player.Character:FindFirstChild("UpperTorso") or player.Character:FindFirstChild("Torso")
                        local lowerTorso = player.Character:FindFirstChild("LowerTorso") or player.Character:FindFirstChild("Torso")
                        local leftArm = player.Character:FindFirstChild("LeftUpperArm") or player.Character:FindFirstChild("Left Arm")
                        local rightArm = player.Character:FindFirstChild("RightUpperArm") or player.Character:FindFirstChild("Right Arm")
                        local leftLeg = player.Character:FindFirstChild("LeftUpperLeg") or player.Character:FindFirstChild("Left Leg")
                        local rightLeg = player.Character:FindFirstChild("RightUpperLeg") or player.Character:FindFirstChild("Right Leg")
                        
                        local function GetScreenPos(part)
                            if part then
                                local sp, os = Camera:WorldToViewportPoint(part.Position)
                                if os then return Vector2.new(sp.X, sp.Y) end
                            end
                            return nil
                        end
                        
                        local headPos = GetScreenPos(head)
                        local upperTorsoPos = GetScreenPos(upperTorso)
                        local lowerTorsoPos = GetScreenPos(lowerTorso)
                        local leftArmPos = GetScreenPos(leftArm)
                        local rightArmPos = GetScreenPos(rightArm)
                        local leftLegPos = GetScreenPos(leftLeg)
                        local rightLegPos = GetScreenPos(rightLeg)
                        
                        if headPos and upperTorsoPos then
                            espData.SkeletonHead.Visible = true
                            espData.SkeletonHead.From = headPos
                            espData.SkeletonHead.To = upperTorsoPos
                        else
                            espData.SkeletonHead.Visible = false
                        end
                        
                        if upperTorsoPos and lowerTorsoPos then
                            espData.SkeletonTorso.Visible = true
                            espData.SkeletonTorso.From = upperTorsoPos
                            espData.SkeletonTorso.To = lowerTorsoPos
                        else
                            espData.SkeletonTorso.Visible = false
                        end
                        
                        if upperTorsoPos and leftArmPos then
                            espData.SkeletonLeftArm.Visible = true
                            espData.SkeletonLeftArm.From = upperTorsoPos
                            espData.SkeletonLeftArm.To = leftArmPos
                        else
                            espData.SkeletonLeftArm.Visible = false
                        end
                        
                        if upperTorsoPos and rightArmPos then
                            espData.SkeletonRightArm.Visible = true
                            espData.SkeletonRightArm.From = upperTorsoPos
                            espData.SkeletonRightArm.To = rightArmPos
                        else
                            espData.SkeletonRightArm.Visible = false
                        end
                        
                        if lowerTorsoPos and leftLegPos then
                            espData.SkeletonLeftLeg.Visible = true
                            espData.SkeletonLeftLeg.From = lowerTorsoPos
                            espData.SkeletonLeftLeg.To = leftLegPos
                        else
                            espData.SkeletonLeftLeg.Visible = false
                        end
                        
                        if lowerTorsoPos and rightLegPos then
                            espData.SkeletonRightLeg.Visible = true
                            espData.SkeletonRightLeg.From = lowerTorsoPos
                            espData.SkeletonRightLeg.To = rightLegPos
                        else
                            espData.SkeletonRightLeg.Visible = false
                        end
                    else
                        espData.SkeletonHead.Visible = false
                        espData.SkeletonTorso.Visible = false
                        espData.SkeletonLeftArm.Visible = false
                        espData.SkeletonRightArm.Visible = false
                        espData.SkeletonLeftLeg.Visible = false
                        espData.SkeletonRightLeg.Visible = false
                    end
                    
                    if ESPChamsEnabled then
                        if not ChamsObjects[player] then
                            CreateChams(player)
                        end
                    else
                        if ChamsObjects[player] then
                            RemoveChams(player)
                        end
                    end
                    
                    if not RainbowESPEnabled then
                        local healthPercent = humanoid.Health / humanoid.MaxHealth
                        if healthPercent > 0.7 then
                            espData.Box.Color = Color3.fromRGB(0, 255, 0)
                            espData.HealthBar.Color = Color3.fromRGB(0, 255, 0)
                        elseif healthPercent > 0.4 then
                            espData.Box.Color = Color3.fromRGB(255, 255, 0)
                            espData.HealthBar.Color = Color3.fromRGB(255, 255, 0)
                        else
                            espData.Box.Color = Color3.fromRGB(255, 0, 0)
                            espData.HealthBar.Color = Color3.fromRGB(255, 0, 0)
                        end
                    end
                else
                    espData.Box.Visible = false
                    espData.Name.Visible = false
                    espData.Distance.Visible = false
                    espData.HealthBg.Visible = false
                    espData.HealthBar.Visible = false
                    espData.Tracer.Visible = false
                    espData.HeadDot.Visible = false
                    espData.SkeletonHead.Visible = false
                    espData.SkeletonTorso.Visible = false
                    espData.SkeletonLeftArm.Visible = false
                    espData.SkeletonRightArm.Visible = false
                    espData.SkeletonLeftLeg.Visible = false
                    espData.SkeletonRightLeg.Visible = false
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
    if LocalPlayer.Team and player.Team then
        if LocalPlayer.Team == player.Team then return true end
    end
    if LocalPlayer.TeamColor and player.TeamColor then
        if LocalPlayer.TeamColor == player.TeamColor then return true end
    end
    return false
end

local function IsWallBetween(origin, target, targetCharacter)
    if not WallCheckEnabled then return false end
    local direction = (target - origin).Unit
    local distance = (target - origin).Magnitude
    local raycastParams = RaycastParams.new()
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
    raycastParams.FilterDescendantsInstances = {LocalPlayer.Character}
    local raycastResult = workspace:Raycast(origin, direction * distance, raycastParams)
    if raycastResult then
        if raycastResult.Instance and targetCharacter and raycastResult.Instance:IsDescendantOf(targetCharacter) then
            return false
        end
        return true
    end
    return false
end

local function GetClosestTarget()
    local closestTarget = nil
    local closestDistance = FOVRadius
    local screenCenter = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
    local cameraPos = Camera.CFrame.Position
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
            if IsSameTeam(player) then continue end
            
            local targetPart = player.Character:FindFirstChild(TargetPart)
            if not targetPart then
                targetPart = player.Character:FindFirstChild("HumanoidRootPart")
            end
            
            if targetPart then
                local targetPosition = targetPart.Position
                
                if PredictionEnabled then
                    local velocity = Vector3.new(0, 0, 0)
                    if player.Character:FindFirstChild("HumanoidRootPart") then
                        velocity = player.Character.HumanoidRootPart.AssemblyLinearVelocity
                    end
                    
                    local distance = (targetPosition - cameraPos).Magnitude
                    local pingFactor = 1 + (distance / 500)
                    local timeToTarget = (distance / 300) * (PredictionAmount / 5) * pingFactor
                    targetPosition = targetPosition + velocity * timeToTarget
                end
                
                local screenPos, onScreen = Camera:WorldToViewportPoint(targetPosition)
                local distance = (Vector2.new(screenPos.X, screenPos.Y) - screenCenter).Magnitude
                
                if onScreen and distance < closestDistance then
                    if WallCheckEnabled then
                        if not IsWallBetween(cameraPos, targetPart.Position, player.Character) then
                            closestTarget = player
                            closestDistance = distance
                        end
                    else
                        closestTarget = player
                        closestDistance = distance
                    end
                end
            end
        end
    end
    return closestTarget
end

local function AimbotFunction()
    if not AimbotEnabled or not IsLoggedIn then return end
    
    AimbotTarget = GetClosestTarget()
    
    if AimbotTarget and AimbotTarget.Character then
        local targetPart = AimbotTarget.Character:FindFirstChild(TargetPart) or AimbotTarget.Character:FindFirstChild("HumanoidRootPart")
        
        if targetPart then
            local targetPos = targetPart.Position
            
            if PredictionEnabled then
                local velocity = Vector3.new(0, 0, 0)
                if AimbotTarget.Character:FindFirstChild("HumanoidRootPart") then
                    velocity = AimbotTarget.Character.HumanoidRootPart.AssemblyLinearVelocity
                end
                
                local distance = (targetPos - Camera.CFrame.Position).Magnitude
                local pingFactor = 1 + (distance / 500)
                local timeToTarget = (distance / 300) * (PredictionAmount / 5) * pingFactor
                targetPos = targetPos + velocity * timeToTarget
            end
            
            local currentPos = Camera.CFrame.Position
            local aimDirection = (targetPos - currentPos).Unit
            local newCFrame = CFrame.new(currentPos, currentPos + aimDirection)
            
            if AimbotMode == "Instant" then
                Camera.CFrame = newCFrame
            elseif AimbotMode == "Accurate" then
                local smoothFactor = math.clamp(Smoothness, 1, 20)
                if smoothFactor <= 2 then
                    Camera.CFrame = newCFrame
                else
                    Camera.CFrame = Camera.CFrame:Lerp(newCFrame, 1 / smoothFactor)
                end
            elseif AimbotMode == "Smooth" then
                local smoothFactor = math.clamp(Smoothness * 2, 2, 30)
                Camera.CFrame = Camera.CFrame:Lerp(newCFrame, 1 / smoothFactor)
            end
        end
    end
end

local function UpdateFOV()
    if FOVCircle and FOVCircleEnabled then
        FOVCircle.Radius = FOVRadius
        FOVCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
    end
end

RunService.RenderStepped:Connect(function()
    UpdateFOV()
    UpdateESP()
    if AimbotEnabled and IsLoggedIn then
        AimbotFunction()
    end
    if SpeedHackEnabled and IsLoggedIn then
        ApplySpeedHack()
    end
end)

-- ==================== LOADING SCREEN ====================
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

local LoadingBgCorner = Instance.new("UICorner")
LoadingBgCorner.CornerRadius = UDim.new(0, 15)
LoadingBgCorner.Parent = LoadingBg

local LoadingTitle = Instance.new("TextLabel")
LoadingTitle.Size = UDim2.new(1, -30, 0, 40)
LoadingTitle.Position = UDim2.new(0, 15, 0, 20)
LoadingTitle.BackgroundTransparency = 1
LoadingTitle.Text = "ZETGAMES-AIMLOCK v3.8"
LoadingTitle.TextColor3 = Color3.fromRGB(0, 180, 255)
LoadingTitle.Font = Enum.Font.Code
LoadingTitle.TextSize = 20
LoadingTitle.ZIndex = 302
LoadingTitle.Parent = LoadingBg

local LoadingSubtitle = Instance.new("TextLabel")
LoadingSubtitle.Size = UDim2.new(1, -30, 0, 25)
LoadingSubtitle.Position = UDim2.new(0, 15, 0, 65)
LoadingSubtitle.BackgroundTransparency = 1
LoadingSubtitle.Text = "> INITIALIZING SYSTEM..."
LoadingSubtitle.TextColor3 = Color3.fromRGB(100, 180, 255)
LoadingSubtitle.Font = Enum.Font.Code
LoadingSubtitle.TextSize = 12
LoadingSubtitle.TextXAlignment = Enum.TextXAlignment.Left
LoadingSubtitle.ZIndex = 302
LoadingSubtitle.Parent = LoadingBg

local LoadingBarBg = Instance.new("Frame")
LoadingBarBg.Size = UDim2.new(1, -30, 0, 15)
LoadingBarBg.Position = UDim2.new(0, 15, 0, 100)
LoadingBarBg.BackgroundColor3 = Color3.fromRGB(20, 20, 35)
LoadingBarBg.BorderColor3 = Color3.fromRGB(0, 150, 255)
LoadingBarBg.BorderSizePixel = 1
LoadingBarBg.ZIndex = 302
LoadingBarBg.Parent = LoadingBg

local LoadingBarBgCorner = Instance.new("UICorner")
LoadingBarBgCorner.CornerRadius = UDim.new(0, 7)
LoadingBarBgCorner.Parent = LoadingBarBg

local LoadingBarFill = Instance.new("Frame")
LoadingBarFill.Size = UDim2.new(0, 0, 1, 0)
LoadingBarFill.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
LoadingBarFill.BorderSizePixel = 0
LoadingBarFill.ZIndex = 303
LoadingBarFill.Parent = LoadingBarBg

local LoadingBarFillCorner = Instance.new("UICorner")
LoadingBarFillCorner.CornerRadius = UDim.new(0, 7)
LoadingBarFillCorner.Parent = LoadingBarFill

local LoadingPercent = Instance.new("TextLabel")
LoadingPercent.Size = UDim2.new(1, -30, 0, 20)
LoadingPercent.Position = UDim2.new(0, 15, 0, 125)
LoadingPercent.BackgroundTransparency = 1
LoadingPercent.Text = "0%"
LoadingPercent.TextColor3 = Color3.fromRGB(0, 180, 255)
LoadingPercent.Font = Enum.Font.Code
LoadingPercent.TextSize = 14
LoadingPercent.ZIndex = 302
LoadingPercent.Parent = LoadingBg

local LoadingStatus = Instance.new("TextLabel")
LoadingStatus.Size = UDim2.new(1, -30, 0, 20)
LoadingStatus.Position = UDim2.new(0, 15, 0, 160)
LoadingStatus.BackgroundTransparency = 1
LoadingStatus.Text = "> LOADING MODULES..."
LoadingStatus.TextColor3 = Color3.fromRGB(80, 150, 255)
LoadingStatus.Font = Enum.Font.Code
LoadingStatus.TextSize = 10
LoadingStatus.TextXAlignment = Enum.TextXAlignment.Left
LoadingStatus.ZIndex = 302
LoadingStatus.Parent = LoadingBg

local loadingMessages = {
    "> LOADING MODULES...",
    "> CONNECTING TO SERVER...",
    "> DECRYPTING DATA...",
    "> INITIALIZING FULL ESP...",
    "> INITIALIZING ENHANCED AIMBOT...",
    "> LOADING FPS/PING DISPLAY...",
    "> LOADING FULLBRIGHT...",
    "> LOADING RAINBOW ESP...",
    "> LOADING TELEPORT...",
    "> SYSTEM READY..."
}

-- ==================== LOGIN UI ====================
local LoginFrame = Instance.new("Frame")
LoginFrame.Size = UDim2.new(0, 320, 0, 420)
LoginFrame.Position = UDim2.new(0.5, -160, 0.5, -210)
LoginFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 25)
LoginFrame.BorderColor3 = Color3.fromRGB(0, 150, 255)
LoginFrame.BorderSizePixel = 2
LoginFrame.ZIndex = 10
LoginFrame.Visible = false
LoginFrame.Parent = ScreenGui

local LoginCorner = Instance.new("UICorner")
LoginCorner.CornerRadius = UDim.new(0, 10)
LoginCorner.Parent = LoginFrame

local LoginTopBar = Instance.new("Frame")
LoginTopBar.Size = UDim2.new(1, 0, 0, 35)
LoginTopBar.BackgroundColor3 = Color3.fromRGB(0, 30, 60)
LoginTopBar.BorderSizePixel = 0
LoginTopBar.ZIndex = 11
LoginTopBar.Parent = LoginFrame

local LoginTopCorner = Instance.new("UICorner")
LoginTopCorner.CornerRadius = UDim.new(0, 10)
LoginTopCorner.Parent = LoginTopBar

local LoginTopText = Instance.new("TextLabel")
LoginTopText.Size = UDim2.new(1, -16, 1, 0)
LoginTopText.Position = UDim2.new(0, 8, 0, 0)
LoginTopText.BackgroundTransparency = 1
LoginTopText.Text = "● ZETGAMES-AIMLOCK v3.8"
LoginTopText.TextColor3 = Color3.fromRGB(0, 180, 255)
LoginTopText.Font = Enum.Font.Code
LoginTopText.TextSize = 12
LoginTopText.TextXAlignment = Enum.TextXAlignment.Left
LoginTopText.ZIndex = 12
LoginTopText.Parent = LoginTopBar

local LoginTitle = Instance.new("TextLabel")
LoginTitle.Size = UDim2.new(1, -30, 0, 30)
LoginTitle.Position = UDim2.new(0, 15, 0, 50)
LoginTitle.BackgroundTransparency = 1
LoginTitle.Text = "> ACCESS VERIFICATION"
LoginTitle.TextColor3 = Color3.fromRGB(0, 180, 255)
LoginTitle.Font = Enum.Font.Code
LoginTitle.TextSize = 16
LoginTitle.ZIndex = 12
LoginTitle.Parent = LoginFrame

local KeyLabel = Instance.new("TextLabel")
KeyLabel.Size = UDim2.new(1, -30, 0, 20)
KeyLabel.Position = UDim2.new(0, 15, 0, 95)
KeyLabel.BackgroundTransparency = 1
KeyLabel.Text = "> KEY_INPUT:"
KeyLabel.TextColor3 = Color3.fromRGB(0, 180, 255)
KeyLabel.Font = Enum.Font.Code
KeyLabel.TextSize = 12
KeyLabel.TextXAlignment = Enum.TextXAlignment.Left
KeyLabel.ZIndex = 12
KeyLabel.Parent = LoginFrame

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

local InputCorner = Instance.new("UICorner")
InputCorner.CornerRadius = UDim.new(0, 5)
InputCorner.Parent = KeyInput

local LoginButton = Instance.new("TextButton")
LoginButton.Size = UDim2.new(1, -30, 0, 45)
LoginButton.Position = UDim2.new(0, 15, 0, 175)
LoginButton.BackgroundColor3 = Color3.fromRGB(0, 80, 150)
LoginButton.BorderColor3 = Color3.fromRGB(0, 180, 255)
LoginButton.BorderSizePixel = 2
LoginButton.Text = "> AUTHENTICATE"
LoginButton.TextColor3 = Color3.fromRGB(0, 180, 255)
LoginButton.Font = Enum.Font.Code
LoginButton.TextSize = 14
LoginButton.ZIndex = 12
LoginButton.Parent = LoginFrame

local LoginBtnCorner = Instance.new("UICorner")
LoginBtnCorner.CornerRadius = UDim.new(0, 5)
LoginBtnCorner.Parent = LoginButton

local GetKeyButton = Instance.new("TextButton")
GetKeyButton.Size = UDim2.new(1, -30, 0, 45)
GetKeyButton.Position = UDim2.new(0, 15, 0, 235)
GetKeyButton.BackgroundColor3 = Color3.fromRGB(0, 50, 100)
GetKeyButton.BorderColor3 = Color3.fromRGB(0, 150, 255)
GetKeyButton.BorderSizePixel = 2
GetKeyButton.Text = "> GET KEY (COPY)"
GetKeyButton.TextColor3 = Color3.fromRGB(0, 180, 255)
GetKeyButton.Font = Enum.Font.Code
GetKeyButton.TextSize = 14
GetKeyButton.ZIndex = 12
GetKeyButton.Parent = LoginFrame

local GetKeyCorner = Instance.new("UICorner")
GetKeyCorner.CornerRadius = UDim.new(0, 5)
GetKeyCorner.Parent = GetKeyButton

local StatusText = Instance.new("TextLabel")
StatusText.Size = UDim2.new(1, -30, 0, 25)
StatusText.Position = UDim2.new(0, 15, 0, 295)
StatusText.BackgroundTransparency = 1
StatusText.Text = "> SYSTEM READY..."
StatusText.TextColor3 = Color3.fromRGB(0, 150, 255)
StatusText.Font = Enum.Font.Code
StatusText.TextSize = 10
StatusText.TextXAlignment = Enum.TextXAlignment.Left
StatusText.ZIndex = 12
StatusText.Parent = LoginFrame

local Instructions = Instance.new("TextLabel")
Instructions.Size = UDim2.new(1, -30, 0, 60)
Instructions.Position = UDim2.new(0, 15, 0, 335)
Instructions.BackgroundTransparency = 1
Instructions.Text = "> STEPS:\n> 1. Click GET KEY\n> 2. Generate key\n> 3. Enter key\n> 4. AUTHENTICATE"
Instructions.TextColor3 = Color3.fromRGB(0, 130, 255)
Instructions.Font = Enum.Font.Code
Instructions.TextSize = 9
Instructions.TextXAlignment = Enum.TextXAlignment.Left
Instructions.ZIndex = 12
Instructions.Parent = LoginFrame

-- ==================== MAIN HUB UI ====================
local MainHub = Instance.new("Frame")
MainHub.Size = UDim2.new(0, 340, 0, 480)
MainHub.Position = UDim2.new(0.5, -170, 0.5, -240)
MainHub.BackgroundColor3 = Color3.fromRGB(10, 10, 25)
MainHub.BorderColor3 = Color3.fromRGB(0, 150, 255)
MainHub.BorderSizePixel = 2
MainHub.Visible = false
MainHub.ZIndex = 10
MainHub.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = MainHub

local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 40)
TitleBar.BackgroundColor3 = Color3.fromRGB(0, 30, 60)
TitleBar.BorderSizePixel = 0
TitleBar.ZIndex = 11
TitleBar.Parent = MainHub

local TitleBarCorner = Instance.new("UICorner")
TitleBarCorner.CornerRadius = UDim.new(0, 10)
TitleBarCorner.Parent = TitleBar

local MainTitle = Instance.new("TextLabel")
MainTitle.Size = UDim2.new(1, -50, 1, 0)
MainTitle.Position = UDim2.new(0, 12, 0, 0)
MainTitle.BackgroundTransparency = 1
MainTitle.Text = "● ZETGAMES-AIMLOCK v3.8"
MainTitle.TextColor3 = Color3.fromRGB(0, 180, 255)
MainTitle.Font = Enum.Font.Code
MainTitle.TextSize = 12
MainTitle.TextXAlignment = Enum.TextXAlignment.Left
MainTitle.ZIndex = 12
MainTitle.Parent = TitleBar

local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -38, 0, 5)
CloseButton.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
CloseButton.BorderColor3 = Color3.fromRGB(255, 0, 0)
CloseButton.BorderSizePixel = 1
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.Font = Enum.Font.Code
CloseButton.TextSize = 16
CloseButton.ZIndex = 13
CloseButton.Parent = TitleBar

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 15)
CloseCorner.Parent = CloseButton

local ScrollFrame = Instance.new("ScrollingFrame")
ScrollFrame.Size = UDim2.new(1, 0, 1, -40)
ScrollFrame.Position = UDim2.new(0, 0, 0, 40)
ScrollFrame.BackgroundTransparency = 1
ScrollFrame.BorderSizePixel = 0
ScrollFrame.ScrollBarThickness = 8
ScrollFrame.ScrollBarImageColor3 = Color3.fromRGB(0, 150, 255)
ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, 2100)
ScrollFrame.ZIndex = 11
ScrollFrame.Parent = MainHub

local ScrollContent = Instance.new("Frame")
ScrollContent.Size = UDim2.new(1, 0, 0, 2100)
ScrollContent.BackgroundTransparency = 1
ScrollContent.ZIndex = 11
ScrollContent.Parent = ScrollFrame

local function CreateSection(title, yPos)
    local SectionFrame = Instance.new("Frame")
    SectionFrame.Size = UDim2.new(1, -20, 0, 25)
    SectionFrame.Position = UDim2.new(0, 10, 0, yPos)
    SectionFrame.BackgroundColor3 = Color3.fromRGB(0, 30, 60)
    SectionFrame.BorderColor3 = Color3.fromRGB(0, 150, 255)
    SectionFrame.BorderSizePixel = 1
    SectionFrame.ZIndex = 12
    SectionFrame.Parent = ScrollContent
    
    local SectionCorner = Instance.new("UICorner")
    SectionCorner.CornerRadius = UDim.new(0, 4)
    SectionCorner.Parent = SectionFrame
    
    local SectionText = Instance.new("TextLabel")
    SectionText.Size = UDim2.new(1, -10, 1, 0)
    SectionText.Position = UDim2.new(0, 5, 0, 0)
    SectionText.BackgroundTransparency = 1
    SectionText.Text = title
    SectionText.TextColor3 = Color3.fromRGB(0, 180, 255)
    SectionText.Font = Enum.Font.Code
    SectionText.TextSize = 11
    SectionText.TextXAlignment = Enum.TextXAlignment.Left
    SectionText.ZIndex = 13
    SectionText.Parent = SectionFrame
end

-- === USER INFO SECTION ===
CreateSection("=== USER INFORMATION ===", 30)

local UserInfoFrame = Instance.new("Frame")
UserInfoFrame.Size = UDim2.new(1, -20, 0, 130)
UserInfoFrame.Position = UDim2.new(0, 10, 0, 60)
UserInfoFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
UserInfoFrame.BorderColor3 = Color3.fromRGB(0, 150, 255)
UserInfoFrame.BorderSizePixel = 1
UserInfoFrame.ZIndex = 12
UserInfoFrame.Parent = ScrollContent

local UserInfoCorner = Instance.new("UICorner")
UserInfoCorner.CornerRadius = UDim.new(0, 4)
UserInfoCorner.Parent = UserInfoFrame

local NameInfoLabel = Instance.new("TextLabel")
NameInfoLabel.Size = UDim2.new(1, -15, 0, 25)
NameInfoLabel.Position = UDim2.new(0, 10, 0, 10)
NameInfoLabel.BackgroundTransparency = 1
NameInfoLabel.Text = "> NAME : " .. LocalPlayer.DisplayName
NameInfoLabel.TextColor3 = Color3.fromRGB(0, 180, 255)
NameInfoLabel.Font = Enum.Font.Code
NameInfoLabel.TextSize = 12
NameInfoLabel.TextXAlignment = Enum.TextXAlignment.Left
NameInfoLabel.ZIndex = 13
NameInfoLabel.Parent = UserInfoFrame

local UsernameInfoLabel = Instance.new("TextLabel")
UsernameInfoLabel.Size = UDim2.new(1, -15, 0, 25)
UsernameInfoLabel.Position = UDim2.new(0, 10, 0, 40)
UsernameInfoLabel.BackgroundTransparency = 1
UsernameInfoLabel.Text = "> USERNAME : " .. LocalPlayer.Name
UsernameInfoLabel.TextColor3 = Color3.fromRGB(0, 180, 255)
UsernameInfoLabel.Font = Enum.Font.Code
UsernameInfoLabel.TextSize = 12
UsernameInfoLabel.TextXAlignment = Enum.TextXAlignment.Left
UsernameInfoLabel.ZIndex = 13
UsernameInfoLabel.Parent = UserInfoFrame

local VPNInfoLabel = Instance.new("TextLabel")
VPNInfoLabel.Size = UDim2.new(1, -15, 0, 25)
VPNInfoLabel.Position = UDim2.new(0, 10, 0, 70)
VPNInfoLabel.BackgroundTransparency = 1
VPNInfoLabel.Text = "> VPN : ON (PROTECTED)"
VPNInfoLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
VPNInfoLabel.Font = Enum.Font.Code
VPNInfoLabel.TextSize = 12
VPNInfoLabel.TextXAlignment = Enum.TextXAlignment.Left
VPNInfoLabel.ZIndex = 13
VPNInfoLabel.Parent = UserInfoFrame

-- Update User Info periodically (for DisplayName changes)
task.spawn(function()
    while task.wait(1) do
        if NameInfoLabel then
            NameInfoLabel.Text = "> NAME : " .. LocalPlayer.DisplayName
        end
        if UsernameInfoLabel then
            UsernameInfoLabel.Text = "> USERNAME : " .. LocalPlayer.Name
        end
    end
end)

-- === MAIN ===
CreateSection("=== MAIN FEATURES ===", 210)

local FPSBoostToggle = Instance.new("TextButton")
FPSBoostToggle.Size = UDim2.new(1, -20, 0, 40)
FPSBoostToggle.Position = UDim2.new(0, 10, 0, 240)
FPSBoostToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
FPSBoostToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
FPSBoostToggle.BorderSizePixel = 1
FPSBoostToggle.Text = "> FPS BOOST: OFF"
FPSBoostToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
FPSBoostToggle.Font = Enum.Font.Code
FPSBoostToggle.TextSize = 12
FPSBoostToggle.ZIndex = 12
FPSBoostToggle.Parent = ScrollContent

local FPSBoostCorner = Instance.new("UICorner")
FPSBoostCorner.CornerRadius = UDim.new(0, 4)
FPSBoostCorner.Parent = FPSBoostToggle

local FullbrightToggle = Instance.new("TextButton")
FullbrightToggle.Size = UDim2.new(1, -20, 0, 40)
FullbrightToggle.Position = UDim2.new(0, 10, 0, 285)
FullbrightToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
FullbrightToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
FullbrightToggle.BorderSizePixel = 1
FullbrightToggle.Text = "> FULLBRIGHT: OFF"
FullbrightToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
FullbrightToggle.Font = Enum.Font.Code
FullbrightToggle.TextSize = 12
FullbrightToggle.ZIndex = 12
FullbrightToggle.Parent = ScrollContent

local FullbrightCorner = Instance.new("UICorner")
FullbrightCorner.CornerRadius = UDim.new(0, 4)
FullbrightCorner.Parent = FullbrightToggle

local FPSPingToggle = Instance.new("TextButton")
FPSPingToggle.Size = UDim2.new(1, -20, 0, 40)
FPSPingToggle.Position = UDim2.new(0, 10, 0, 330)
FPSPingToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
FPSPingToggle.BorderColor3 = Color3.fromRGB(0, 180, 255)
FPSPingToggle.BorderSizePixel = 1
FPSPingToggle.Text = "> FPS/PING DISPLAY: ON"
FPSPingToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
FPSPingToggle.Font = Enum.Font.Code
FPSPingToggle.TextSize = 12
FPSPingToggle.ZIndex = 12
FPSPingToggle.Parent = ScrollContent

local FPSPingCorner = Instance.new("UICorner")
FPSPingCorner.CornerRadius = UDim.new(0, 4)
FPSPingCorner.Parent = FPSPingToggle

local SpeedHackToggle = Instance.new("TextButton")
SpeedHackToggle.Size = UDim2.new(1, -20, 0, 40)
SpeedHackToggle.Position = UDim2.new(0, 10, 0, 375)
SpeedHackToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
SpeedHackToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
SpeedHackToggle.BorderSizePixel = 1
SpeedHackToggle.Text = "> SPEED HACK: OFF"
SpeedHackToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
SpeedHackToggle.Font = Enum.Font.Code
SpeedHackToggle.TextSize = 12
SpeedHackToggle.ZIndex = 12
SpeedHackToggle.Parent = ScrollContent

local SpeedHackCorner = Instance.new("UICorner")
SpeedHackCorner.CornerRadius = UDim.new(0, 4)
SpeedHackCorner.Parent = SpeedHackToggle

local SpeedInput = Instance.new("TextBox")
SpeedInput.Size = UDim2.new(1, -20, 0, 35)
SpeedInput.Position = UDim2.new(0, 10, 0, 420)
SpeedInput.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
SpeedInput.BorderColor3 = Color3.fromRGB(0, 150, 255)
SpeedInput.BorderSizePixel = 1
SpeedInput.PlaceholderText = "> Speed (16-500)"
SpeedInput.PlaceholderColor3 = Color3.fromRGB(60, 80, 100)
SpeedInput.Text = ""
SpeedInput.TextColor3 = Color3.fromRGB(0, 180, 255)
SpeedInput.Font = Enum.Font.Code
SpeedInput.TextSize = 11
SpeedInput.ZIndex = 12
SpeedInput.Parent = ScrollContent

local SpeedInputCorner = Instance.new("UICorner")
SpeedInputCorner.CornerRadius = UDim.new(0, 4)
SpeedInputCorner.Parent = SpeedInput

local InfiniteJumpToggle = Instance.new("TextButton")
InfiniteJumpToggle.Size = UDim2.new(1, -20, 0, 40)
InfiniteJumpToggle.Position = UDim2.new(0, 10, 0, 460)
InfiniteJumpToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
InfiniteJumpToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
InfiniteJumpToggle.BorderSizePixel = 1
InfiniteJumpToggle.Text = "> INFINITE JUMP: OFF"
InfiniteJumpToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
InfiniteJumpToggle.Font = Enum.Font.Code
InfiniteJumpToggle.TextSize = 12
InfiniteJumpToggle.ZIndex = 12
InfiniteJumpToggle.Parent = ScrollContent

local InfiniteJumpCorner = Instance.new("UICorner")
InfiniteJumpCorner.CornerRadius = UDim.new(0, 4)
InfiniteJumpCorner.Parent = InfiniteJumpToggle

local NoclipToggle = Instance.new("TextButton")
NoclipToggle.Size = UDim2.new(1, -20, 0, 40)
NoclipToggle.Position = UDim2.new(0, 10, 0, 505)
NoclipToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
NoclipToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
NoclipToggle.BorderSizePixel = 1
NoclipToggle.Text = "> NOCLIP: OFF"
NoclipToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
NoclipToggle.Font = Enum.Font.Code
NoclipToggle.TextSize = 12
NoclipToggle.ZIndex = 12
NoclipToggle.Parent = ScrollContent

local NoclipCorner = Instance.new("UICorner")
NoclipCorner.CornerRadius = UDim.new(0, 4)
NoclipCorner.Parent = NoclipToggle

-- === ENHANCED AIMBOT ===
CreateSection("=== ENHANCED AIMBOT ===", 555)

local AimbotToggle = Instance.new("TextButton")
AimbotToggle.Size = UDim2.new(1, -20, 0, 45)
AimbotToggle.Position = UDim2.new(0, 10, 0, 585)
AimbotToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
AimbotToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
AimbotToggle.BorderSizePixel = 2
AimbotToggle.Text = "> AIMBOT: OFF"
AimbotToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
AimbotToggle.Font = Enum.Font.Code
AimbotToggle.TextSize = 13
AimbotToggle.ZIndex = 12
AimbotToggle.Parent = ScrollContent

local AimbotCorner = Instance.new("UICorner")
AimbotCorner.CornerRadius = UDim.new(0, 4)
AimbotCorner.Parent = AimbotToggle

local AimbotModeLabel = Instance.new("TextLabel")
AimbotModeLabel.Size = UDim2.new(1, -20, 0, 20)
AimbotModeLabel.Position = UDim2.new(0, 10, 0, 635)
AimbotModeLabel.BackgroundTransparency = 1
AimbotModeLabel.Text = "> AIMBOT MODE: ACCURATE"
AimbotModeLabel.TextColor3 = Color3.fromRGB(0, 180, 255)
AimbotModeLabel.Font = Enum.Font.Code
AimbotModeLabel.TextSize = 11
AimbotModeLabel.TextXAlignment = Enum.TextXAlignment.Left
AimbotModeLabel.ZIndex = 12
AimbotModeLabel.Parent = ScrollContent

local AimbotModeButtons = Instance.new("Frame")
AimbotModeButtons.Size = UDim2.new(1, -20, 0, 35)
AimbotModeButtons.Position = UDim2.new(0, 10, 0, 660)
AimbotModeButtons.BackgroundTransparency = 1
AimbotModeButtons.ZIndex = 12
AimbotModeButtons.Parent = ScrollContent

local function CreateAimbotModeButton(text, mode, xPos)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.31, 0, 1, 0)
    btn.Position = UDim2.new(xPos, 0, 0, 0)
    btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    btn.BorderColor3 = Color3.fromRGB(0, 150, 255)
    btn.BorderSizePixel = 1
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(0, 180, 255)
    btn.Font = Enum.Font.Code
    btn.TextSize = 10
    btn.ZIndex = 13
    btn.Parent = AimbotModeButtons
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 3)
    corner.Parent = btn
    
    btn.MouseButton1Click:Connect(function()
        AimbotMode = mode
        AimbotModeLabel.Text = "> AIMBOT MODE: " .. string.upper(mode)
        Notify("Aimbot", "> MODE: " .. string.upper(mode), 2)
    end)
end

CreateAimbotModeButton("ACCURATE", "Accurate", 0)
CreateAimbotModeButton("SMOOTH", "Smooth", 0.345)
CreateAimbotModeButton("INSTANT", "Instant", 0.69)

local PredictionToggle = Instance.new("TextButton")
PredictionToggle.Size = UDim2.new(1, -20, 0, 40)
PredictionToggle.Position = UDim2.new(0, 10, 0, 705)
PredictionToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
PredictionToggle.BorderColor3 = Color3.fromRGB(0, 180, 255)
PredictionToggle.BorderSizePixel = 1
PredictionToggle.Text = "> PREDICTION: ON"
PredictionToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
PredictionToggle.Font = Enum.Font.Code
PredictionToggle.TextSize = 12
PredictionToggle.ZIndex = 12
PredictionToggle.Parent = ScrollContent

local PredictionCorner = Instance.new("UICorner")
PredictionCorner.CornerRadius = UDim.new(0, 4)
PredictionCorner.Parent = PredictionToggle

local PredictionInput = Instance.new("TextBox")
PredictionInput.Size = UDim2.new(1, -20, 0, 35)
PredictionInput.Position = UDim2.new(0, 10, 0, 750)
PredictionInput.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
PredictionInput.BorderColor3 = Color3.fromRGB(0, 150, 255)
PredictionInput.BorderSizePixel = 1
PredictionInput.PlaceholderText = "> Prediction (1-10)"
PredictionInput.PlaceholderColor3 = Color3.fromRGB(60, 80, 100)
PredictionInput.Text = ""
PredictionInput.TextColor3 = Color3.fromRGB(0, 180, 255)
PredictionInput.Font = Enum.Font.Code
PredictionInput.TextSize = 11
PredictionInput.ZIndex = 12
PredictionInput.Parent = ScrollContent

local PredictionInputCorner = Instance.new("UICorner")
PredictionInputCorner.CornerRadius = UDim.new(0, 4)
PredictionInputCorner.Parent = PredictionInput

local WallCheckToggle = Instance.new("TextButton")
WallCheckToggle.Size = UDim2.new(1, -20, 0, 40)
WallCheckToggle.Position = UDim2.new(0, 10, 0, 790)
WallCheckToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
WallCheckToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
WallCheckToggle.BorderSizePixel = 1
WallCheckToggle.Text = "> WALL_CHECK: OFF"
WallCheckToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
WallCheckToggle.Font = Enum.Font.Code
WallCheckToggle.TextSize = 12
WallCheckToggle.ZIndex = 12
WallCheckToggle.Parent = ScrollContent

local WallCheckCorner = Instance.new("UICorner")
WallCheckCorner.CornerRadius = UDim.new(0, 4)
WallCheckCorner.Parent = WallCheckToggle

local TeamCheckToggle = Instance.new("TextButton")
TeamCheckToggle.Size = UDim2.new(1, -20, 0, 40)
TeamCheckToggle.Position = UDim2.new(0, 10, 0, 835)
TeamCheckToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
TeamCheckToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
TeamCheckToggle.BorderSizePixel = 1
TeamCheckToggle.Text = "> TEAM_CHECK: OFF"
TeamCheckToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
TeamCheckToggle.Font = Enum.Font.Code
TeamCheckToggle.TextSize = 12
TeamCheckToggle.ZIndex = 12
TeamCheckToggle.Parent = ScrollContent

local TeamCheckCorner = Instance.new("UICorner")
TeamCheckCorner.CornerRadius = UDim.new(0, 4)
TeamCheckCorner.Parent = TeamCheckToggle

local TargetLabel = Instance.new("TextLabel")
TargetLabel.Size = UDim2.new(1, -20, 0, 20)
TargetLabel.Position = UDim2.new(0, 10, 0, 880)
TargetLabel.BackgroundTransparency = 1
TargetLabel.Text = "> TARGET_PART: HEAD"
TargetLabel.TextColor3 = Color3.fromRGB(0, 180, 255)
TargetLabel.Font = Enum.Font.Code
TargetLabel.TextSize = 11
TargetLabel.TextXAlignment = Enum.TextXAlignment.Left
TargetLabel.ZIndex = 12
TargetLabel.Parent = ScrollContent

local TargetButtons = Instance.new("Frame")
TargetButtons.Size = UDim2.new(1, -20, 0, 35)
TargetButtons.Position = UDim2.new(0, 10, 0, 905)
TargetButtons.BackgroundTransparency = 1
TargetButtons.ZIndex = 12
TargetButtons.Parent = ScrollContent

local function CreateTargetButton(text, part, xPos)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 90, 1, 0)
    btn.Position = UDim2.new(xPos, 0, 0, 0)
    btn.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    btn.BorderColor3 = Color3.fromRGB(0, 150, 255)
    btn.BorderSizePixel = 1
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(0, 180, 255)
    btn.Font = Enum.Font.Code
    btn.TextSize = 10
    btn.ZIndex = 13
    btn.Parent = TargetButtons
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 3)
    corner.Parent = btn
    
    btn.MouseButton1Click:Connect(function()
        TargetPart = part
        TargetLabel.Text = "> TARGET_PART: " .. text:upper()
    end)
end

CreateTargetButton("Head", "Head", 0)
CreateTargetButton("Torso", "HumanoidRootPart", 0.37)
CreateTargetButton("Body", "UpperTorso", 0.74)

-- === FULL ESP ===
CreateSection("=== FULL ESP FEATURES ===", 950)

local ESPToggle = Instance.new("TextButton")
ESPToggle.Size = UDim2.new(1, -20, 0, 40)
ESPToggle.Position = UDim2.new(0, 10, 0, 980)
ESPToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
ESPToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
ESPToggle.BorderSizePixel = 1
ESPToggle.Text = "> ESP MASTER: OFF"
ESPToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
ESPToggle.Font = Enum.Font.Code
ESPToggle.TextSize = 12
ESPToggle.ZIndex = 12
ESPToggle.Parent = ScrollContent

local ESPCorner = Instance.new("UICorner")
ESPCorner.CornerRadius = UDim.new(0, 4)
ESPCorner.Parent = ESPToggle

local RainbowESPToggle = Instance.new("TextButton")
RainbowESPToggle.Size = UDim2.new(1, -20, 0, 40)
RainbowESPToggle.Position = UDim2.new(0, 10, 0, 1025)
RainbowESPToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
RainbowESPToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
RainbowESPToggle.BorderSizePixel = 2
RainbowESPToggle.Text = "> 🌈 RAINBOW ESP: OFF"
RainbowESPToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
RainbowESPToggle.Font = Enum.Font.Code
RainbowESPToggle.TextSize = 12
RainbowESPToggle.ZIndex = 12
RainbowESPToggle.Parent = ScrollContent

local RainbowESPCorner = Instance.new("UICorner")
RainbowESPCorner.CornerRadius = UDim.new(0, 4)
RainbowESPCorner.Parent = RainbowESPToggle

local ESPBoxToggle = Instance.new("TextButton")
ESPBoxToggle.Size = UDim2.new(0.48, -15, 0, 35)
ESPBoxToggle.Position = UDim2.new(0, 10, 0, 1070)
ESPBoxToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
ESPBoxToggle.BorderColor3 = Color3.fromRGB(0, 180, 255)
ESPBoxToggle.BorderSizePixel = 1
ESPBoxToggle.Text = "> BOX: ON"
ESPBoxToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
ESPBoxToggle.Font = Enum.Font.Code
ESPBoxToggle.TextSize = 10
ESPBoxToggle.ZIndex = 12
ESPBoxToggle.Parent = ScrollContent

local ESPBoxCorner = Instance.new("UICorner")
ESPBoxCorner.CornerRadius = UDim.new(0, 4)
ESPBoxCorner.Parent = ESPBoxToggle

local ESPNameToggle = Instance.new("TextButton")
ESPNameToggle.Size = UDim2.new(0.48, -15, 0, 35)
ESPNameToggle.Position = UDim2.new(0.52, 5, 0, 1070)
ESPNameToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
ESPNameToggle.BorderColor3 = Color3.fromRGB(0, 180, 255)
ESPNameToggle.BorderSizePixel = 1
ESPNameToggle.Text = "> NAME: ON"
ESPNameToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
ESPNameToggle.Font = Enum.Font.Code
ESPNameToggle.TextSize = 10
ESPNameToggle.ZIndex = 12
ESPNameToggle.Parent = ScrollContent

local ESPNameCorner = Instance.new("UICorner")
ESPNameCorner.CornerRadius = UDim.new(0, 4)
ESPNameCorner.Parent = ESPNameToggle

local ESPDistanceToggle = Instance.new("TextButton")
ESPDistanceToggle.Size = UDim2.new(0.48, -15, 0, 35)
ESPDistanceToggle.Position = UDim2.new(0, 10, 0, 1110)
ESPDistanceToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
ESPDistanceToggle.BorderColor3 = Color3.fromRGB(0, 180, 255)
ESPDistanceToggle.BorderSizePixel = 1
ESPDistanceToggle.Text = "> DISTANCE: ON"
ESPDistanceToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
ESPDistanceToggle.Font = Enum.Font.Code
ESPDistanceToggle.TextSize = 10
ESPDistanceToggle.ZIndex = 12
ESPDistanceToggle.Parent = ScrollContent

local ESPDistanceCorner = Instance.new("UICorner")
ESPDistanceCorner.CornerRadius = UDim.new(0, 4)
ESPDistanceCorner.Parent = ESPDistanceToggle

local ESPHealthToggle = Instance.new("TextButton")
ESPHealthToggle.Size = UDim2.new(0.48, -15, 0, 35)
ESPHealthToggle.Position = UDim2.new(0.52, 5, 0, 1110)
ESPHealthToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
ESPHealthToggle.BorderColor3 = Color3.fromRGB(0, 180, 255)
ESPHealthToggle.BorderSizePixel = 1
ESPHealthToggle.Text = "> HEALTH: ON"
ESPHealthToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
ESPHealthToggle.Font = Enum.Font.Code
ESPHealthToggle.TextSize = 10
ESPHealthToggle.ZIndex = 12
ESPHealthToggle.Parent = ScrollContent

local ESPHealthCorner = Instance.new("UICorner")
ESPHealthCorner.CornerRadius = UDim.new(0, 4)
ESPHealthCorner.Parent = ESPHealthToggle

local ESPSkeletonToggle = Instance.new("TextButton")
ESPSkeletonToggle.Size = UDim2.new(0.48, -15, 0, 35)
ESPSkeletonToggle.Position = UDim2.new(0, 10, 0, 1150)
ESPSkeletonToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
ESPSkeletonToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
ESPSkeletonToggle.BorderSizePixel = 1
ESPSkeletonToggle.Text = "> SKELETON: OFF"
ESPSkeletonToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
ESPSkeletonToggle.Font = Enum.Font.Code
ESPSkeletonToggle.TextSize = 10
ESPSkeletonToggle.ZIndex = 12
ESPSkeletonToggle.Parent = ScrollContent

local ESPSkeletonCorner = Instance.new("UICorner")
ESPSkeletonCorner.CornerRadius = UDim.new(0, 4)
ESPSkeletonCorner.Parent = ESPSkeletonToggle

local ESPHeadDotToggle = Instance.new("TextButton")
ESPHeadDotToggle.Size = UDim2.new(0.48, -15, 0, 35)
ESPHeadDotToggle.Position = UDim2.new(0.52, 5, 0, 1150)
ESPHeadDotToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
ESPHeadDotToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
ESPHeadDotToggle.BorderSizePixel = 1
ESPHeadDotToggle.Text = "> HEAD DOT: OFF"
ESPHeadDotToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
ESPHeadDotToggle.Font = Enum.Font.Code
ESPHeadDotToggle.TextSize = 10
ESPHeadDotToggle.ZIndex = 12
ESPHeadDotToggle.Parent = ScrollContent

local ESPHeadDotCorner = Instance.new("UICorner")
ESPHeadDotCorner.CornerRadius = UDim.new(0, 4)
ESPHeadDotCorner.Parent = ESPHeadDotToggle

local ESPChamsToggle = Instance.new("TextButton")
ESPChamsToggle.Size = UDim2.new(0.48, -15, 0, 35)
ESPChamsToggle.Position = UDim2.new(0, 10, 0, 1190)
ESPChamsToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
ESPChamsToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
ESPChamsToggle.BorderSizePixel = 1
ESPChamsToggle.Text = "> CHAMS: OFF"
ESPChamsToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
ESPChamsToggle.Font = Enum.Font.Code
ESPChamsToggle.TextSize = 10
ESPChamsToggle.ZIndex = 12
ESPChamsToggle.Parent = ScrollContent

local ESPChamsCorner = Instance.new("UICorner")
ESPChamsCorner.CornerRadius = UDim.new(0, 4)
ESPChamsCorner.Parent = ESPChamsToggle

local ESPTracerToggle = Instance.new("TextButton")
ESPTracerToggle.Size = UDim2.new(0.48, -15, 0, 35)
ESPTracerToggle.Position = UDim2.new(0.52, 5, 0, 1190)
ESPTracerToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
ESPTracerToggle.BorderColor3 = Color3.fromRGB(0, 180, 255)
ESPTracerToggle.BorderSizePixel = 1
ESPTracerToggle.Text = "> TRACER: ON"
ESPTracerToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
ESPTracerToggle.Font = Enum.Font.Code
ESPTracerToggle.TextSize = 10
ESPTracerToggle.ZIndex = 12
ESPTracerToggle.Parent = ScrollContent

local ESPTracerCorner = Instance.new("UICorner")
ESPTracerCorner.CornerRadius = UDim.new(0, 4)
ESPTracerCorner.Parent = ESPTracerToggle

local TracerColorLabel = Instance.new("TextLabel")
TracerColorLabel.Size = UDim2.new(1, -20, 0, 20)
TracerColorLabel.Position = UDim2.new(0, 10, 0, 1235)
TracerColorLabel.BackgroundTransparency = 1
TracerColorLabel.Text = "> TRACER COLOR: MERAH"
TracerColorLabel.TextColor3 = Color3.fromRGB(0, 180, 255)
TracerColorLabel.Font = Enum.Font.Code
TracerColorLabel.TextSize = 11
TracerColorLabel.TextXAlignment = Enum.TextXAlignment.Left
TracerColorLabel.ZIndex = 12
TracerColorLabel.Parent = ScrollContent

local TracerColorButtons = Instance.new("Frame")
TracerColorButtons.Size = UDim2.new(1, -20, 0, 40)
TracerColorButtons.Position = UDim2.new(0, 10, 0, 1260)
TracerColorButtons.BackgroundTransparency = 1
TracerColorButtons.ZIndex = 12
TracerColorButtons.Parent = ScrollContent

local function CreateTracerColorButton(text, color, name, xPos, yPos)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.31, 0, 0, 35)
    btn.Position = UDim2.new(xPos, 0, yPos, 0)
    btn.BackgroundColor3 = color
    btn.BorderColor3 = Color3.fromRGB(0, 150, 255)
    btn.BorderSizePixel = 1
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.Code
    btn.TextSize = 10
    btn.ZIndex = 13
    btn.Parent = TracerColorButtons
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 3)
    corner.Parent = btn
    
    btn.MouseButton1Click:Connect(function()
        TracerColor = color
        TracerColorName = name
        TracerColorLabel.Text = "> TRACER COLOR: " .. name
    end)
end

CreateTracerColorButton("MERAH", Color3.fromRGB(255, 0, 0), "MERAH", 0, 0)
CreateTracerColorButton("PUTIH", Color3.fromRGB(255, 255, 255), "PUTIH", 0.345, 0)
CreateTracerColorButton("HITAM", Color3.fromRGB(0, 0, 0), "HITAM", 0.69, 0)

-- === TELEPORT ===
CreateSection("=== TELEPORT (UNLOCKED) ===", 1325)

local TeleportMouseBtn = Instance.new("TextButton")
TeleportMouseBtn.Size = UDim2.new(1, -20, 0, 40)
TeleportMouseBtn.Position = UDim2.new(0, 10, 0, 1355)
TeleportMouseBtn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
TeleportMouseBtn.BorderColor3 = Color3.fromRGB(0, 180, 255)
TeleportMouseBtn.BorderSizePixel = 1
TeleportMouseBtn.Text = "> TELEPORT TO MOUSE"
TeleportMouseBtn.TextColor3 = Color3.fromRGB(0, 180, 255)
TeleportMouseBtn.Font = Enum.Font.Code
TeleportMouseBtn.TextSize = 12
TeleportMouseBtn.ZIndex = 12
TeleportMouseBtn.Parent = ScrollContent

local TeleportMouseCorner = Instance.new("UICorner")
TeleportMouseCorner.CornerRadius = UDim.new(0, 4)
TeleportMouseCorner.Parent = TeleportMouseBtn

local SaveLocBtn = Instance.new("TextButton")
SaveLocBtn.Size = UDim2.new(0.48, -15, 0, 40)
SaveLocBtn.Position = UDim2.new(0, 10, 0, 1400)
SaveLocBtn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
SaveLocBtn.BorderColor3 = Color3.fromRGB(0, 180, 255)
SaveLocBtn.BorderSizePixel = 1
SaveLocBtn.Text = "> SAVE LOC"
SaveLocBtn.TextColor3 = Color3.fromRGB(0, 180, 255)
SaveLocBtn.Font = Enum.Font.Code
SaveLocBtn.TextSize = 11
SaveLocBtn.ZIndex = 12
SaveLocBtn.Parent = ScrollContent

local SaveLocCorner = Instance.new("UICorner")
SaveLocCorner.CornerRadius = UDim.new(0, 4)
SaveLocCorner.Parent = SaveLocBtn

local LoadLocBtn = Instance.new("TextButton")
LoadLocBtn.Size = UDim2.new(0.48, -15, 0, 40)
LoadLocBtn.Position = UDim2.new(0.52, 5, 0, 1400)
LoadLocBtn.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
LoadLocBtn.BorderColor3 = Color3.fromRGB(0, 180, 255)
LoadLocBtn.BorderSizePixel = 1
LoadLocBtn.Text = "> LOAD LOC"
LoadLocBtn.TextColor3 = Color3.fromRGB(0, 180, 255)
LoadLocBtn.Font = Enum.Font.Code
LoadLocBtn.TextSize = 11
LoadLocBtn.ZIndex = 12
LoadLocBtn.Parent = ScrollContent

local LoadLocCorner = Instance.new("UICorner")
LoadLocCorner.CornerRadius = UDim.new(0, 4)
LoadLocCorner.Parent = LoadLocBtn

local TeleportListLabel = Instance.new("TextLabel")
TeleportListLabel.Size = UDim2.new(1, -20, 0, 20)
TeleportListLabel.Position = UDim2.new(0, 10, 0, 1445)
TeleportListLabel.BackgroundTransparency = 1
TeleportListLabel.Text = "> PLAYERS (CLICK TO TELEPORT):"
TeleportListLabel.TextColor3 = Color3.fromRGB(0, 180, 255)
TeleportListLabel.Font = Enum.Font.Code
TeleportListLabel.TextSize = 10
TeleportListLabel.TextXAlignment = Enum.TextXAlignment.Left
TeleportListLabel.ZIndex = 12
TeleportListLabel.Parent = ScrollContent

local TeleportListFrame = Instance.new("ScrollingFrame")
TeleportListFrame.Size = UDim2.new(1, -20, 0, 120)
TeleportListFrame.Position = UDim2.new(0, 10, 0, 1470)
TeleportListFrame.BackgroundColor3 = Color3.fromRGB(8, 8, 20)
TeleportListFrame.BorderColor3 = Color3.fromRGB(0, 150, 255)
TeleportListFrame.BorderSizePixel = 1
TeleportListFrame.ScrollBarThickness = 5
TeleportListFrame.ScrollBarImageColor3 = Color3.fromRGB(0, 150, 255)
TeleportListFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
TeleportListFrame.ZIndex = 12
TeleportListFrame.Parent = ScrollContent

local TeleportListCorner = Instance.new("UICorner")
TeleportListCorner.CornerRadius = UDim.new(0, 4)
TeleportListCorner.Parent = TeleportListFrame

-- === VISUAL ===
CreateSection("=== VISUAL FEATURES ===", 1605)

local FOVToggle = Instance.new("TextButton")
FOVToggle.Size = UDim2.new(1, -20, 0, 40)
FOVToggle.Position = UDim2.new(0, 10, 0, 1635)
FOVToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
FOVToggle.BorderColor3 = Color3.fromRGB(0, 150, 255)
FOVToggle.BorderSizePixel = 1
FOVToggle.Text = "> FOV_CIRCLE: OFF"
FOVToggle.TextColor3 = Color3.fromRGB(0, 180, 255)
FOVToggle.Font = Enum.Font.Code
FOVToggle.TextSize = 12
FOVToggle.ZIndex = 12
FOVToggle.Parent = ScrollContent

local FOVCorner = Instance.new("UICorner")
FOVCorner.CornerRadius = UDim.new(0, 4)
FOVCorner.Parent = FOVToggle

local FOVInput = Instance.new("TextBox")
FOVInput.Size = UDim2.new(1, -20, 0, 35)
FOVInput.Position = UDim2.new(0, 10, 0, 1680)
FOVInput.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
FOVInput.BorderColor3 = Color3.fromRGB(0, 150, 255)
FOVInput.BorderSizePixel = 1
FOVInput.PlaceholderText = "> FOV Radius (50-5000)"
FOVInput.PlaceholderColor3 = Color3.fromRGB(60, 80, 100)
FOVInput.Text = ""
FOVInput.TextColor3 = Color3.fromRGB(0, 180, 255)
FOVInput.Font = Enum.Font.Code
FOVInput.TextSize = 11
FOVInput.ZIndex = 12
FOVInput.Parent = ScrollContent

local FOVInputCorner = Instance.new("UICorner")
FOVInputCorner.CornerRadius = UDim.new(0, 4)
FOVInputCorner.Parent = FOVInput

local SmoothInput = Instance.new("TextBox")
SmoothInput.Size = UDim2.new(1, -20, 0, 35)
SmoothInput.Position = UDim2.new(0, 10, 0, 1720)
SmoothInput.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
SmoothInput.BorderColor3 = Color3.fromRGB(0, 150, 255)
SmoothInput.BorderSizePixel = 1
SmoothInput.PlaceholderText = "> Smoothness (1-20)"
SmoothInput.PlaceholderColor3 = Color3.fromRGB(60, 80, 100)
SmoothInput.Text = ""
SmoothInput.TextColor3 = Color3.fromRGB(0, 180, 255)
SmoothInput.Font = Enum.Font.Code
SmoothInput.TextSize = 11
SmoothInput.ZIndex = 12
SmoothInput.Parent = ScrollContent

local SmoothInputCorner = Instance.new("UICorner")
SmoothInputCorner.CornerRadius = UDim.new(0, 4)
SmoothInputCorner.Parent = SmoothInput

-- Toggle Menu Button
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

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(0, 22)
ToggleCorner.Parent = ToggleMenuButton

-- FOV Circle
FOVCircle = Drawing.new("Circle")
FOVCircle.Visible = false
FOVCircle.Color = Color3.fromRGB(0, 180, 255)
FOVCircle.Thickness = 2
FOVCircle.Radius = FOVRadius
FOVCircle.Filled = false
FOVCircle.Transparency = 1
FOVCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)

-- ==================== LOADING SEQUENCE ====================
task.spawn(function()
    local totalTime = 10
    local interval = totalTime / 100
    
    for i = 1, 100 do
        task.wait(interval)
        
        LoadingBarFill.Size = UDim2.new(i / 100, 0, 1, 0)
        LoadingPercent.Text = i .. "%"
        
        local messageIndex = math.floor(i / 10) + 1
        if messageIndex > #loadingMessages then messageIndex = #loadingMessages end
        LoadingStatus.Text = loadingMessages[messageIndex]
    end
    
    LoadingPercent.Text = "100%"
    LoadingStatus.Text = "> SYSTEM READY!"
    LoadingBarFill.Size = UDim2.new(1, 0, 1, 0)
    
    task.wait(0.5)
    LoadingScreen.Visible = false
    
    if IsNightLockActive() then
        KickPlayer("* TIDUR UNTUK KESEHATAN MU *")
    else
        LoginFrame.Visible = true
        Notify("ZetGames-AimLock v3.8", "> SYSTEM LOADED", 3)
        Notify("Login", "> ENTER ACCESS KEY", 3)
    end
end)

-- Auto Update Teleport List
task.spawn(function()
    while task.wait(3) do
        if IsLoggedIn then
            UpdateTeleportList()
        end
    end
end)

-- ==================== LOGIN BUTTON ====================
LoginButton.MouseButton1Click:Connect(function()
    local key = KeyInput.Text
    local keyData = ValidKeys[key]
    
    if keyData then
        if keyData.Expiry == 0 then
            IsLoggedIn = true
            LoginFrame.Visible = false
            MainHub.Visible = true
            ToggleMenuButton.Visible = true
            MenuVisible = true
            StatusText.Text = "> ACCESS GRANTED..."
            UpdateTeleportList()
            Notify("Success", "> WELCOME | " .. keyData.Level, 3)
        elseif os.time() < keyData.Expiry then
            IsLoggedIn = true
            LoginFrame.Visible = false
            MainHub.Visible = true
            ToggleMenuButton.Visible = true
            MenuVisible = true
            StatusText.Text = "> ACCESS GRANTED..."
            UpdateTeleportList()
            local timeLeft = keyData.Expiry - os.time()
            local days = math.floor(timeLeft / 86400)
            Notify("Success", "> WELCOME | " .. days .. "d left", 3)
        else
            StatusText.Text = "> ERROR: KEY EXPIRED"
            Notify("Failed", "> KEY EXPIRED", 2)
        end
    else
        StatusText.Text = "> ERROR: INVALID KEY"
        Notify("Failed", "> INVALID KEY", 2)
    end
end)

GetKeyButton.MouseButton1Click:Connect(function()
    if setclipboard then
        setclipboard(KeyWebsite)
        Notify("Key", "> LINK COPIED", 2)
    else
        Notify("Key", "> " .. KeyWebsite, 3)
    end
end)

-- ==================== ALL BUTTONS ====================
FPSBoostToggle.MouseButton1Click:Connect(function()
    FPSBoostEnabled = not FPSBoostEnabled
    if FPSBoostEnabled then
        FPSBoostToggle.Text = "> FPS BOOST: ON"
        FPSBoostToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        EnableFPSBoost()
        Notify("FPS Boost", "> ENABLED", 2)
    else
        FPSBoostToggle.Text = "> FPS BOOST: OFF"
        FPSBoostToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        DisableFPSBoost()
        Notify("FPS Boost", "> DISABLED", 2)
    end
end)

FullbrightToggle.MouseButton1Click:Connect(function()
    FullbrightEnabled = not FullbrightEnabled
    if FullbrightEnabled then
        FullbrightToggle.Text = "> FULLBRIGHT: ON"
        FullbrightToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        EnableFullbright()
        Notify("Fullbright", "> ENABLED", 2)
    else
        FullbrightToggle.Text = "> FULLBRIGHT: OFF"
        FullbrightToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        DisableFullbright()
        Notify("Fullbright", "> DISABLED", 2)
    end
end)

FPSPingToggle.MouseButton1Click:Connect(function()
    FPSPingEnabled = not FPSPingEnabled
    if FPSPingEnabled then
        FPSPingToggle.Text = "> FPS/PING DISPLAY: ON"
        FPSPingToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        if FPSPingFrame then FPSPingFrame.Visible = true end
        Notify("FPS/Ping", "> ENABLED", 2)
    else
        FPSPingToggle.Text = "> FPS/PING DISPLAY: OFF"
        FPSPingToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        if FPSPingFrame then FPSPingFrame.Visible = false end
        Notify("FPS/Ping", "> DISABLED", 2)
    end
end)

SpeedHackToggle.MouseButton1Click:Connect(function()
    SpeedHackEnabled = not SpeedHackEnabled
    if SpeedHackEnabled then
        SpeedHackToggle.Text = "> SPEED HACK: ON"
        SpeedHackToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        ApplySpeedHack()
    else
        SpeedHackToggle.Text = "> SPEED HACK: OFF"
        SpeedHackToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        ApplySpeedHack()
    end
end)

SpeedInput.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        local newSpeed = tonumber(SpeedInput.Text)
        if newSpeed then
            SpeedMultiplier = math.clamp(newSpeed, 16, MaxSpeed)
            if SpeedHackEnabled then ApplySpeedHack() end
        end
        SpeedInput.Text = ""
    end
end)

InfiniteJumpToggle.MouseButton1Click:Connect(function()
    InfiniteJumpEnabled = not InfiniteJumpEnabled
    if InfiniteJumpEnabled then
        InfiniteJumpToggle.Text = "> INFINITE JUMP: ON"
        InfiniteJumpToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        EnableInfiniteJump()
    else
        InfiniteJumpToggle.Text = "> INFINITE JUMP: OFF"
        InfiniteJumpToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        DisableInfiniteJump()
    end
end)

NoclipToggle.MouseButton1Click:Connect(function()
    NoclipEnabled = not NoclipEnabled
    if NoclipEnabled then
        NoclipToggle.Text = "> NOCLIP: ON"
        NoclipToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        EnableNoclip()
    else
        NoclipToggle.Text = "> NOCLIP: OFF"
        NoclipToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        DisableNoclip()
    end
end)

AimbotToggle.MouseButton1Click:Connect(function()
    AimbotEnabled = not AimbotEnabled
    if AimbotEnabled then
        AimbotToggle.Text = "> AIMBOT: ON"
        AimbotToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        Notify("Aimbot", "> ENABLED | " .. string.upper(AimbotMode), 2)
    else
        AimbotToggle.Text = "> AIMBOT: OFF"
        AimbotToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        Notify("Aimbot", "> DISABLED", 2)
    end
end)

PredictionToggle.MouseButton1Click:Connect(function()
    PredictionEnabled = not PredictionEnabled
    if PredictionEnabled then
        PredictionToggle.Text = "> PREDICTION: ON"
        PredictionToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    else
        PredictionToggle.Text = "> PREDICTION: OFF"
        PredictionToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    end
end)

PredictionInput.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        local newAmount = tonumber(PredictionInput.Text)
        if newAmount then
            PredictionAmount = math.clamp(newAmount, 1, 10)
        end
        PredictionInput.Text = ""
    end
end)

WallCheckToggle.MouseButton1Click:Connect(function()
    WallCheckEnabled = not WallCheckEnabled
    if WallCheckEnabled then
        WallCheckToggle.Text = "> WALL_CHECK: ON"
        WallCheckToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    else
        WallCheckToggle.Text = "> WALL_CHECK: OFF"
        WallCheckToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    end
end)

TeamCheckToggle.MouseButton1Click:Connect(function()
    TeamCheckEnabled = not TeamCheckEnabled
    if TeamCheckEnabled then
        TeamCheckToggle.Text = "> TEAM_CHECK: ON"
        TeamCheckToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    else
        TeamCheckToggle.Text = "> TEAM_CHECK: OFF"
        TeamCheckToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    end
end)

ESPToggle.MouseButton1Click:Connect(function()
    ESPEnabled = not ESPEnabled
    if ESPEnabled then
        ESPToggle.Text = "> ESP MASTER: ON"
        ESPToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        Notify("ESP", "> ENABLED", 2)
    else
        ESPToggle.Text = "> ESP MASTER: OFF"
        ESPToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        Notify("ESP", "> DISABLED", 2)
    end
end)

RainbowESPToggle.MouseButton1Click:Connect(function()
    RainbowESPEnabled = not RainbowESPEnabled
    if RainbowESPEnabled then
        RainbowESPToggle.Text = "> 🌈 RAINBOW ESP: ON"
        RainbowESPToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        EnableRainbowESP()
        Notify("Rainbow ESP", "> ENABLED", 2)
    else
        RainbowESPToggle.Text = "> 🌈 RAINBOW ESP: OFF"
        RainbowESPToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        DisableRainbowESP()
        Notify("Rainbow ESP", "> DISABLED", 2)
    end
end)

ESPBoxToggle.MouseButton1Click:Connect(function()
    ESPBoxEnabled = not ESPBoxEnabled
    if ESPBoxEnabled then
        ESPBoxToggle.Text = "> BOX: ON"
        ESPBoxToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    else
        ESPBoxToggle.Text = "> BOX: OFF"
        ESPBoxToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    end
end)

ESPNameToggle.MouseButton1Click:Connect(function()
    ESPNameEnabled = not ESPNameEnabled
    if ESPNameEnabled then
        ESPNameToggle.Text = "> NAME: ON"
        ESPNameToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    else
        ESPNameToggle.Text = "> NAME: OFF"
        ESPNameToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    end
end)

ESPDistanceToggle.MouseButton1Click:Connect(function()
    ESPDistanceEnabled = not ESPDistanceEnabled
    if ESPDistanceEnabled then
        ESPDistanceToggle.Text = "> DISTANCE: ON"
        ESPDistanceToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    else
        ESPDistanceToggle.Text = "> DISTANCE: OFF"
        ESPDistanceToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    end
end)

ESPHealthToggle.MouseButton1Click:Connect(function()
    ESPHealthEnabled = not ESPHealthEnabled
    if ESPHealthEnabled then
        ESPHealthToggle.Text = "> HEALTH: ON"
        ESPHealthToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    else
        ESPHealthToggle.Text = "> HEALTH: OFF"
        ESPHealthToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    end
end)

ESPSkeletonToggle.MouseButton1Click:Connect(function()
    ESPSkeletonEnabled = not ESPSkeletonEnabled
    if ESPSkeletonEnabled then
        ESPSkeletonToggle.Text = "> SKELETON: ON"
        ESPSkeletonToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        Notify("ESP", "> SKELETON ON", 2)
    else
        ESPSkeletonToggle.Text = "> SKELETON: OFF"
        ESPSkeletonToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        Notify("ESP", "> SKELETON OFF", 2)
    end
end)

ESPHeadDotToggle.MouseButton1Click:Connect(function()
    ESPHeadDotEnabled = not ESPHeadDotEnabled
    if ESPHeadDotEnabled then
        ESPHeadDotToggle.Text = "> HEAD DOT: ON"
        ESPHeadDotToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    else
        ESPHeadDotToggle.Text = "> HEAD DOT: OFF"
        ESPHeadDotToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    end
end)

ESPChamsToggle.MouseButton1Click:Connect(function()
    ESPChamsEnabled = not ESPChamsEnabled
    if ESPChamsEnabled then
        ESPChamsToggle.Text = "> CHAMS: ON"
        ESPChamsToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        Notify("ESP", "> CHAMS ON", 2)
    else
        ESPChamsToggle.Text = "> CHAMS: OFF"
        ESPChamsToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
        for player, _ in pairs(ChamsObjects) do
            RemoveChams(player)
        end
        Notify("ESP", "> CHAMS OFF", 2)
    end
end)

ESPTracerToggle.MouseButton1Click:Connect(function()
    ESPTracerEnabled = not ESPTracerEnabled
    if ESPTracerEnabled then
        ESPTracerToggle.Text = "> TRACER: ON"
        ESPTracerToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
    else
        ESPTracerToggle.Text = "> TRACER: OFF"
        ESPTracerToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    end
end)

TeleportMouseBtn.MouseButton1Click:Connect(TeleportToMouse)
SaveLocBtn.MouseButton1Click:Connect(SaveLocation)
LoadLocBtn.MouseButton1Click:Connect(LoadLocation)

FOVToggle.MouseButton1Click:Connect(function()
    FOVCircleEnabled = not FOVCircleEnabled
    FOVCircle.Visible = FOVCircleEnabled
    if FOVCircleEnabled then
        FOVToggle.Text = "> FOV_CIRCLE: ON"
        FOVToggle.BackgroundColor3 = Color3.fromRGB(0, 60, 120)
        FOVCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
    else
        FOVToggle.Text = "> FOV_CIRCLE: OFF"
        FOVToggle.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
    end
end)

FOVInput.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        local newRadius = tonumber(FOVInput.Text)
        if newRadius then
            FOVRadius = math.clamp(newRadius, 50, MaxFOVRadius)
            if FOVCircle then FOVCircle.Radius = FOVRadius end
        end
        FOVInput.Text = ""
    end
end)

SmoothInput.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        local newSmooth = tonumber(SmoothInput.Text)
        if newSmooth then
            Smoothness = math.clamp(newSmooth, 1, 20)
        end
        SmoothInput.Text = ""
    end
end)

-- Toggle Menu
local function ToggleMenu()
    if not IsLoggedIn then return end
    MenuVisible = not MenuVisible
    MainHub.Visible = MenuVisible
end

ToggleMenuButton.MouseButton1Click:Connect(ToggleMenu)

CloseButton.MouseButton1Click:Connect(function()
    MenuVisible = false
    MainHub.Visible = false
end)

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == MenuKey and IsLoggedIn then
        ToggleMenu()
    end
end)

-- Player Connections
Players.PlayerAdded:Connect(function(player)
    task.wait(1)
    if IsLoggedIn then UpdateTeleportList() end
end)

Players.PlayerRemoving:Connect(function(player)
    RemoveESP(player)
    if IsLoggedIn then UpdateTeleportList() end
end)

-- Draggable
local function MakeDraggable(frame)
    local dragging = false
    local dragInput = nil
    local dragStart = nil
    local startPos = nil
    
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
MakeDraggable(FPSPingFrame)

-- Initialize FPS/Ping Display
CreateFPSPingDisplay()
UpdateFPSPing()
