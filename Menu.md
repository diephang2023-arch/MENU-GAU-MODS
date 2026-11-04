--[[
    GAU MODS - Giao diện một file dành cho Delta Executor
    Phiên bản: 2.1 - One-click HTTPS / Lua executor

    CÁCH DÙNG:
    1) Dán mã Lua hoặc URL Raw HTTPS vào ô nhập.
    2) Nhấn Execute: URL sẽ được tải rồi biên dịch/chạy; Lua thuần chạy trực tiếp.
    3) Chỉ dùng mã và URL do anh kiểm soát hoặc tin cậy. Mã từ xa có thể chạy với
       quyền mà executor cung cấp và có thể gây mất dữ liệu/tài khoản.
    4) Đổi ICON_ID và AUTHOR_LINKS nếu cần.
]]

--==============================================================
-- PHẦN 1: CẤU HÌNH - ANH CÓ THỂ SỬA CÁC GIÁ TRỊ Ở ĐÂY
--==============================================================
local ICON_ID = "rbxassetid://0" -- Thay số 0 bằng ID ảnh của anh
local AUTHOR_LINKS = {
    TikTok = "https://www.tiktok.com/@your_name", -- Sửa thành link TikTok
    YouTube = "https://www.youtube.com/@your_channel", -- Sửa thành link YouTube
    Discord = "https://discord.gg/your_invite", -- Sửa thành link Discord
}

local MENU_TITLE = "GAU MODS"
local SAVE_FOLDER = "GAU_MODS"
local SAVE_FILE = SAVE_FOLDER .. "/saved.json"
local MAX_VIEW_TEXT = 250000 -- Giới hạn nội dung xem để tránh treo giao diện

--==============================================================
-- PHẦN 2: DỊCH VỤ ROBLOX VÀ HÀM HỖ TRỢ
--==============================================================
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local HttpService = game:GetService("HttpService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer

local function safeGet(parent, childName)
    local ok, result = pcall(function() return parent[childName] end)
    if ok then return result end
    return nil
end

local function notify(message)
    -- Thông báo nhỏ do chính menu tạo ra
    local playerGui = safeGet(LocalPlayer, "PlayerGui")
    local parent = playerGui
    if not parent then return end

    local old = parent:FindFirstChild("GAU_Toast")
    if old then old:Destroy() end

    local toastGui = Instance.new("ScreenGui")
    toastGui.Name = "GAU_Toast"
    toastGui.ResetOnSpawn = false
    toastGui.DisplayOrder = 100000
    toastGui.Parent = parent

    local toast = Instance.new("TextLabel")
    toast.Name = "Toast"
    toast.AnchorPoint = Vector2.new(0.5, 0)
    toast.Position = UDim2.new(0.5, 0, 0, 26)
    toast.Size = UDim2.new(0.82, 0, 0, 42)
    toast.BackgroundColor3 = Color3.fromRGB(22, 29, 42)
    toast.TextColor3 = Color3.fromRGB(240, 246, 255)
    toast.Text = tostring(message)
    toast.TextSize = 14
    toast.Font = Enum.Font.GothamMedium
    toast.TextWrapped = true
    toast.Parent = toastGui

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 10)
    corner.Parent = toast
    local stroke = Instance.new("UIStroke")
    stroke.Color = Color3.fromRGB(45, 150, 255)
    stroke.Thickness = 1.2
    stroke.Parent = toast

    task.delay(2.6, function()
        if toastGui and toastGui.Parent then toastGui:Destroy() end
    end)
end

local function make(className, props, parent)
    local object = Instance.new(className)
    for property, value in pairs(props or {}) do
        pcall(function() object[property] = value end)
    end
    object.Parent = parent
    return object
end

local function addCorner(object, radius)
    return make("UICorner", {CornerRadius = UDim.new(0, radius or 10)}, object)
end

local function addStroke(object, color, thickness, transparency)
    return make("UIStroke", {
        Color = color or Color3.fromRGB(80, 155, 255),
        Thickness = thickness or 1,
        Transparency = transparency or 0,
    }, object)
end

local function addPadding(object, left, right, top, bottom)
    return make("UIPadding", {
        PaddingLeft = UDim.new(0, left or 8),
        PaddingRight = UDim.new(0, right or 8),
        PaddingTop = UDim.new(0, top or 8),
        PaddingBottom = UDim.new(0, bottom or 8),
    }, object)
end

