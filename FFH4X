-- ██████╗ ██╗  ██╗ ██████╗ ███████╗████████╗    ██╗  ██╗██╗████████╗
-- ██╔════╝ ██║  ██║██╔═══██╗██╔════╝╚══██╔══╝    ╚██╗██╔╝██║╚══██╔══╝
-- ██║  ███╗███████║██║   ██║███████╗   ██║        ╚███╔╝ ██║   ██║
-- ██║   ██║██╔══██║██║   ██║╚════██║   ██║        ██╔██╗ ██║   ██║
-- ╚██████╔╝██║  ██║╚██████╔╝███████║   ██║       ██╔╝ ██╗██║   ██║
-- GHOST XIT | Built by Olivia — https://discord.gg/ojailbreaks

-- ══════════════════════════════════════════
--  DELTA INVISÍVEL
--  CoreGui parent = não aparece em gravações
--  OBS / captura de tela não pega CoreGui
-- ══════════════════════════════════════════
local Players          = game:GetService("Players")
local RunService       = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService     = game:GetService("TweenService")
local CoreGui          = game:GetService("CoreGui")
local Camera           = workspace.CurrentCamera
local LP               = Players.LocalPlayer
local Mouse            = LP:GetMouse()

-- destroy antigo
pcall(function()
    if CoreGui:FindFirstChild("GHOSTXIT") then
        CoreGui.GHOSTXIT:Destroy()
    end
end)

-- ScreenGui no CoreGui — invisível em gravações
local SG = Instance.new("ScreenGui")
SG.Name              = "GHOSTXIT"
SG.ResetOnSpawn      = false
SG.ZIndexBehavior    = Enum.ZIndexBehavior.Sibling
SG.IgnoreGuiInset    = true
SG.DisplayOrder      = 999   -- sempre na frente
pcall(function() SG.Parent = CoreGui end)
-- fallback se executor não permite CoreGui
if not SG.Parent then
    SG.Parent = LP:WaitForChild("PlayerGui")
end

-- ══════════════════════════════════════════
--  CFG
-- ══════════════════════════════════════════
local CFG = {
    -- Aimbot
    AimbotEnabled = false,
    MagneticAim   = false,
    AimLegit      = false,
    AimbotBone    = "UpperTorso",
    AimbotSmooth  = 10,     -- 10 / 20 / 30
    IgnoreTeam    = true,
    IgnoreDead    = true,
    -- ESP
    ESPEnabled    = false,
    ESPSkeleton   = false,
    ESPBox        = false,
    ESPHealth     = false,
    ESPName       = false,
    ESPLine       = false,
    -- Combat
    NoRecoil      = false,
    -- Speed (preset: 30 / 35 / 40)
    SpeedValue    = 16,
    -- UI
    PanelVisible  = true,
    DeltaHidden   = false,   -- esconde o delta (ScreenGui inteiro)
}

local SAFE_CAP = 32

-- ══════════════════════════════════════════
--  HELPERS
-- ══════════════════════════════════════════
local function getChar(p)
    return p and p.Character
end
local function getHum(p)
    local c = getChar(p)
    return c and c:FindFirstChildOfClass("Humanoid")
end
local function getRoot(p)
    local c = getChar(p)
    return c and c:FindFirstChild("HumanoidRootPart")
end
local function getBone(p, b)
    local c = getChar(p)
    return c and (c:FindFirstChild(b) or c:FindFirstChild("HumanoidRootPart"))
end
local function isAlive(p)
    local h = getHum(p)
    return h and h.Health > 0
end
local function sameTeam(p)
    return p.Team and LP.Team and p.Team == LP.Team
end

local function getTargets()
    local t = {}
    for _, p in pairs(Players:GetPlayers()) do
        if p == LP then continue end
        if CFG.IgnoreDead and not isAlive(p) then continue end
        if CFG.IgnoreTeam and sameTeam(p)    then continue end
        if getRoot(p) then table.insert(t, p) end
    end
    return t
end

