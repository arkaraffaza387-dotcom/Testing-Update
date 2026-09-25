--[[
    ZetGames-AimLock-Advanserver | TESTING BUILD v4.4
    Theme: Red & Black (TESTING THEME)
    Status: TESTING - PLEASE WAIT FOR VERSION 4.4 (LATEST)
    Simple Login -> Auto Kick System
    6 Keys Included
--]]

--==============================================================
-- SERVICES
--==============================================================
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer

--==============================================================
-- THEME COLORS (RED & BLACK - TESTING)
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
    WarningColor = Color3.fromRGB(255, 200, 0),
}

--==============================================================
-- KEY SYSTEM (6 KEYS)
--==============================================================
local ValidKeys = {
    ["AzferModz"] = {Expiry = 0, Level = "Premium"},
    ["AzferFree"] = {Expiry = os.time({year=2026, month=9, day=5, hour=0, min=0, sec=0}), Level = "Free"},
    ["AzferCode"] = {Expiry = os.time({year=2026, month=11, day=26, hour=0, min=0, sec=0}), Level = "Code"},
    ["AzferHc"] = {Expiry = os.time({year=2027, month=1, day=27, hour=0, min=0, sec=0}), Level = "Code"},
    ["FazxyFree"] = {Expiry = os.time({year=2027, month=9, day=10, hour=0, min=0, sec=0}), Level = "Code"},
    ["FreePrem-By-Fazxy"] = {Expiry = 0, Level = "Free"},
}

local KeyWebsite = "https://arkaraffaza387-dotcom.github.io/Key-Zero/"

--==============================================================
-- SCREEN GUI (FIXED - MULTIPLE FALLBACK)
--==============================================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ZetGamesAimLockAdvanserver"
ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = true
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local parentSuccess = false
pcall(function()
    ScreenGui.Parent = game:GetService("CoreGui")
    parentSuccess = true
end)
if not parentSuccess then
    pcall(function()
        ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
        parentSuccess = true
    end)
end
if not parentSuccess then
    pcall(function()
        ScreenGui.Parent = game:GetService("Players").LocalPlayer:WaitForChild("PlayerGui")
    end)
end

