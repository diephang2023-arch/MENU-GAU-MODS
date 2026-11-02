
--[[
====================================================
              GAU MODS V3.0
          PREMIUM SCRIPT CENTER
====================================================
--]]

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")

local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

local LOGO_ID = "rbxassetid://THAY_ID_LOGO"
local OPEN_ICON_ID = "rbxassetid://THAY_ID_ANH_MO_MENU"

--==================================================
-- DANH SÁCH SCRIPT: 6 MỤC MỖI TAB
--==================================================

local Scripts = {
    -- TAB 1
    {Name = "SKIBIDI", URL = "", Tab = 1},
    {Name = "SCRIPT 02", URL = "", Tab = 1},
    {Name = "SCRIPT 03", URL = "", Tab = 1},
    {Name = "SCRIPT 04", URL = "", Tab = 1},
    {Name = "SCRIPT 05", URL = "", Tab = 1},
    {Name = "SCRIPT 06", URL = "", Tab = 1},

    -- TAB 2
    {Name = "SKIBIDI", URL = "", Tab = 2},
    {Name = "SCRIPT 02", URL = "", Tab = 2},
    {Name = "SCRIPT 03", URL = "", Tab = 2},
    {Name = "SCRIPT 04", URL = "", Tab = 2},
    {Name = "SCRIPT 05", URL = "", Tab = 2},
    {Name = "SCRIPT 06", URL = "", Tab = 2},

    -- TAB 3
    {Name = "SKIBIDI", URL = "", Tab = 3},
    {Name = "SCRIPT 02", URL = "", Tab = 3},
    {Name = "SCRIPT 03", URL = "", Tab = 3},
    {Name = "SCRIPT 04", URL = "", Tab = 3},
    {Name = "SCRIPT 05", URL = "", Tab = 3},
    {Name = "SCRIPT 06", URL = "", Tab = 3},
}

--==================================================
-- MÀU GIAO DIỆN
--==================================================

local Theme = {
    Background = Color3.fromRGB(10, 13, 23),
    Panel = Color3.fromRGB(19, 24, 39),
    Card = Color3.fromRGB(25, 31, 49),
    Blue = Color3.fromRGB(25, 105, 255),
    BlueLight = Color3.fromRGB(55, 155, 255),
    White = Color3.fromRGB(245, 247, 255),
    Gray = Color3.fromRGB(145, 158, 185),
    Green = Color3.fromRGB(50, 220, 120),
    Red = Color3.fromRGB(220, 55, 65)
}

--==================================================
-- TẠO SCREEN GUI
--==================================================

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "AXURS_HUB_V32"
ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = true
ScreenGui.Parent = PlayerGui

local function Corner(Object, Radius)
    local C = Instance.new("UICorner")
    C.CornerRadius = UDim.new(0, Radius)
    C.Parent = Object
    return C
end

local function Stroke(Object, Color, Thickness)
    local S = Instance.new("UIStroke")
    S.Color = Color
    S.Thickness = Thickness
    S.Parent = Object
    return S
end

--==================================================
-- NÚT MỞ MENU NỔI
--==================================================

local OpenButton = Instance.new("ImageButton")
OpenButton.Name = "OpenAXURS"
OpenButton.Parent = ScreenGui
OpenButton.Size = UDim2.new(0, 75, 0, 75)
OpenButton.Position = UDim2.new(0, 25, 0.5, -37)
OpenButton.BackgroundColor3 = Theme.Panel
OpenButton.Image = OPEN_ICON_ID
OpenButton.ScaleType = Enum.ScaleType.Fit
OpenButton.Visible = false
OpenButton.ZIndex = 10

Corner(OpenButton, 18)
Stroke(OpenButton, Theme.BlueLight, 2)

--==================================================
-- MENU CHÍNH
--==================================================

local Main = Instance.new("Frame")
Main.Name = "MainMenu"
Main.Parent = ScreenGui
Main.Size = UDim2.new(0, 650, 0, 490)
Main.Position = UDim2.new(0.5, -325, 0.5, -245)
Main.BackgroundColor3 = Theme.Background
Main.BorderSizePixel = 0
Main.ClipsDescendants = true