local function getNearestTarget()
    local best, bestDist = nil, math.huge
    local cx = Camera.ViewportSize.X / 2
    local cy = Camera.ViewportSize.Y / 2
    for _, p in pairs(getTargets()) do
        local bone = getBone(p, CFG.AimbotBone)
        if bone then
            local sp, vis = Camera:WorldToViewportPoint(bone.Position)
            if vis then
                local d = (Vector2.new(sp.X, sp.Y) - Vector2.new(cx, cy)).Magnitude
                if d < bestDist then
                    best, bestDist = p, d
                end
            end
        end
    end
    return best
end

-- ══════════════════════════════════════════
--  SPEED SAFE
-- ══════════════════════════════════════════
local function getOrMakeBV()
    if not LP.Character then return nil end
    local hrp = LP.Character:FindFirstChild("HumanoidRootPart")
    if not hrp then return nil end
    local ex = hrp:FindFirstChild("GX_BV")
    if ex then return ex end
    local bv = Instance.new("BodyVelocity")
    bv.Name = "GX_BV"
    bv.MaxForce = Vector3.new(1e5, 0, 1e5)
    bv.Velocity = Vector3.new(0, 0, 0)
    bv.Parent = hrp
    return bv
end
local function removeBV()
    if not LP.Character then return end
    local hrp = LP.Character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    local bv = hrp:FindFirstChild("GX_BV")
    if bv then bv:Destroy() end
end

-- ══════════════════════════════════════════
--  PALETA — FANTASMA ESCURO
-- ══════════════════════════════════════════
local G = {
    bg       = Color3.fromRGB(8,  8,  12),
    bgPanel  = Color3.fromRGB(12, 12, 18),
    bgRow    = Color3.fromRGB(18, 18, 26),
    topbar   = Color3.fromRGB(14, 14, 22),
    accent   = Color3.fromRGB(90, 90, 220),
    hi       = Color3.fromRGB(140,140,255),
    ghost    = Color3.fromRGB(180,180,220),
    btn      = Color3.fromRGB(30, 30, 55),
    txt      = Color3.fromRGB(210,210,240),
    dim      = Color3.fromRGB(100,100,140),
    sec      = Color3.fromRGB(60,  60,  90),
    knob     = Color3.fromRGB(230,230,255),
    track    = Color3.fromRGB(25, 25, 40),
    off      = Color3.fromRGB(30, 30, 45),
    on       = Color3.fromRGB(80, 80, 200),
    green    = Color3.fromRGB(0,  200, 100),
    red      = Color3.fromRGB(220, 50,  50),
    espCol   = Color3.fromRGB(100,180,255),
    skelCol  = Color3.fromRGB(80, 160,255),
}

-- ══════════════════════════════════════════
--  PANEL
-- ══════════════════════════════════════════
local Panel = Instance.new("Frame", SG)
Panel.Name              = "Panel"
Panel.Size              = UDim2.new(0, 400, 0, 500)
Panel.Position          = UDim2.new(0.5,-200,0.5,-250)
Panel.BackgroundColor3  = G.bgPanel
Panel.BackgroundTransparency = 0.04
Panel.BorderSizePixel   = 0
Panel.Active            = true
Panel.Draggable         = true
Panel.ClipsDescendants  = true
Instance.new("UICorner", Panel).CornerRadius = UDim.new(0, 12)
local PStroke = Instance.new("UIStroke", Panel)
PStroke.Color = G.accent; PStroke.Thickness = 1.4

-- ── TopBar ──
local TopBar = Instance.new("Frame", Panel)
TopBar.Size             = UDim2.new(1, 0, 0, 44)
TopBar.BackgroundColor3 = G.topbar
TopBar.BorderSizePixel  = 0
Instance.new("UICorner", TopBar).CornerRadius = UDim.new(0, 12)

-- ghost icon (texto)
local GhostIcon = Instance.new("TextLabel", TopBar)
GhostIcon.Text              = "👻"
GhostIcon.Font              = Enum.Font.GothamBold
GhostIcon.TextSize          = 20
GhostIcon.BackgroundTransparency = 1
GhostIcon.Size              = UDim2.new(0, 32, 1, 0)
GhostIcon.Position          = UDim2.new(0, 10, 0, 0)