--==============================================================
-- NOTIFICATION (RED THEME)
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
-- KICK OVERLAY (v4.4 TESTING MESSAGE)
--==============================================================
local function ShowPatchKickOverlay()
    local KickOverlay = Instance.new("Frame")
    KickOverlay.Size = UDim2.new(1, 0, 1, 0)
    KickOverlay.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    KickOverlay.BackgroundTransparency = 0.15
    KickOverlay.ZIndex = 900
    KickOverlay.Parent = ScreenGui

    local KickFrame = Instance.new("Frame")
    KickFrame.Size = UDim2.new(0, 480, 0, 300)
    KickFrame.Position = UDim2.new(0.5, -240, 0.5, -150)
    KickFrame.BackgroundColor3 = THEME.MainBG
    KickFrame.BorderColor3 = THEME.WarningColor
    KickFrame.BorderSizePixel = 3
    KickFrame.ZIndex = 901
    KickFrame.Parent = KickOverlay
    Instance.new("UICorner", KickFrame).CornerRadius = UDim.new(0, 15)

    local KTitle = Instance.new("TextLabel")
    KTitle.Size = UDim2.new(1, -30, 0, 35)
    KTitle.Position = UDim2.new(0, 15, 0, 15)
    KTitle.BackgroundTransparency = 1
    KTitle.Text = "⚠ TESTING BUILD v4.4 ⚠"
    KTitle.TextColor3 = THEME.WarningColor
    KTitle.Font = Enum.Font.Code
    KTitle.TextSize = 18
    KTitle.ZIndex = 902
    KTitle.Parent = KickFrame

    local KLine = Instance.new("Frame")
    KLine.Size = UDim2.new(1, -30, 0, 2)
    KLine.Position = UDim2.new(0, 15, 0, 55)
    KLine.BackgroundColor3 = THEME.AccentColor
    KLine.BorderSizePixel = 0
    KLine.ZIndex = 902
    KLine.Parent = KickFrame

    local KMsg1 = Instance.new("TextLabel")
    KMsg1.Size = UDim2.new(1, -30, 0, 70)
    KMsg1.Position = UDim2.new(0, 15, 0, 70)
    KMsg1.BackgroundTransparency = 1
    KMsg1.Text = "PLEASE WAIT FOR\nVERSION 4.4 ( LATEST )"
    KMsg1.TextColor3 = Color3.fromRGB(255, 255, 255)
    KMsg1.Font = Enum.Font.Code
    KMsg1.TextSize = 20
    KMsg1.TextWrapped = true
    KMsg1.ZIndex = 902
    KMsg1.Parent = KickFrame

    local KMsg2 = Instance.new("TextLabel")
    KMsg2.Size = UDim2.new(1, -30, 0, 60)
    KMsg2.Position = UDim2.new(0, 15, 0, 150)
    KMsg2.BackgroundTransparency = 1
    KMsg2.Text = "VERSI TESTING INI SEDANG DALAM PROSES\nMOHON TUNGGU VERSI TERBARU RILIS"
    KMsg2.TextColor3 = THEME.WarningColor
    KMsg2.Font = Enum.Font.Code
    KMsg2.TextSize = 11
    KMsg2.TextWrapped = true
    KMsg2.ZIndex = 902
    KMsg2.Parent = KickFrame

    local KMsg3 = Instance.new("TextLabel")
    KMsg3.Size = UDim2.new(1, -30, 0, 25)
    KMsg3.Position = UDim2.new(0, 15, 0, 215)
    KMsg3.BackgroundTransparency = 1
    KMsg3.Text = "> [ TESTING BUILD - NOT FOR PUBLIC USE ]"
    KMsg3.TextColor3 = THEME.AccentLight
    KMsg3.Font = Enum.Font.Code
    KMsg3.TextSize = 10
    KMsg3.ZIndex = 902
    KMsg3.Parent = KickFrame

    local KStatus = Instance.new("TextLabel")
    KStatus.Size = UDim2.new(1, -30, 0, 25)
    KStatus.Position = UDim2.new(0, 15, 0, 240)
    KStatus.BackgroundTransparency = 1
    KStatus.Text = "> YOU WILL BE DISCONNECTED IN 3 SECONDS..."
    KStatus.TextColor3 = THEME.AccentColor
    KStatus.Font = Enum.Font.Code
    KStatus.TextSize = 11
    KStatus.TextXAlignment = Enum.TextXAlignment.Left
    KStatus.ZIndex = 902
    KStatus.Parent = KickFrame

    local KFooter = Instance.new("TextLabel")
    KFooter.Size = UDim2.new(1, -30, 0, 20)
    KFooter.Position = UDim2.new(0, 15, 0, 268)
    KFooter.BackgroundTransparency = 1
    KFooter.Text = "> ZETGAMES-AIMLOCK-ADVANSERVER | TESTING v4.4"
    KFooter.TextColor3 = THEME.TextLight
    KFooter.Font = Enum.Font.Code
    KFooter.TextSize = 9
    KFooter.ZIndex = 902
    KFooter.Parent = KickFrame
end

local function KickWithPatchMessage()
    ShowPatchKickOverlay()
    task.wait(3)
    pcall(function()
        LocalPlayer:Kick("PLEASE WAIT FOR VERSION 4.4 ( LATEST )")
    end)
end

--==============================================================
-- LOADING SCREEN
--==============================================================
local LoadingScreen = Instance.new("Frame")
LoadingScreen.Size = UDim2.new(1, 0, 1, 0)
LoadingScreen.BackgroundColor3 = Color3.fromRGB(5, 0, 0)
LoadingScreen.BorderSizePixel = 0
LoadingScreen.ZIndex = 300
LoadingScreen.Visible = true
LoadingScreen.Parent = ScreenGui