local function makeButton(parent, text, size, position, color)
    local button = make("TextButton", {
        Size = size,
        Position = position,
        BackgroundColor3 = color or Color3.fromRGB(31, 91, 157),
        BorderSizePixel = 0,
        Text = text,
        TextColor3 = Color3.fromRGB(245, 249, 255),
        TextSize = 13,
        Font = Enum.Font.GothamSemibold,
        AutoButtonColor = true,
    }, parent)
    addCorner(button, 9)
    return button
end

local function trim(value)
    return (tostring(value or ""):gsub("^%s+", ""):gsub("%s+$", ""))
end

local function wordCount(value)
    local count = 0
    for _ in tostring(value or ""):gmatch("%S+") do count = count + 1 end
    return count
end

--==============================================================
-- PHẦN 3: LƯU VÀ ĐỌC DỮ LIỆU TRÊN THIẾT BỊ
--==============================================================
local savedItems = {}
local storageAvailable = type(writefile) == "function" and type(readfile) == "function"

local function ensureSaveFolder()
    if type(makefolder) == "function" then
        local exists = false
        if type(isfolder) == "function" then
            local ok, result = pcall(isfolder, SAVE_FOLDER)
            exists = ok and result == true
        end
        if not exists then pcall(makefolder, SAVE_FOLDER) end
    end
end

local function loadSavedItems()
    savedItems = {}
    if not storageAvailable then
        return false
    end
    ensureSaveFolder()
    local ok, raw = pcall(readfile, SAVE_FILE)
    if not ok or type(raw) ~= "string" or raw == "" then
        return true
    end
    local decodedOk, decoded = pcall(function()
        return HttpService:JSONDecode(raw)
    end)
    if decodedOk and type(decoded) == "table" then
        savedItems = decoded
    end
    return true
end

local function persistSavedItems()
    if not storageAvailable then
        notify("Delta hiện không cung cấp readfile/writefile; danh sách chỉ còn trong phiên này.")
        return false
    end
    ensureSaveFolder()
    local okEncode, encoded = pcall(function()
        return HttpService:JSONEncode(savedItems)
    end)
    if not okEncode then
        notify("Không thể chuyển dữ liệu lưu thành JSON.")
        return false
    end
    local okWrite = pcall(writefile, SAVE_FILE, encoded)
    if not okWrite then
        notify("Không ghi được file. Hãy kiểm tra quyền lưu tệp của Delta.")
        return false
    end
    return true
end

loadSavedItems()

--==============================================================
-- PHẦN 4: TẠO GUI CHÍNH
--==============================================================
local parentForGui
local function getGuiParent()
    -- Ưu tiên gethui nếu executor có hỗ trợ; nếu không dùng CoreGui/PlayerGui.
    if type(gethui) == "function" then
        local ok, result = pcall(gethui)
        if ok and result then return result end
    end
    local okCore, core = pcall(function() return game:GetService("CoreGui") end)
    if okCore and core then return core end
    return LocalPlayer:WaitForChild("PlayerGui")
end
parentForGui = getGuiParent()

-- Xóa bản GAU MODS cũ nếu chạy script nhiều lần
local oldGui = parentForGui:FindFirstChild("GAU_MODS_GUI")
if oldGui then oldGui:Destroy() end

local screenGui = make("ScreenGui", {
    Name = "GAU_MODS_GUI",
    ResetOnSpawn = false,
    IgnoreGuiInset = true,
    ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
    DisplayOrder = 99999,
}, parentForGui)

local toggle = make("ImageButton", {
    Name = "OpenMenuButton",
    Size = UDim2.fromOffset(56, 56),
    Position = UDim2.new(0, 24, 0.42, 0),
    BackgroundColor3 = Color3.fromRGB(17, 25, 39),
    BorderSizePixel = 0,
    Image = ICON_ID,
    ScaleType = Enum.ScaleType.Fit,
    AutoButtonColor = true,
    ZIndex = 20,
}, screenGui)
addCorner(toggle, 14)
addStroke(toggle, Color3.fromRGB(37, 151, 255), 2, 0)

local main = make("Frame", {
    Name = "MainMenu",
    AnchorPoint = Vector2.new(0.5, 0.5),
    Position = UDim2.new(0.5, 0, 0.5, 0),
    Size = UDim2.new(0.88, 0, 0.72, 0),
    BackgroundColor3 = Color3.fromRGB(15, 20, 30),
    BorderSizePixel = 0,
    Visible = false,
    ClipsDescendants = true,
}, screenGui)
addCorner(main, 15)
addStroke(main, Color3.fromRGB(57, 139, 235), 1.5, 0.05)
make("UISizeConstraint", {MinSize = Vector2.new(310, 320), MaxSize = Vector2.new(560, 520)}, main)