local TitleL = Instance.new("TextLabel", TopBar)
TitleL.Text              = "GHOST XIT"
TitleL.Font              = Enum.Font.GothamBold
TitleL.TextSize          = 16
TitleL.TextColor3        = G.hi
TitleL.BackgroundTransparency = 1
TitleL.Size              = UDim2.new(0, 160, 1, 0)
TitleL.Position          = UDim2.new(0, 46, 0, 0)
TitleL.TextXAlignment    = Enum.TextXAlignment.Left

local SubL = Instance.new("TextLabel", TopBar)
SubL.Text             = "mobile external"
SubL.Font             = Enum.Font.Gotham
SubL.TextSize         = 10
SubL.TextColor3       = G.sec
SubL.BackgroundTransparency = 1
SubL.Size             = UDim2.new(0, 100, 1, 0)
SubL.Position         = UDim2.new(0, 46, 0, 22)
SubL.TextXAlignment   = Enum.TextXAlignment.Left

-- MinBtn
local MinBtn = Instance.new("TextButton", TopBar)
MinBtn.Text             = "—"
MinBtn.Font             = Enum.Font.GothamBold
MinBtn.TextSize         = 14
MinBtn.TextColor3       = G.hi
MinBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 45)
MinBtn.Size             = UDim2.new(0, 28, 0, 22)
MinBtn.Position         = UDim2.new(1, -34, 0.5, -11)
MinBtn.AutoButtonColor  = false
Instance.new("UICorner", MinBtn).CornerRadius = UDim.new(0, 6)

-- ── TabBar ──
local TabBarFrame = Instance.new("Frame", Panel)
TabBarFrame.Size            = UDim2.new(1, 0, 0, 34)
TabBarFrame.Position        = UDim2.new(0, 0, 0, 44)
TabBarFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 16)
TabBarFrame.BorderSizePixel = 0
local TBL = Instance.new("UIListLayout", TabBarFrame)
TBL.FillDirection = Enum.FillDirection.Horizontal
TBL.Padding = UDim.new(0, 2)
TBL.VerticalAlignment = Enum.VerticalAlignment.Center
Instance.new("UIPadding", TabBarFrame).PaddingLeft = UDim.new(0, 8)

-- ── Content ──
local ContentFrame = Instance.new("Frame", Panel)
ContentFrame.Size               = UDim2.new(1, 0, 1, -78)
ContentFrame.Position           = UDim2.new(0, 0, 0, 78)
ContentFrame.BackgroundTransparency = 1

-- ══════════════════════════════════════════
--  MINIMIZAR / ESCONDER DELTA
-- ══════════════════════════════════════════
local minimized = false
MinBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    ContentFrame.Visible  = not minimized
    TabBarFrame.Visible   = not minimized
    TweenService:Create(Panel, TweenInfo.new(0.16, Enum.EasingStyle.Quad), {
        Size = minimized
            and UDim2.new(0, 400, 0, 44)
            or  UDim2.new(0, 400, 0, 500)
    }):Play()
    MinBtn.Text = minimized and "□" or "—"
end)

-- INSERT = esconde painel completamente
-- TAP duplo em mobile: 2 toques em 0.3s no MinBtn
local lastTap = 0
MinBtn.MouseButton1Click:Connect(function()
    local now = tick()
    if now - lastTap < 0.35 then
        -- duplo tap = esconde/mostra tudo
        CFG.PanelVisible = not CFG.PanelVisible
        Panel.Visible    = CFG.PanelVisible
    end
    lastTap = now
end)

UserInputService.InputBegan:Connect(function(inp, gpe)
    if gpe then return end
    if inp.KeyCode == Enum.KeyCode.Insert then
        CFG.PanelVisible = not CFG.PanelVisible
        Panel.Visible    = CFG.PanelVisible
    end
end)

-- ══════════════════════════════════════════
--  FACTORIES
-- ══════════════════════════════════════════
local AllTabs = {}