Corner(Main, 18)
Stroke(Main, Theme.Blue, 2)

--==================================================
-- HEADER
--==================================================

local Header = Instance.new("Frame")
Header.Parent = Main
Header.Size = UDim2.new(1, -4, 0, 105)
Header.Position = UDim2.new(0, 2, 0, 2)
Header.BackgroundColor3 = Theme.Panel
Header.BorderSizePixel = 0

Corner(Header, 16)

local Logo = Instance.new("ImageLabel")
Logo.Parent = Header
Logo.Size = UDim2.new(0, 76, 0, 76)
Logo.Position = UDim2.new(0, 16, 0, 14)
Logo.BackgroundTransparency = 1
Logo.Image = LOGO_ID
Logo.ScaleType = Enum.ScaleType.Fit

local Title = Instance.new("TextLabel")
Title.Parent = Header
Title.Size = UDim2.new(0, 330, 0, 43)
Title.Position = UDim2.new(0, 105, 0, 17)
Title.BackgroundTransparency = 1
Title.Text = "AXURS HUB"
Title.TextColor3 = Theme.White
Title.TextSize = 29
Title.Font = Enum.Font.GothamBlack
Title.TextXAlignment = Enum.TextXAlignment.Left

local Subtitle = Instance.new("TextLabel")
Subtitle.Parent = Header
Subtitle.Size = UDim2.new(0, 330, 0, 28)
Subtitle.Position = UDim2.new(0, 106, 0, 58)
Subtitle.BackgroundTransparency = 1
Subtitle.Text = "PREMIUM SCRIPT CENTER"
Subtitle.TextColor3 = Theme.Gray
Subtitle.TextSize = 15
Subtitle.Font = Enum.Font.GothamBold
Subtitle.TextXAlignment = Enum.TextXAlignment.Left

local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Parent = Header
MinimizeButton.Size = UDim2.new(0, 42, 0, 42)
MinimizeButton.Position = UDim2.new(1, -104, 0.5, -21)
MinimizeButton.BackgroundColor3 = Color3.fromRGB(38, 45, 68)
MinimizeButton.Text = "−"
MinimizeButton.TextColor3 = Theme.White
MinimizeButton.TextSize = 30
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.BorderSizePixel = 0
Corner(MinimizeButton, 11)

local CloseButton = Instance.new("TextButton")
CloseButton.Parent = Header
CloseButton.Size = UDim2.new(0, 42, 0, 42)
CloseButton.Position = UDim2.new(1, -54, 0.5, -21)
CloseButton.BackgroundColor3 = Theme.Red
CloseButton.Text = "X"
CloseButton.TextColor3 = Theme.White
CloseButton.TextSize = 24
CloseButton.Font = Enum.Font.GothamBold
CloseButton.BorderSizePixel = 0
Corner(CloseButton, 11)

--==================================================
-- KÉO MENU BẰNG HEADER
--==================================================

local Dragging = false
local DragInput
local DragStart
local StartPosition

Header.InputBegan:Connect(function(Input)
    if Input.UserInputType == Enum.UserInputType.MouseButton1
        or Input.UserInputType == Enum.UserInputType.Touch then

        if Input.Position.X >= CloseButton.AbsolutePosition.X - 5
            or Input.Position.X >= MinimizeButton.AbsolutePosition.X - 5 then
            return
        end

        Dragging = true
        DragStart = Input.Position
        StartPosition = Main.Position
    end
end)

Header.InputEnded:Connect(function(Input)
    if Input.UserInputType == Enum.UserInputType.MouseButton1
        or Input.UserInputType == Enum.UserInputType.Touch then
        Dragging = false
    end
end)

UIS.InputChanged:Connect(function(Input)
    if not Dragging then return end

    if Input.UserInputType == Enum.UserInputType.MouseMovement
        or Input.UserInputType == Enum.UserInputType.Touch then

        local Delta = Input.Position - DragStart

        Main.Position = UDim2.new(
            StartPosition.X.Scale,
            StartPosition.X.Offset + Delta.X,
            StartPosition.Y.Scale,
            StartPosition.Y.Offset + Delta.Y
        )
    end
end)