local header = make("Frame", {
    Name = "Header",
    Size = UDim2.new(1, 0, 0, 48),
    BackgroundColor3 = Color3.fromRGB(22, 31, 46),
    BorderSizePixel = 0,
}, main)

local headerIcon = make("ImageLabel", {
    Name = "HeaderIcon",
    Position = UDim2.fromOffset(12, 8),
    Size = UDim2.fromOffset(32, 32),
    BackgroundTransparency = 1,
    Image = ICON_ID,
    ScaleType = Enum.ScaleType.Fit,
}, header)

make("TextLabel", {
    Name = "HeaderTitle",
    Position = UDim2.fromOffset(52, 0),
    Size = UDim2.new(1, -148, 1, 0),
    BackgroundTransparency = 1,
    Text = MENU_TITLE,
    TextColor3 = Color3.fromRGB(238, 245, 255),
    TextSize = 16,
    Font = Enum.Font.GothamBold,
    TextXAlignment = Enum.TextXAlignment.Left,
}, header)

-- Vùng kéo menu: phải giữ 0,5 giây mới bắt đầu kéo
local dragZone = make("Frame", {
    Name = "DragZone",
    Position = UDim2.fromOffset(48, 0),
    Size = UDim2.new(1, -145, 1, 0),
    BackgroundTransparency = 1,
    Active = true,
}, header)

local minimizeButton = makeButton(header, "−", UDim2.fromOffset(34, 32), UDim2.new(1, -82, 0, 8), Color3.fromRGB(42, 61, 86))
minimizeButton.TextSize = 23
local closeButton = makeButton(header, "×", UDim2.fromOffset(34, 32), UDim2.new(1, -42, 0, 8), Color3.fromRGB(132, 47, 57))
closeButton.TextSize = 22

local tabBar = make("Frame", {
    Name = "TabBar",
    Position = UDim2.fromOffset(10, 56),
    Size = UDim2.new(1, -20, 0, 38),
    BackgroundTransparency = 1,
}, main)

local tabButtons = {}
for i = 1, 3 do
    local tabButton = makeButton(tabBar, "TAB " .. i, UDim2.new(1/3, -6, 1, 0), UDim2.new((i-1)/3, (i == 1 and 0 or 3), 0, 0), Color3.fromRGB(34, 48, 68))
    tabButtons[i] = tabButton
end

local contentHolder = make("Frame", {
    Name = "ContentHolder",
    Position = UDim2.fromOffset(10, 102),
    Size = UDim2.new(1, -20, 1, -112),
    BackgroundTransparency = 1,
}, main)

local tabPages = {}
for i = 1, 3 do
    tabPages[i] = make("Frame", {
        Name = "TabPage" .. i,
        Size = UDim2.fromScale(1, 1),
        BackgroundTransparency = 1,
        Visible = i == 1,
    }, contentHolder)
end

local function selectTab(index)
    for i = 1, 3 do
        tabPages[i].Visible = i == index
        tabButtons[i].BackgroundColor3 = (i == index) and Color3.fromRGB(30, 111, 190) or Color3.fromRGB(34, 48, 68)
    end
end
for i = 1, 3 do
    tabButtons[i].MouseButton1Click:Connect(function() selectTab(i) end)
end

--==============================================================
-- PHẦN 5: TAB 1 - Ô NHẬP VĂN BẢN VÀ CÁC NÚT
--==============================================================
local editorBorder = make("Frame", {
    Name = "EditorBorder",
    Size = UDim2.new(1, 0, 1, -52),
    BackgroundColor3 = Color3.fromRGB(10, 15, 23),
    BorderSizePixel = 0,
    ClipsDescendants = true,
}, tabPages[1])
addCorner(editorBorder, 10)
addStroke(editorBorder, Color3.fromRGB(109, 127, 150), 1, 0.25)

local editor = make("TextBox", {
    Name = "ScriptInput",
    Position = UDim2.fromOffset(9, 8),
    Size = UDim2.new(1, -18, 1, -16),
    BackgroundTransparency = 1,
    ClearTextOnFocus = false,
    MultiLine = true,
    TextWrapped = true,
    TextXAlignment = Enum.TextXAlignment.Left,
    TextYAlignment = Enum.TextYAlignment.Top,
    Text = "",
    PlaceholderText = "Nhập văn bản",
    PlaceholderColor3 = Color3.fromRGB(120, 132, 150),
    TextColor3 = Color3.fromRGB(224, 233, 245),
    TextSize = 14,
    Font = Enum.Font.Code,
}, editorBorder)