local function NewTab(name, icon)
    local Btn = Instance.new("TextButton", TabBarFrame)
    Btn.Text            = icon .. " " .. name
    Btn.Font            = Enum.Font.GothamBold
    Btn.TextSize        = 11
    Btn.TextColor3      = G.dim
    Btn.BackgroundColor3 = Color3.fromRGB(18, 18, 28)
    Btn.AutoButtonColor = false
    Btn.Size            = UDim2.new(0, 88, 0, 28)
    Instance.new("UICorner", Btn).CornerRadius = UDim.new(0, 6)

    local Page = Instance.new("ScrollingFrame", ContentFrame)
    Page.Size               = UDim2.new(1, 0, 1, 0)
    Page.BackgroundTransparency = 1
    Page.ScrollBarThickness = 3
    Page.ScrollBarImageColor3 = G.accent
    Page.CanvasSize         = UDim2.new(0, 0, 0, 0)
    Page.AutomaticCanvasSize = Enum.AutomaticSize.Y
    Page.Visible            = false
    local PL = Instance.new("UIListLayout", Page)
    PL.Padding = UDim.new(0, 5)
    local PP = Instance.new("UIPadding", Page)
    PP.PaddingLeft  = UDim.new(0, 12)
    PP.PaddingRight = UDim.new(0, 12)
    PP.PaddingTop   = UDim.new(0, 10)

    local entry = {Btn = Btn, Page = Page}
    table.insert(AllTabs, entry)

    Btn.MouseButton1Click:Connect(function()
        for _, t in pairs(AllTabs) do
            t.Page.Visible        = false
            t.Btn.BackgroundColor3 = Color3.fromRGB(18, 18, 28)
            t.Btn.TextColor3      = G.dim
        end
        Page.Visible        = true
        Btn.BackgroundColor3 = G.accent
        Btn.TextColor3      = Color3.fromRGB(230, 230, 255)
    end)
    return Page
end

local function Section(parent, txt)
    local L = Instance.new("TextLabel", parent)
    L.Text  = "── " .. txt .. " ──"
    L.Font  = Enum.Font.GothamBold
    L.TextSize = 11
    L.TextColor3 = G.hi
    L.BackgroundTransparency = 1
    L.Size  = UDim2.new(1, 0, 0, 20)
    L.TextXAlignment = Enum.TextXAlignment.Center
end

local function Toggle(parent, label, cfgKey, cb)
    local Row = Instance.new("Frame", parent)
    Row.Size            = UDim2.new(1, 0, 0, 34)
    Row.BackgroundColor3 = G.bgRow
    Row.BorderSizePixel = 0
    Instance.new("UICorner", Row).CornerRadius = UDim.new(0, 8)

    local Lbl = Instance.new("TextLabel", Row)
    Lbl.Text            = label
    Lbl.Font            = Enum.Font.Gotham
    Lbl.TextSize        = 12
    Lbl.TextColor3      = G.txt
    Lbl.BackgroundTransparency = 1
    Lbl.Size            = UDim2.new(1, -58, 1, 0)
    Lbl.Position        = UDim2.new(0, 12, 0, 0)
    Lbl.TextXAlignment  = Enum.TextXAlignment.Left

    local Bg = Instance.new("Frame", Row)
    Bg.Size             = UDim2.new(0, 40, 0, 22)
    Bg.Position         = UDim2.new(1, -50, 0.5, -11)
    Bg.BackgroundColor3 = CFG[cfgKey] and G.on or G.off
    Instance.new("UICorner", Bg).CornerRadius = UDim.new(1, 0)

    local Knob = Instance.new("Frame", Bg)
    Knob.Size = UDim2.new(0, 16, 0, 16)
    Knob.Position = CFG[cfgKey]
        and UDim2.new(1, -18, 0.5, -8)
        or  UDim2.new(0,   2, 0.5, -8)
    Knob.BackgroundColor3 = G.knob
    Instance.new("UICorner", Knob).CornerRadius = UDim.new(1, 0)

    local Hit = Instance.new("TextButton", Row)
    Hit.Size               = UDim2.new(1, 0, 1, 0)
    Hit.BackgroundTransparency = 1
    Hit.Text               = ""

    Hit.MouseButton1Click:Connect(function()
        CFG[cfgKey] = not CFG[cfgKey]
        local v = CFG[cfgKey]
        TweenService:Create(Knob, TweenInfo.new(0.13), {
            Position = v and UDim2.new(1,-18,0.5,-8) or UDim2.new(0,2,0.5,-8)
        }):Play()
        TweenService:Create(Bg, TweenInfo.new(0.13), {
            BackgroundColor3 = v and G.on or G.off
        }):Play()
        if cb then pcall(cb, v) end
    end)