--==================================================
-- KHU VỰC TAB
--==================================================

local TabHolder = Instance.new("Frame")
TabHolder.Parent = Main
TabHolder.Size = UDim2.new(1, -28, 0, 48)
TabHolder.Position = UDim2.new(0, 14, 0, 115)
TabHolder.BackgroundTransparency = 1

local TabNames = {
    "⚡ TAB 1",
    "👑 TAB 2",
    "⚙ TAB 3"
}

local TabButtons = {}
local TabPages = {}

local ContentHolder = Instance.new("Frame")
ContentHolder.Parent = Main
ContentHolder.Size = UDim2.new(1, -28, 0, 330)
ContentHolder.Position = UDim2.new(0, 14, 0, 172)
ContentHolder.BackgroundColor3 = Theme.Panel
ContentHolder.BorderSizePixel = 0

Corner(ContentHolder, 15)

for i = 1, 3 do
    local TabButton = Instance.new("TextButton")
    TabButton.Parent = TabHolder
    TabButton.Size = UDim2.new(1/3, -7, 1, 0)
    TabButton.Position = UDim2.new((i-1)/3, 0, 0, 0)
    TabButton.BackgroundColor3 = Theme.Card
    TabButton.Text = TabNames[i]
    TabButton.TextColor3 = Theme.Gray
    TabButton.TextSize = 16
    TabButton.Font = Enum.Font.GothamBold
    TabButton.BorderSizePixel = 0
    Corner(TabButton, 12)

    TabButtons[i] = TabButton

    local Page = Instance.new("ScrollingFrame")
    Page.Name = "Tab" .. i
    Page.Parent = ContentHolder
    Page.Size = UDim2.new(1, -12, 1, -12)
    Page.Position = UDim2.new(0, 6, 0, 6)
    Page.BackgroundTransparency = 1
    Page.BorderSizePixel = 0
    Page.ScrollBarThickness = 5
    Page.ScrollBarImageColor3 = Theme.BlueLight
    Page.CanvasSize = UDim2.new(0, 0, 0, 0)
    Page.AutomaticCanvasSize = Enum.AutomaticSize.Y
    Page.Visible = (i == 1)

    local Layout = Instance.new("UIListLayout")
    Layout.Parent = Page
    Layout.Padding = UDim.new(0, 9)
    Layout.SortOrder = Enum.SortOrder.LayoutOrder

    local Padding = Instance.new("UIPadding")
    Padding.Parent = Page
    Padding.PaddingTop = UDim.new(0, 7)
    Padding.PaddingBottom = UDim.new(0, 7)
    Padding.PaddingLeft = UDim.new(0, 7)
    Padding.PaddingRight = UDim.new(0, 7)

    TabPages[i] = Page
end

--==================================================
-- CHUYỂN TAB
--==================================================

local function SelectTab(Number)
    for i = 1, 3 do
        TabPages[i].Visible = (i == Number)

        if i == Number then
            TabButtons[i].BackgroundColor3 = Theme.Blue
            TabButtons[i].TextColor3 = Theme.White
        else
            TabButtons[i].BackgroundColor3 = Theme.Card
            TabButtons[i].TextColor3 = Theme.Gray
        end
    end
end

for i = 1, 3 do
    TabButtons[i].MouseButton1Click:Connect(function()
        SelectTab(i)
    end)
end

SelectTab(1)

--==================================================
-- TẠO CÁC THẺ SCRIPT
--==================================================