local actionBar = make("Frame", {
    Name = "ActionBar",
    Position = UDim2.new(0, 0, 1, -44),
    Size = UDim2.new(1, 0, 0, 40),
    BackgroundTransparency = 1,
}, tabPages[1])
local actionLayout = make("UIListLayout", {
    FillDirection = Enum.FillDirection.Horizontal,
    HorizontalAlignment = Enum.HorizontalAlignment.Center,
    VerticalAlignment = Enum.VerticalAlignment.Center,
    Padding = UDim.new(0, 5),
    SortOrder = Enum.SortOrder.LayoutOrder,
}, actionBar)

local function actionButton(text, order)
    local button = makeButton(actionBar, text, UDim2.new(0.25, -5, 1, 0), UDim2.new(), Color3.fromRGB(32, 89, 145))
    button.LayoutOrder = order
    button.TextSize = 11
    return button
end
local executeButton = actionButton("Execute", 1)
local copyButton = actionButton("Sao chép", 2)
local pasteButton = actionButton("Dán", 3)
local saveButton = actionButton("Lưu", 4)

--==============================================================
-- PHẦN 6: KHUNG XEM LINK - CHỈ HIỂN THỊ VĂN BẢN, KHÔNG CHẠY MÃ
--==============================================================
local viewer = make("Frame", {
    Name = "LinkViewer",
    Size = UDim2.fromScale(1, 1),
    BackgroundColor3 = Color3.fromRGB(15, 20, 30),
    BorderSizePixel = 0,
    Visible = false,
    ZIndex = 30,
}, main)

local viewerTop = make("Frame", {
    Size = UDim2.new(1, 0, 0, 42),
    BackgroundColor3 = Color3.fromRGB(22, 31, 46),
    BorderSizePixel = 0,
    ZIndex = 31,
}, viewer)
local backButton = makeButton(viewerTop, "‹", UDim2.fromOffset(36, 30), UDim2.fromOffset(8, 6), Color3.fromRGB(42, 61, 86))
backButton.TextSize = 22
local viewerTitle = make("TextLabel", {
    Position = UDim2.fromOffset(52, 0),
    Size = UDim2.new(1, -150, 1, 0),
    BackgroundTransparency = 1,
    Text = "NỘI DUNG LINK",
    TextColor3 = Color3.fromRGB(238, 245, 255),
    TextSize = 13,
    Font = Enum.Font.GothamBold,
    TextXAlignment = Enum.TextXAlignment.Left,
    ZIndex = 32,
}, viewerTop)
local viewerCopyButton = makeButton(viewerTop, "Copy", UDim2.fromOffset(62, 30), UDim2.new(1, -70, 0, 6), Color3.fromRGB(30, 111, 190))
viewerCopyButton.ZIndex = 32

local viewerScroll = make("ScrollingFrame", {
    Name = "ViewerScroll",
    Position = UDim2.fromOffset(8, 50),
    Size = UDim2.new(1, -16, 1, -58),
    BackgroundColor3 = Color3.fromRGB(9, 13, 20),
    BorderSizePixel = 0,
    CanvasSize = UDim2.new(0, 0, 0, 0),
    ScrollBarThickness = 6,
    ScrollBarImageColor3 = Color3.fromRGB(74, 145, 220),
    AutomaticCanvasSize = Enum.AutomaticSize.Y,
    ZIndex = 31,
}, viewer)
addCorner(viewerScroll, 8)
addStroke(viewerScroll, Color3.fromRGB(109, 127, 150), 1, 0.35)
local viewerText = make("TextLabel", {
    Name = "ViewerText",
    Position = UDim2.fromOffset(10, 9),
    Size = UDim2.new(1, -20, 0, 30),
    AutomaticSize = Enum.AutomaticSize.Y,
    BackgroundTransparency = 1,
    Text = "Nội dung đường link sẽ hiển thị ở đây.",
    TextColor3 = Color3.fromRGB(222, 232, 245),
    TextSize = 13,
    Font = Enum.Font.Code,
    TextWrapped = true,
    TextXAlignment = Enum.TextXAlignment.Left,
    TextYAlignment = Enum.TextYAlignment.Top,
    ZIndex = 32,
}, viewerScroll)

local currentViewerText = ""
local function showViewer(text, title)
    currentViewerText = tostring(text or "")
    viewerText.Text = currentViewerText
    viewerTitle.Text = title or "NỘI DUNG LINK"
    viewer.Visible = true
end
backButton.MouseButton1Click:Connect(function() viewer.Visible = false end)
viewerCopyButton.MouseButton1Click:Connect(function()
    if type(setclipboard) == "function" then
        local ok = pcall(setclipboard, currentViewerText)
        notify(ok and "Đã sao chép nội dung." or "Không thể sao chép bằng clipboard của Delta.")
    else
        notify("Delta không có setclipboard. Hãy chọn văn bản trong khung để sao chép.")
    end
end)