local LoadingBg = Instance.new("Frame")
LoadingBg.Size = UDim2.new(0, 340, 0, 230)
LoadingBg.Position = UDim2.new(0.5, -170, 0.5, -115)
LoadingBg.BackgroundColor3 = THEME.MainBG
LoadingBg.BorderColor3 = THEME.AccentColor
LoadingBg.BorderSizePixel = 2
LoadingBg.ZIndex = 301
LoadingBg.Parent = LoadingScreen
Instance.new("UICorner", LoadingBg).CornerRadius = UDim.new(0, 15)

local LTitle = Instance.new("TextLabel")
LTitle.Size = UDim2.new(1, -30, 0, 35)
LTitle.Position = UDim2.new(0, 15, 0, 15)
LTitle.BackgroundTransparency = 1
LTitle.Text = "ZETGAMES-AIMLOCK"
LTitle.TextColor3 = THEME.TextColor
LTitle.Font = Enum.Font.Code
LTitle.TextSize = 20
LTitle.ZIndex = 302
LTitle.Parent = LoadingBg

local LTag = Instance.new("TextLabel")
LTag.Size = UDim2.new(1, -30, 0, 20)
LTag.Position = UDim2.new(0, 15, 0, 45)
LTag.BackgroundTransparency = 1
LTag.Text = "[ TESTING BUILD - v4.4 ]"
LTag.TextColor3 = THEME.WarningColor
LTag.Font = Enum.Font.Code
LTag.TextSize = 11
LTag.ZIndex = 302
LTag.Parent = LoadingBg

local LSub = Instance.new("TextLabel")
LSub.Size = UDim2.new(1, -30, 0, 25)
LSub.Position = UDim2.new(0, 15, 0, 70)
LSub.BackgroundTransparency = 1
LSub.Text = "> INITIALIZING SYSTEM..."
LSub.TextColor3 = THEME.TextLight
LSub.Font = Enum.Font.Code
LSub.TextSize = 12
LSub.TextXAlignment = Enum.TextXAlignment.Left
LSub.ZIndex = 302
LSub.Parent = LoadingBg

local LBarBg = Instance.new("Frame")
LBarBg.Size = UDim2.new(1, -30, 0, 15)
LBarBg.Position = UDim2.new(0, 15, 0, 105)
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
LPercent.Position = UDim2.new(0, 15, 0, 130)
LPercent.BackgroundTransparency = 1
LPercent.Text = "0%"
LPercent.TextColor3 = THEME.TextColor
LPercent.Font = Enum.Font.Code
LPercent.TextSize = 14
LPercent.ZIndex = 302
LPercent.Parent = LoadingBg

local LStatus = Instance.new("TextLabel")
LStatus.Size = UDim2.new(1, -30, 0, 20)
LStatus.Position = UDim2.new(0, 15, 0, 165)
LStatus.BackgroundTransparency = 1
LStatus.Text = "> LOADING..."
LStatus.TextColor3 = THEME.TextLight
LStatus.Font = Enum.Font.Code
LStatus.TextSize = 10
LStatus.TextXAlignment = Enum.TextXAlignment.Left
LStatus.ZIndex = 302
LStatus.Parent = LoadingBg

local LFooter = Instance.new("TextLabel")
LFooter.Size = UDim2.new(1, -30, 0, 20)
LFooter.Position = UDim2.new(0, 15, 0, 195)
LFooter.BackgroundTransparency = 1
LFooter.Text = "> PLEASE WAIT FOR VERSION 4.4 ( LATEST )"
LFooter.TextColor3 = THEME.WarningColor
LFooter.Font = Enum.Font.Code
LFooter.TextSize = 9
LFooter.ZIndex = 302
LFooter.Parent = LoadingBg