for Index, Data in ipairs(Scripts) do
    local TabNumber = math.clamp(tonumber(Data.Tab) or 1, 1, 3)

    local Card = Instance.new("Frame")
    Card.Parent = TabPages[TabNumber]
    Card.Size = UDim2.new(1, -8, 0, 76)
    Card.BackgroundColor3 = Theme.Card
    Card.BorderSizePixel = 0
    Card.LayoutOrder = Index

    Corner(Card, 13)
    Stroke(Card, Color3.fromRGB(40, 48, 70), 1)

    local Icon = Instance.new("TextLabel")
    Icon.Parent = Card
    Icon.Size = UDim2.new(0, 55, 0, 55)
    Icon.Position = UDim2.new(0, 10, 0.5, -27)
    Icon.BackgroundTransparency = 1
    Icon.Text = "🚀"
    Icon.TextSize = 29

    local ScriptName = Instance.new("TextLabel")
    ScriptName.Parent = Card
    ScriptName.Size = UDim2.new(0.55, 0, 0, 31)
    ScriptName.Position = UDim2.new(0, 70, 0, 9)
    ScriptName.BackgroundTransparency = 1
    ScriptName.Text = string.format("%02d  •  %s", Index, tostring(Data.Name))
    ScriptName.TextColor3 = Theme.White
    ScriptName.TextSize = 17
    ScriptName.Font = Enum.Font.GothamBold
    ScriptName.TextXAlignment = Enum.TextXAlignment.Left
    ScriptName.TextTruncate = Enum.TextTruncate.AtEnd

    local Status = Instance.new("TextLabel")
    Status.Parent = Card
    Status.Size = UDim2.new(0.55, 0, 0, 23)
    Status.Position = UDim2.new(0, 70, 0, 40)
    Status.BackgroundTransparency = 1
    Status.Text = "● SẴN SÀNG"
    Status.TextColor3 = Theme.Green
    Status.TextSize = 13
    Status.Font = Enum.Font.GothamBold
    Status.TextXAlignment = Enum.TextXAlignment.Left

    local EnableButton = Instance.new("TextButton")
    EnableButton.Parent = Card
    EnableButton.Size = UDim2.new(0, 105, 0, 46)
    EnableButton.Position = UDim2.new(1, -118, 0.5, -23)
    EnableButton.BackgroundColor3 = Theme.Blue
    EnableButton.Text = "BẬT"
    EnableButton.TextColor3 = Theme.White
    EnableButton.TextSize = 17
    EnableButton.Font = Enum.Font.GothamBold
    EnableButton.BorderSizePixel = 0
    Corner(EnableButton, 10)

    EnableButton.MouseButton1Click:Connect(function()
        Status.Text = "● ĐÃ CHỌN"
        Status.TextColor3 = Theme.BlueLight
        EnableButton.Text = "ĐÃ CHỌN"
        EnableButton.BackgroundColor3 = Color3.fromRGB(35, 80, 145)
    end)
end

--==================================================
-- CHÂN MENU
--==================================================

local Footer = Instance.new("TextLabel")
Footer.Parent = Main
Footer.Size = UDim2.new(1, -28, 0, 32)
Footer.Position = UDim2.new(0, 14, 1, -38)
Footer.BackgroundTransparency = 1
Footer.Text = "♛  GAU MODS V3.0                    Enjoy Script! ❤️"
Footer.TextColor3 = Theme.Gray
Footer.TextSize = 13
Footer.Font = Enum.Font.GothamBold
Footer.TextXAlignment = Enum.TextXAlignment.Left

--==================================================
-- BẢNG XÁC NHẬN TẮT
--==================================================

local ConfirmOverlay = Instance.new("Frame")
ConfirmOverlay.Parent = ScreenGui
ConfirmOverlay.Size = UDim2.new(1, 0, 1, 0)
ConfirmOverlay.BackgroundColor3 = Color3.new(0, 0, 0)
ConfirmOverlay.BackgroundTransparency = 0.35
ConfirmOverlay.Visible = false
ConfirmOverlay.ZIndex = 100

local ConfirmBox = Instance.new("Frame")
ConfirmBox.Parent = ConfirmOverlay
ConfirmBox.Size = UDim2.new(0, 320, 0, 170)
ConfirmBox.Position = UDim2.new(0.5, -160, 0.5, -85)
ConfirmBox.BackgroundColor3 = Theme.Panel
ConfirmBox.BorderSizePixel = 0
ConfirmBox.ZIndex = 101

Corner(ConfirmBox, 15)
Stroke(ConfirmBox, Theme.BlueLight, 2)

