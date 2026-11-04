... existing code ...
-- 1. KHỞI TẠO DỊCH VỤ CẦN THIẾT
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
-- Kiểm tra xem đã tồn tại GUI chưa, nếu có thì xóa cái cũ để tránh bị trùng lặp
if PlayerGui:FindFirstChild("GauModsUI") then
PlayerGui.GauModsUI:Destroy()
end
-- 2. TẠO SCREEN GUI CHÍNH (Đã chuyển sang PlayerGui chuẩn an toàn 100%)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "GauModsUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui
-- ==============================================================================
-- 3. NÚT MỞ MENU (TOGGLE BUTTON) - CÓ THỂ KÉO THẢ, BO TRÒN, VIỀN XANH
-- ==============================================================================
local OpenButton = Instance.new("ImageButton")
OpenButton.Name = "OpenButton"
OpenButton.Size = UDim2.new(0, 50, 0, 50)
OpenButton.Position = UDim2.new(0.05, 0, 0.2, 0)
OpenButton.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
OpenButton.Image = "rbxassetid://10723345518" -- ID Ảnh icon mặc định (Bạn có thể thay đổi ID ở đây)
OpenButton.ScaleType = Enum.ScaleType.Fit
OpenButton.Parent = ScreenGui
-- Bo tròn góc cho nút mở
local OpenCorner = Instance.new("UICorner")
OpenCorner.CornerRadius = UDim.new(0, 12)
OpenCorner.Parent = OpenButton
-- Viền màu xanh nước biển đậm đà cho nút mở
local OpenStroke = Instance.new("UIStroke")
OpenStroke.Color = Color3.fromRGB(0, 162, 255) -- Màu xanh nước biển
OpenStroke.Thickness = 2.5
OpenStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
OpenStroke.Parent = OpenButton
-- ==============================================================================
-- 4. BẢNG UI CHÍNH (MAIN MENU)
-- ==============================================================================
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 520, 0, 360)
MainFrame.Position = UDim2.new(0.5, -260, 0.5, -180)
MainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 24)
MainFrame.ClipsDescendants = true
MainFrame.Visible = false -- Ban đầu ẩn, bấm nút mở mới hiện
MainFrame.Parent = ScreenGui
local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 14)
MainCorner.Parent = MainFrame
local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(45, 45, 60)
MainStroke.Thickness = 1.5
MainStroke.Parent = MainFrame
-- --- thanh tiêu đề (Top Bar) ---
local TopBar = Instance.new("Frame")
TopBar.Name = "TopBar"
TopBar.Size = UDim2.new(1, 0, 0, 40)
TopBar.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
TopBar.Parent = MainFrame
local TopBarCorner = Instance.new("UICorner")
TopBarCorner.CornerRadius = UDim.new(0, 14)
TopBarCorner.Parent = TopBar
-- Icon ở góc trái TopBar
local HeaderIcon = Instance.new("ImageLabel")
HeaderIcon.Name = "HeaderIcon"
HeaderIcon.Size = UDim2.new(0, 26, 0, 26)
HeaderIcon.Position = UDim2.new(0, 10, 0, 7)
HeaderIcon.BackgroundTransparency = 1
HeaderIcon.Image = "rbxassetid://10723345518" -- ID Ảnh icon topbar (Bạn có thể thay đổi ID ở đây)
HeaderIcon.Parent = TopBar
-- Tiêu đề "GAU MODS"
local TitleLabel = Instance.new("TextLabel")
TitleLabel.Name = "TitleLabel"
TitleLabel.Size = UDim2.new(0, 150, 1, 0)
TitleLabel.Position = UDim2.new(0, 42, 0, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "GAU MODS"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.TextSize = 16
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = TopBar
-- Nút Thu nhỏ (-)
local MinimizeBtn = Instance.new("TextButton")
MinimizeBtn.Name = "MinimizeBtn"
MinimizeBtn.Size = UDim2.new(0, 30, 0, 30)
MinimizeBtn.Position = UDim2.new(1, -70, 0, 5)
MinimizeBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
MinimizeBtn.Text = "-"
MinimizeBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
MinimizeBtn.TextSize = 20
MinimizeBtn.Font = Enum.Font.GothamBold
MinimizeBtn.Parent = TopBar
local MinBtnCorner = Instance.new("UICorner")
MinBtnCorner.CornerRadius = UDim.new(0, 6)
MinBtnCorner.Parent = MinimizeBtn
-- Nút Tắt (X)
local CloseBtn = Instance.new("TextButton")
CloseBtn.Name = "CloseBtn"
CloseBtn.Size = UDim2.new(0, 30, 0, 30)
CloseBtn.Position = UDim2.new(1, -35, 0, 5)
CloseBtn.BackgroundColor3 = Color3.fromRGB(220, 50, 50)
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.TextSize = 14
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.Parent = TopBar
local CloseBtnCorner = Instance.new("UICorner")
CloseBtnCorner.CornerRadius = UDim.new(0, 6)
CloseBtnCorner.Parent = CloseBtn
-- ==============================================================================
-- 5. CÁC NÚT TAB (TAB 1, TAB 2, TAB 3)
-- ==============================================================================
local TabBar = Instance.new("Frame")
TabBar.Name = "TabBar"
TabBar.Size = UDim2.new(1, -20, 0, 35)
TabBar.Position = UDim2.new(0, 10, 0, 48)
TabBar.BackgroundTransparency = 1
TabBar.Parent = MainFrame
local TabLayout = Instance.new("UIListLayout")
TabLayout.FillDirection = Enum.FillDirection.Horizontal
TabLayout.Padding = UDim.new(0, 10)
TabLayout.Parent = TabBar
local function CreateTabButton(name, text)
local btn = Instance.new("TextButton")
btn.Name = name
btn.Size = UDim2.new(0.315, 0, 1, 0)
btn.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
btn.Text = text
btn.TextColor3 = Color3.fromRGB(180, 180, 200)
btn.Font = Enum.Font.GothamSemibold
btn.TextSize = 13
btn.Parent = TabBar
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 8)
corner.Parent = btn
return btn
end
local Tab1Btn = CreateTabButton("Tab1Btn", "Tab 1")
local Tab2Btn = CreateTabButton("Tab2Btn", "Tab 2")
local Tab3Btn = CreateTabButton("Tab3Btn", "Tab 3")
-- Container chứa nội dung các Tab
local ContentContainer = Instance.new("Frame")
ContentContainer.Name = "ContentContainer"
ContentContainer.Size = UDim2.new(1, -20, 1, -95)
ContentContainer.Position = UDim2.new(0, 10, 0, 90)
ContentContainer.BackgroundTransparency = 1
ContentContainer.Parent = MainFrame
-- Helper tạo Tab Frame
local function CreateTabFrame(name)
local f = Instance.new("Frame")
f.Name = name
f.Size = UDim2.new(1, 0, 1, 0)
f.BackgroundTransparency = 1
f.Visible = false
f.Parent = ContentContainer
return f
end
local Tab1Frame = CreateTabFrame("Tab1Frame")
local Tab2Frame = CreateTabFrame("Tab2Frame")
local Tab3Frame = CreateTabFrame("Tab3Frame")
-- Mặc định mở Tab 1
Tab1Frame.Visible = true
Tab1Btn.BackgroundColor3 = Color3.fromRGB(0, 122, 255)
Tab1Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
-- Logic chuyển Tab
local function SwitchTab(targetBtn, targetFrame)
for _, btn in ipairs(TabBar:GetChildren()) do
if btn:IsA("TextButton") then
btn.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
btn.TextColor3 = Color3.fromRGB(180, 180, 200)
end
end
for _, frame in ipairs(ContentContainer:GetChildren()) do
if frame:IsA("Frame") then
frame.Visible = false
end
end
targetBtn.BackgroundColor3 = Color3.fromRGB(0, 122, 255)
targetBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
targetFrame.Visible = true
end
Tab1Btn.MouseButton1Click:Connect(function() SwitchTab(Tab1Btn, Tab1Frame) end)
Tab2Btn.MouseButton1Click:Connect(function() SwitchTab(Tab2Btn, Tab2Frame) end)
Tab3Btn.MouseButton1Click:Connect(function() SwitchTab(Tab3Btn, Tab3Frame) end)
-- ==============================================================================
-- 6. NỘI DUNG TAB 1 (EXECUTE, LINK VIEWER, COPY, PASTE, SAVE)
-- ==============================================================================
-- Ô nhập văn bản (ScrollingFrame chứa TextBox để cuộn khi văn bản dài)
local InputContainer = Instance.new("ScrollingFrame")
InputContainer.Name = "InputContainer"
InputContainer.Size = UDim2.new(1, 0, 0, 170)
InputContainer.Position = UDim2.new(0, 0, 0, 0)
InputContainer.BackgroundColor3 = Color3.fromRGB(24, 24, 32)
InputContainer.BorderSizePixel = 0
InputContainer.ScrollBarThickness = 5
InputContainer.CanvasSize = UDim2.new(0, 0, 0, 0)
InputContainer.AutomaticCanvasSize = Enum.AutomaticSize.Y
InputContainer.Parent = Tab1Frame
local InputCorner = Instance.new("UICorner")
InputCorner.CornerRadius = UDim.new(0, 8)
InputCorner.Parent = InputContainer
-- Viền màu trắng nhạt cho ô nhập
local InputStroke = Instance.new("UIStroke")
InputStroke.Color = Color3.fromRGB(255, 255, 255)
InputStroke.Transparency = 0.85
InputStroke.Thickness = 1.5
InputStroke.Parent = InputContainer
local InputBox = Instance.new("TextBox")
InputBox.Name = "InputBox"
InputBox.Size = UDim2.new(1, -16, 1, -16)
InputBox.Position = UDim2.new(0, 8, 0, 8)
InputBox.BackgroundTransparency = 1
InputBox.Text = ""
InputBox.PlaceholderText = "Nhập văn bản..." -- Chữ mờ khi chưa nhập gì
InputBox.PlaceholderColor3 = Color3.fromRGB(120, 120, 140)
InputBox.TextColor3 = Color3.fromRGB(240, 240, 240)
InputBox.TextSize = 13
InputBox.Font = Enum.Font.Code
InputBox.TextXAlignment = Enum.TextXAlignment.Left
InputBox.TextYAlignment = Enum.TextYAlignment.Top
InputBox.ClearTextOnFocus = false
InputBox.MultiLine = true
InputBox.TextWrapped = true -- Giới hạn chữ không vượt quá ô vuông
InputBox.Parent = InputContainer
-- Thanh chứa 5 nút chức năng bên dưới Tab 1
local ButtonBar = Instance.new("Frame")
ButtonBar.Name = "ButtonBar"
ButtonBar.Size = UDim2.new(1, 0, 0, 35)
ButtonBar.Position = UDim2.new(0, 0, 1, -35)
ButtonBar.BackgroundTransparency = 1
ButtonBar.Parent = Tab1Frame
local ButtonLayout = Instance.new("UIListLayout")
ButtonLayout.FillDirection = Enum.FillDirection.Horizontal
ButtonLayout.Padding = UDim.new(0, 6)
ButtonLayout.Parent = ButtonBar
local function CreateActionBtn(text, widthScale)
local btn = Instance.new("TextButton")
btn.Size = UDim2.new(widthScale, -5, 1, 0)
btn.BackgroundColor3 = Color3.fromRGB(35, 35, 48)
btn.Text = text
btn.TextColor3 = Color3.fromRGB(230, 230, 230)
btn.Font = Enum.Font.GothamSemibold
btn.TextSize = 11
btn.Parent = ButtonBar
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 6)
corner.Parent = btn
local stroke = Instance.new("UIStroke")
stroke.Color = Color3.fromRGB(60, 60, 80)
stroke.Thickness = 1
stroke.Parent = btn
return btn
end
local ExecBtn = CreateActionBtn("Execute", 0.2)
local OpenLinkBtn = CreateActionBtn("Mở link", 0.2)
local CopyBtn = CreateActionBtn("Sao chép", 0.2)
local PasteBtn = CreateActionBtn("Dán", 0.2)
local SaveBtn = CreateActionBtn("Lưu", 0.2)
-- ==============================================================================
-- 7. UI MỞ LINK (VIEWER OVERLAY) - KHÔNG THỂ KÉO DI CHUYỂN
-- ==============================================================================
local LinkViewerOverlay = Instance.new("Frame")
LinkViewerOverlay.Name = "LinkViewerOverlay"
LinkViewerOverlay.Size = UDim2.new(1, 0, 1, 0)
LinkViewerOverlay.Position = UDim2.new(0, 0, 0, 0)
LinkViewerOverlay.BackgroundColor3 = Color3.fromRGB(18, 18, 24)
LinkViewerOverlay.Visible = false
LinkViewerOverlay.ZIndex = 10
LinkViewerOverlay.Parent = MainFrame
local ViewerCorner = Instance.new("UICorner")
ViewerCorner.CornerRadius = UDim.new(0, 14)
ViewerCorner.Parent = LinkViewerOverlay
-- TopBar cho Viewer
local ViewerTopBar = Instance.new("Frame")
ViewerTopBar.Size = UDim2.new(1, 0, 0, 40)
ViewerTopBar.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
ViewerTopBar.ZIndex = 11
ViewerTopBar.Parent = LinkViewerOverlay
local ViewerTopCorner = Instance.new("UICorner")
ViewerTopCorner.CornerRadius = UDim.new(0, 14)
ViewerTopCorner.Parent = ViewerTopBar
-- Nút quay lại (Mũi tên bên trái)
local BackBtn = Instance.new("TextButton")
BackBtn.Size = UDim2.new(0, 30, 0, 30)
BackBtn.Position = UDim2.new(0, 8, 0, 5)
BackBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
BackBtn.Text = "←"
BackBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
BackBtn.TextSize = 18
BackBtn.Font = Enum.Font.GothamBold
BackBtn.ZIndex = 12
BackBtn.Parent = ViewerTopBar
local BackBtnCorner = Instance.new("UICorner")
BackBtnCorner.CornerRadius = UDim.new(0, 6)
BackBtnCorner.Parent = BackBtn
-- Nút Copy bên phải TopBar
local ViewerCopyBtn = Instance.new("TextButton")
ViewerCopyBtn.Size = UDim2.new(0, 70, 0, 28)
ViewerCopyBtn.Position = UDim2.new(1, -78, 0, 6)
ViewerCopyBtn.BackgroundColor3 = Color3.fromRGB(0, 122, 255)
ViewerCopyBtn.Text = "Copy"
ViewerCopyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ViewerCopyBtn.Font = Enum.Font.GothamSemibold
ViewerCopyBtn.TextSize = 12
ViewerCopyBtn.ZIndex = 12
ViewerCopyBtn.Parent = ViewerTopBar
local ViewerCopyCorner = Instance.new("UICorner")
ViewerCopyCorner.CornerRadius = UDim.new(0, 6)
ViewerCopyCorner.Parent = ViewerCopyBtn
-- Ô hiển thị nội dung link mở rộng
local ViewerScroll = Instance.new("ScrollingFrame")
ViewerScroll.Size = UDim2.new(1, -20, 1, -55)
ViewerScroll.Position = UDim2.new(0, 10, 0, 48)
ViewerScroll.BackgroundColor3 = Color3.fromRGB(24, 24, 32)
ViewerScroll.ScrollBarThickness = 6
ViewerScroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
ViewerScroll.ZIndex = 11
ViewerScroll.Parent = LinkViewerOverlay
local ViewerScrollCorner = Instance.new("UICorner")
ViewerScrollCorner.CornerRadius = UDim.new(0, 8)
ViewerScrollCorner.Parent = ViewerScroll
local ViewerScrollStroke = Instance.new("UIStroke")
ViewerScrollStroke.Color = Color3.fromRGB(255, 255, 255)
ViewerScrollStroke.Transparency = 0.85
ViewerScrollStroke.Thickness = 1.5
ViewerScrollStroke.Parent = ViewerScroll
local ViewerText = Instance.new("TextLabel")
ViewerText.Size = UDim2.new(1, -16, 1, -16)
ViewerText.Position = UDim2.new(0, 8, 0, 8)
ViewerText.BackgroundTransparency = 1
ViewerText.Text = ""
ViewerText.TextColor3 = Color3.fromRGB(220, 220, 230)
ViewerText.TextSize = 12
ViewerText.Font = Enum.Font.Code
ViewerText.TextXAlignment = Enum.TextXAlignment.Left
ViewerText.TextYAlignment = Enum.TextYAlignment.Top
ViewerText.TextWrapped = true
ViewerText.ZIndex = 12
ViewerText.Parent = ViewerScroll
-- Xử lý mở link và lấy nội dung web (Có Try-catch an toàn)
OpenLinkBtn.MouseButton1Click:Connect(function()
local url = InputBox.Text
if url ~= "" then
ViewerText.Text = "Đang tải dữ liệu từ URL..."
LinkViewerOverlay.Visible = true
task.spawn(function()
local success, response = pcall(function()
return game:HttpGet(url)
end)
if success then
ViewerText.Text = response
else
ViewerText.Text = "Không thể tải URL này (Lỗi phân quyền hoặc đường dẫn sai)!\nChi tiết: " .. tostring(response)
end
end)
end
end)
BackBtn.MouseButton1Click:Connect(function()
LinkViewerOverlay.Visible = false
end)
ViewerCopyBtn.MouseButton1Click:Connect(function()
pcall(function()
if setclipboard then
setclipboard(ViewerText.Text)
end
end)
end)
-- Execute Script
ExecBtn.MouseButton1Click:Connect(function()
local scriptContent = InputBox.Text
if scriptContent ~= "" then
pcall(function()
if scriptContent:sub(1, 4) == "http" then
loadstring(game:HttpGet(scriptContent))()
else
loadstring(scriptContent)()
end
end)
end
end)
-- Copy & Paste
CopyBtn.MouseButton1Click:Connect(function()
pcall(function()
if setclipboard then
setclipboard(InputBox.Text)
end
end)
end)
PasteBtn.MouseButton1Click:Connect(function()
pcall(function()
if getclipboard then
InputBox.Text = getclipboard()
end
end)
end)
-- ==============================================================================
-- 8. UI LƯU SCRIPT VÀ LƯU DỮ LIỆU TỰ ĐỘNG
-- ==============================================================================
local SaveModal = Instance.new("Frame")
SaveModal.Name = "SaveModal"
SaveModal.Size = UDim2.new(0, 300, 0, 160)
SaveModal.Position = UDim2.new(0.5, -150, 0.5, -80)
SaveModal.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
SaveModal.Visible = false
SaveModal.ZIndex = 20
SaveModal.Parent = MainFrame
local SaveModalCorner = Instance.new("UICorner")
SaveModalCorner.CornerRadius = UDim.new(0, 10)
SaveModalCorner.Parent = SaveModal
local SaveModalStroke = Instance.new("UIStroke")
SaveModalStroke.Color = Color3.fromRGB(0, 122, 255)
SaveModalStroke.Thickness = 1.5
SaveModalStroke.Parent = SaveModal
local SaveModalTitle = Instance.new("TextLabel")
SaveModalTitle.Size = UDim2.new(1, 0, 0, 30)
SaveModalTitle.BackgroundTransparency = 1
SaveModalTitle.Text = "LƯU SCRIPT"
SaveModalTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
SaveModalTitle.Font = Enum.Font.GothamBold
SaveModalTitle.TextSize = 14
SaveModalTitle.ZIndex = 21
SaveModalTitle.Parent = SaveModal
local SaveNameBox = Instance.new("TextBox")
SaveNameBox.Size = UDim2.new(1, -30, 0, 35)
SaveNameBox.Position = UDim2.new(0, 15, 0, 45)
SaveNameBox.BackgroundColor3 = Color3.fromRGB(35, 35, 48)
SaveNameBox.PlaceholderText = "Tối đa 15 từ..."
SaveNameBox.Text = ""
SaveNameBox.TextColor3 = Color3.fromRGB(255, 255, 255)
SaveNameBox.Font = Enum.Font.Gotham
SaveNameBox.TextSize = 13
SaveNameBox.ZIndex = 21
SaveNameBox.Parent = SaveModal
local SaveNameCorner = Instance.new("UICorner")
SaveNameCorner.CornerRadius = UDim.new(0, 6)
SaveNameCorner.Parent = SaveNameBox
-- Giới hạn tối đa 15 từ
SaveNameBox:GetPropertyChangedSignal("Text"):Connect(function()
local words = {}
for word in SaveNameBox.Text:gmatch("%S+") do
table.insert(words, word)
end
if #words > 15 then
SaveNameBox.Text = table.concat(words, " ", 1, 15)
end
end)
local ConfirmSaveBtn = Instance.new("TextButton")
ConfirmSaveBtn.Size = UDim2.new(0, 100, 0, 32)
ConfirmSaveBtn.Position = UDim2.new(0.5, -50, 0, 100)
ConfirmSaveBtn.BackgroundColor3 = Color3.fromRGB(0, 122, 255)
ConfirmSaveBtn.Text = "Lưu"
ConfirmSaveBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ConfirmSaveBtn.Font = Enum.Font.GothamBold
ConfirmSaveBtn.TextSize = 13
ConfirmSaveBtn.ZIndex = 21
ConfirmSaveBtn.Parent = SaveModal
local ConfirmSaveCorner = Instance.new("UICorner")
ConfirmSaveCorner.CornerRadius = UDim.new(0, 6)
ConfirmSaveCorner.Parent = ConfirmSaveBtn
SaveBtn.MouseButton1Click:Connect(function()
if InputBox.Text ~= "" then
SaveModal.Visible = true
end
end)
-- Quản lý danh sách lưu trữ (Tự động thích ứng Studio / Executor)
local SAVE_FILE_NAME = "GauMods_SavedScripts.json"
local SavedScripts = {}
local function LoadSavedData()
pcall(function()
if readfile and isfile and isfile(SAVE_FILE_NAME) then
SavedScripts = HttpService:JSONDecode(readfile(SAVE_FILE_NAME))
end
end)
end
local function SaveDataToFile()
pcall(function()
if writefile then
writefile(SAVE_FILE_NAME, HttpService:JSONEncode(SavedScripts))
end
end)
end
LoadSavedData()
-- ==============================================================================
-- 9. NỘI DUNG TAB 2 (DANH SÁCH SCRIPT ĐÃ LƯU)
-- ==============================================================================
local Tab2Scroll = Instance.new("ScrollingFrame")
Tab2Scroll.Size = UDim2.new(1, 0, 1, 0)
Tab2Scroll.BackgroundTransparency = 1
Tab2Scroll.ScrollBarThickness = 5
Tab2Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
Tab2Scroll.Parent = Tab2Frame
local Tab2List = Instance.new("UIListLayout")
Tab2List.Padding = UDim.new(0, 8)
Tab2List.Parent = Tab2Scroll
local function RenderSavedScripts()
-- Xóa các danh sách cũ
for _, item in ipairs(Tab2Scroll:GetChildren()) do
if item:IsA("Frame") then
item:Destroy()
end
end
for index, data in ipairs(SavedScripts) do
local itemFrame = Instance.new("Frame")
itemFrame.Size = UDim2.new(1, 0, 0, 45)
itemFrame.BackgroundColor3 = Color3.fromRGB(26, 26, 36)
itemFrame.Parent = Tab2Scroll
local itemCorner = Instance.new("UICorner")
itemCorner.CornerRadius = UDim.new(0, 8)
itemCorner.Parent = itemFrame
local itemTitle = Instance.new("TextLabel")
itemTitle.Size = UDim2.new(0.5, -10, 1, 0)
itemTitle.Position = UDim2.new(0, 10, 0, 0)
itemTitle.BackgroundTransparency = 1
itemTitle.Text = data.Name
itemTitle.TextColor3 = Color3.fromRGB(240, 240, 240)
itemTitle.Font = Enum.Font.GothamSemibold
itemTitle.TextSize = 13
itemTitle.TextXAlignment = Enum.TextXAlignment.Left
itemTitle.Parent = itemFrame
-- Nút Execute trên danh sách lưu
local execSaveBtn = Instance.new(