--==============================================================
-- LOGIN FRAME
--==============================================================
local LoginFrame = Instance.new("Frame")
LoginFrame.Size = UDim2.new(0, 320, 0, 440)
LoginFrame.Position = UDim2.new(0.5, -160, 0.5, -220)
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
LTopTxt.Text = "● ZETGAMES-AIMLOCK TESTING v4.4"
LTopTxt.TextColor3 = THEME.TextColor
LTopTxt.Font = Enum.Font.Code
LTopTxt.TextSize = 11
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
LTag2.Text = "[ TESTING BUILD - v4.4 ]"
LTag2.TextColor3 = THEME.WarningColor
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
KeyInput.ClearTextOnFocus = false
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
GetKeyBtn.BackgroundColor3 = THEME.AccentDark
GetKeyBtn.BorderColor3 = THEME.AccentColor
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
Instr.Size = UDim2.new(1, -30, 0, 70)
Instr.Position = UDim2.new(0, 15, 0, 350)
Instr.BackgroundTransparency = 1
Instr.Text = "> STEPS:\n> 1. Click GET KEY\n> 2. Generate key\n> 3. Enter key\n> 4. AUTHENTICATE\n> [ TESTING BUILD - WAIT v4.4 ]"
Instr.TextColor3 = THEME.TextLight
Instr.Font = Enum.Font.Code
Instr.TextSize = 9
Instr.TextXAlignment = Enum.TextXAlignment.Left
Instr.ZIndex = 12
Instr.Parent = LoginFrame

--==============================================================
-- DRAGGABLE
--==============================================================
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

--==============================================================
-- LOGIN LOGIC (TESTING AUTO KICK SYSTEM - v4.4)
--==============================================================
LoginBtn.MouseButton1Click:Connect(function()
    local key = KeyInput.Text
    local keyData = ValidKeys[key]
    if keyData then
        if keyData.Expiry == 0 or os.time() < keyData.Expiry then
            StatusTxt.Text = "> ACCESS GRANTED..."
            Notify("Success", "> KEY ACCEPTED | " .. keyData.Level, 2)

            task.wait(0.8)
            Notify("ZetGames-AimLock", "> TESTING BUILD v4.4", 2.5)
            task.wait(1.2)
            Notify("Update", "> PLEASE WAIT FOR VERSION 4.4", 2.5)
            task.wait(1.2)
            Notify("Update", "> VERSI TERBARU ( LATEST )", 2.5)
            task.wait(1.2)
            Notify("Update", "> TUNGGU UPDATE SELANJUTNYA", 2.5)
            task.wait(2)

            KickWithPatchMessage()
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

--==============================================================
-- LOADING ANIMATION (AUTO START)
--==============================================================
local loadingMessages = {
    "> LOADING MODULES...",
    "> CONNECTING TO SERVER...",
    "> DECRYPTING DATA...",
    "> TESTING BUILD v4.4...",
    "> CHECKING COMPATIBILITY...",
    "> WAITING FOR VERSION 4.4 ( LATEST )...",
    "> SYSTEM READY..."
}

task.spawn(function()
    task.wait(0.5)
    local totalTime = 5
    local steps = 100
    local interval = totalTime / steps
    for i = 1, steps do
        task.wait(interval)
        pcall(function()
            LBarFill.Size = UDim2.new(i / 100, 0, 1, 0)
            LPercent.Text = i .. "%"
            local mi = math.floor(i / 15) + 1
            if mi > #loadingMessages then mi = #loadingMessages end
            LStatus.Text = loadingMessages[mi]
        end)
    end
    pcall(function()
        LPercent.Text = "100%"
        LStatus.Text = "> SYSTEM READY!"
        LBarFill.Size = UDim2.new(1, 0, 1, 0)
    end)
    task.wait(0.5)
    pcall(function()
        LoadingScreen.Visible = false
        LoginFrame.Visible = true
    end)
    Notify("ZetGames-AimLock TESTING v4.4", "> SYSTEM LOADED", 3)
    Notify("Login", "> ENTER ACCESS KEY", 3)
end)

print("[ZetGames-AimLock] TESTING BUILD v4.4 - Loaded Successfully")
print("[ZetGames-AimLock] Theme: Red & Black | Total Keys: 6")