end

-- Slider com steps fixos (botões preset) + drag livre
local function SliderPreset(parent, label, cfgKey, presets, cb)
    local Row = Instance.new("Frame", parent)
    Row.Size            = UDim2.new(1, 0, 0, 56)
    Row.BackgroundColor3 = G.bgRow
    Row.BorderSizePixel = 0
    Instance.new("UICorner", Row).CornerRadius = UDim.new(0, 8)

    local LblV = Instance.new("TextLabel", Row)
    LblV.Text       = label .. ":  " .. tostring(CFG[cfgKey])
    LblV.Font       = Enum.Font.GothamBold
    LblV.TextSize   = 11
    LblV.TextColor3 = G.txt
    LblV.BackgroundTransparency = 1
    LblV.Size       = UDim2.new(1, -10, 0, 20)
    LblV.Position   = UDim2.new(0, 12, 0, 4)
    LblV.TextXAlignment = Enum.TextXAlignment.Left

    -- linha de botões preset
    local BtnRow = Instance.new("Frame", Row)
    BtnRow.Size             = UDim2.new(1, -24, 0, 24)
    BtnRow.Position         = UDim2.new(0, 12, 0, 28)
    BtnRow.BackgroundTransparency = 1
    local BL = Instance.new("UIListLayout", BtnRow)
    BL.FillDirection = Enum.FillDirection.Horizontal
    BL.Padding       = UDim.new(0, 6)

    for _, val in ipairs(presets) do
        local PBtn = Instance.new("TextButton", BtnRow)
        PBtn.Text            = tostring(val)
        PBtn.Font            = Enum.Font.GothamBold
        PBtn.TextSize        = 11
        PBtn.TextColor3      = CFG[cfgKey]==val and Color3.fromRGB(230,230,255) or G.dim
        PBtn.BackgroundColor3 = CFG[cfgKey]==val and G.accent or G.btn
        PBtn.AutoButtonColor = false
        PBtn.Size            = UDim2.new(0, 56, 0, 24)
        Instance.new("UICorner", PBtn).CornerRadius = UDim.new(0, 6)

        PBtn.MouseButton1Click:Connect(function()
            CFG[cfgKey] = val
            LblV.Text   = label .. ":  " .. tostring(val)
            -- reset todos os botões
            for _, child in ipairs(BtnRow:GetChildren()) do
                if child:IsA("TextButton") then
                    local isThis = tonumber(child.Text) == val
                    child.BackgroundColor3 = isThis and G.accent or G.btn
                    child.TextColor3       = isThis and Color3.fromRGB(230,230,255) or G.dim
                end
            end
            if cb then pcall(cb, val) end
        end)
    end
end