local Question = Instance.new("TextLabel")
Question.Parent = ConfirmBox
Question.Size = UDim2.new(1, -20, 0, 75)
Question.Position = UDim2.new(0, 10, 0, 10)
Question.BackgroundTransparency = 1
Question.Text = "BẠN CÓ MUỐN TẮT AXURS HUB KHÔNG?"
Question.TextColor3 = Theme.White
Question.TextSize = 17
Question.Font = Enum.Font.GothamBold
Question.TextWrapped = true
Question.ZIndex = 102

local YesButton = Instance.new("TextButton")
YesButton.Parent = ConfirmBox
YesButton.Size = UDim2.new(0, 120, 0, 42)
YesButton.Position = UDim2.new(0, 25, 1, -57)
YesButton.BackgroundColor3 = Theme.Red
YesButton.Text = "CÓ"
YesButton.TextColor3 = Theme.White
YesButton.TextSize = 16
YesButton.Font = Enum.Font.GothamBold
YesButton.BorderSizePixel = 0
YesButton.ZIndex = 102
Corner(YesButton, 9)

local NoButton = Instance.new("TextButton")
NoButton.Parent = ConfirmBox
NoButton.Size = UDim2.new(0, 120, 0, 42)
NoButton.Position = UDim2.new(1, -145, 1, -57)
NoButton.BackgroundColor3 = Theme.Blue
NoButton.Text = "KHÔNG"
NoButton.TextColor3 = Theme.White
NoButton.TextSize = 16
NoButton.Font = Enum.Font.GothamBold
NoButton.BorderSizePixel = 0
NoButton.ZIndex = 102
Corner(NoButton, 9)

CloseButton.MouseButton1Click:Connect(function()
    ConfirmOverlay.Visible = true
end)

NoButton.MouseButton1Click:Connect(function()
    ConfirmOverlay.Visible = false
end)

YesButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)

--==================================================
-- THU NHỎ MENU
--==================================================

MinimizeButton.MouseButton1Click:Connect(function()
    Main.Visible = false
    OpenButton.Visible = true
end)

--==================================================
-- KÉO THẢ + CHẠM MỞ NÚT NỔI
--==================================================

local FloatingDragging = false
local FloatingInput = nil
local FloatingStart = nil
local FloatingStartPosition = nil
local FloatingMoved = false

OpenButton.InputBegan:Connect(function(Input)
    if Input.UserInputType == Enum.UserInputType.MouseButton1
        or Input.UserInputType == Enum.UserInputType.Touch then

        FloatingDragging = true
        FloatingInput = Input
        FloatingStart = Input.Position
        FloatingStartPosition = OpenButton.Position
        FloatingMoved = false
    end
end)

UIS.InputChanged:Connect(function(Input)
    if not FloatingDragging then return end

    local IsMouse = FloatingInput.UserInputType == Enum.UserInputType.MouseButton1
        and Input.UserInputType == Enum.UserInputType.MouseMovement

    local IsTouch = FloatingInput.UserInputType == Enum.UserInputType.Touch
        and Input == FloatingInput

    if IsMouse or IsTouch then
        local Delta = Input.Position - FloatingStart

        if math.abs(Delta.X) > 6 or math.abs(Delta.Y) > 6 then
            FloatingMoved = true
        end

        OpenButton.Position = UDim2.new(
            FloatingStartPosition.X.Scale,
            FloatingStartPosition.X.Offset + Delta.X,
            FloatingStartPosition.Y.Scale,
            FloatingStartPosition.Y.Offset + Delta.Y
        )
    end
end)

UIS.InputEnded:Connect(function(Input)
    if not FloatingDragging or not FloatingInput then return end

    local IsMouse = FloatingInput.UserInputType == Enum.UserInputType.MouseButton1
        and Input.UserInputType == Enum.UserInputType.MouseButton1

    local IsTouch = FloatingInput.UserInputType == Enum.UserInputType.Touch
        and Input == FloatingInput

    if IsMouse or IsTouch then
        FloatingDragging = false
        FloatingInput = nil

        if not FloatingMoved then
            Main.Visible = true
            OpenButton.Visible = false
        end
    end
end)

print("GAU MODS V3.0 đã khởi tạo!")