--==============================================================
-- PHẦN 7: TAB 2 - DANH SÁCH ĐÃ LƯU
--==============================================================
local savedScroll = make("ScrollingFrame", {
    Name = "SavedList",
    Size = UDim2.fromScale(1, 1),
    BackgroundColor3 = Color3.fromRGB(10, 15, 23),
    BorderSizePixel = 0,
    CanvasSize = UDim2.new(0, 0, 0, 0),
    ScrollBarThickness = 6,
    ScrollBarImageColor3 = Color3.fromRGB(74, 145, 220),
    AutomaticCanvasSize = Enum.AutomaticSize.Y,
}, tabPages[2])
addCorner(savedScroll, 10)
addStroke(savedScroll, Color3.fromRGB(109, 127, 150), 1, 0.3)
addPadding(savedScroll, 8, 8, 8, 8)
local savedLayout = make("UIListLayout", {
    Padding = UDim.new(0, 8),
    SortOrder = Enum.SortOrder.LayoutOrder,
}, savedScroll)

local function clearSavedRows()
    for _, child in ipairs(savedScroll:GetChildren()) do
        if child:IsA("Frame") then child:Destroy() end
    end
end

--==============================================================
-- PHẦN 8: LỆNH TIỆN ÍCH ĐƯỢC CHO PHÉP - KHÔNG THỰC THI LUA TÙY Ý
--==============================================================
local function runSafeCommands(source)
    local ranSomething = false
    for line in tostring(source or ""):gmatch("[^\r\n]+") do
        line = trim(line)
        if line ~= "" then
            local command, payload = line:match("^(%w+)%s*:%s*(.*)$")
            local lower = line:lower()
            if lower == "help" then
                notify("Lệnh: help | time | print: nội dung | warn: nội dung | notify: nội dung | clear")
                ranSomething = true
            elseif lower == "time" then
                print("GAU MODS - Thời gian: " .. os.date("%H:%M:%S %d/%m/%Y"))
                notify("Đã in thời gian ra console.")
                ranSomething = true
            elseif lower == "clear" then
                editor.Text = ""
                notify("Đã xóa ô nhập văn bản.")
                ranSomething = true
            elseif command and command:lower() == "print" then
                print("[GAU MODS] " .. payload)
                ranSomething = true
            elseif command and command:lower() == "warn" then
                warn("[GAU MODS] " .. payload)
                ranSomething = true
            elseif command and command:lower() == "notify" then
                notify(payload)
                ranSomething = true
            else
                warn("[GAU MODS] Bỏ qua dòng không nằm trong danh sách lệnh an toàn: " .. line)
            end
        end
    end
    if not ranSomething then
        notify("Không có lệnh tiện ích hợp lệ. Nhập help để xem danh sách lệnh.")
    end
end

local executeLua -- Khai báo trước để nút chạy trong danh sách đã lưu dùng được.

local function refreshSavedList()
    clearSavedRows()
    if #savedItems == 0 then
        local empty = make("TextLabel", {
            Size = UDim2.new(1, -4, 0, 64),
            BackgroundTransparency = 1,
            Text = "Chưa có mục nào được lưu.\nQuay lại TAB 1 và nhấn Lưu.",
            TextColor3 = Color3.fromRGB(145, 158, 177),
            TextSize = 13,
            Font = Enum.Font.Gotham,
            TextWrapped = true,
        }, savedScroll)
        empty.LayoutOrder = 1
        return
    end

    for index, item in ipairs(savedItems) do
        local row = make("Frame", {
            Name = "SavedItem" .. index,
            Size = UDim2.new(1, -4, 0, 72),
            BackgroundColor3 = Color3.fromRGB(22, 31, 46),
            BorderSizePixel = 0,
            LayoutOrder = index,
        }, savedScroll)
        addCorner(row, 9)

        make("TextLabel", {
            Position = UDim2.fromOffset(10, 5),
            Size = UDim2.new(1, -150, 0, 25),
            BackgroundTransparency = 1,
            Text = tostring(item.name or ("Mục " .. index)),
            TextColor3 = Color3.fromRGB(236, 244, 255),
            TextSize = 13,
            Font = Enum.Font.GothamSemibold,
            TextXAlignment = Enum.TextXAlignment.Left,
            TextTruncate = Enum.TextTruncate.AtEnd,
        }, row)
        local preview = tostring(item.content or ""):gsub("\n", " ")
        make("TextLabel", {
            Position = UDim2.fromOffset(10, 31),
            Size = UDim2.new(1, -150, 0, 30),
            Background