-- Slider livre (drag) para valores contínuos
local function Slider(parent, label, cfgKey, minVal, maxVal, step, cb)
    step = step or 1
    local Row = Instance.new("Frame", parent)
    Row.Size            = UDim2.new(1, 0, 0, 52)
    Row.BackgroundColor3 = G.bgRow
    Row.BorderSizePixel = 0
    Instance.new("UICorner", Row).CornerRadius = UDim.new(0, 8)

    local LblV = Instance.new("TextLabel", Row)
    LblV.Text       = label .. ":  " .. tostring(CFG[cfgKey])
    LblV.Font       = Enum.Font.GothamBold
    LblV.TextSize   = 11
    LblV.TextColor3 = G.txt
    LblV.BackgroundTransparency = 1
    LblV.Size       = UDim2.new(1,-10,0,22)
    LblV.Position   = UDim2.new(0,12,0,2)
    LblV.TextXAlignment = Enum.TextXAlignment.Left

    local Track = Instance.new("Frame", Row)
    Track.Size            = UDim2.new(1,-24,0,8)
    Track.Position        = UDim2.new(0,12,0,34)
    Track.BackgroundColor3 = G.track
    Track.BorderSizePixel = 0
    Instance.new("UICorner", Track).CornerRadius = UDim.new(1,0)

    local ratio0 = (CFG[cfgKey]-minVal)/(maxVal-minVal)
    local Fill = Instance.new("Frame", Track)
    Fill.Size            = UDim2.new(ratio0,0,1,0)
    Fill.BackgroundColor3 = G.accent
    Fill.BorderSizePixel = 0
    Instance.new("UICorner", Fill).CornerRadius = UDim.new(1,0)

    local Dot = Instance.new("Frame", Track)
    Dot.Size            = UDim2.new(0,16,0,16)
    Dot.Position        = UDim2.new(ratio0,-8,0.5,-8)
    Dot.BackgroundColor3 = G.hi
    Dot.BorderSizePixel = 0
    Instance.new("UICorner", Dot).CornerRadius = UDim.new(1,0)
    local DS = Instance.new("UIStroke", Dot)
    DS.Color = Color3.fromRGB(160,160,255); DS.Thickness = 1.2

    local DA = Instance.new("TextButton", Row)
    DA.Size               = UDim2.new(1,0,1,0)
    DA.BackgroundTransparency = 1
    DA.Text               = ""
    DA.ZIndex             = 10

    local dragging = false
    local function applyX(absX)
        local tX = Track.AbsolutePosition.X
        local tW = Track.AbsoluteSize.X
        if tW <= 0 then return end
        local rel = math.clamp((absX-tX)/tW, 0, 1)
        local val = math.clamp(
            math.round((minVal + rel*(maxVal-minVal))/step)*step,
            minVal, maxVal)
        CFG[cfgKey] = val
        local nr = (val-minVal)/(maxVal-minVal)
        Fill.Size     = UDim2.new(nr,0,1,0)
        Dot.Position  = UDim2.new(nr,-8,0.5,-8)
        LblV.Text     = label .. ":  " .. tostring(val)
        if cb then pcall(cb, val) end
    end

    DA.MouseButton1Down:Connect(function(x) dragging=true; applyX(x) end)
    DA.InputBegan:Connect(function(i)
        if i.UserInputType==Enum.UserInputType.Touch then
            dragging=true; applyX(i.Position.X)
        end
    end)
    UserInputService.InputEnded:Connect(function(i)
        if i.UserInputType==Enum.UserInputType.MouseButton1
        or i.UserInputType==Enum.UserInputType.Touch then
            dragging=false
        end
    end)
    UserInputService.InputChanged:Connect(function(i)
        if not dragging then return end
        if i.UserInputType==Enum.UserInputType.MouseMovement
        or i.UserInputType==Enum.UserInputType.Touch then
            applyX(i.Position.X)
        end
    end)
end

local function MakeButton(parent, label, cb)
    local Btn = Instance.new("TextButton", parent)
    Btn.Text            = label
    Btn.Font            = Enum.Font.GothamBold
    Btn.TextSize        = 12
    Btn.TextColor3      = G.txt
    Btn.BackgroundColor3 = G.btn
    Btn.Size            = UDim2.new(1,0,0,32)
    Btn.AutoButtonColor = false
    Instance.new("UICorner", Btn).CornerRadius = UDim.new(0, 8)
    local S = Instance.new("UIStroke", Btn)
    S.Color = G.accent; S.Thickness = 1
    Btn.MouseButton1Click:Connect(function()
        TweenService:Create(Btn,TweenInfo.new(0.07),{BackgroundColor3=G.accent}):Play()
        task.delay(0.14, function()
            TweenService:Create(Btn,TweenInfo.new(0
