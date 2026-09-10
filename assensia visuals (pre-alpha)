-- ============================================================================
-- [ASSENSIA VISUAL] v6.5 — Pure Obsidian & Modular Weather Engine
-- ============================================================================
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")
local Debris = game:GetService("Debris")
local CoreGui = game:GetService("CoreGui")

local LocalPlayer = Players.LocalPlayer
if not LocalPlayer then
    Players:GetPropertyChangedSignal("LocalPlayer"):Wait()
    LocalPlayer = Players.LocalPlayer
end

local function GetSafeParent()
    local target = nil
    pcall(function()
        if gethui then
            target = gethui()
        elseif CoreGui then
            local probe = Instance.new("Folder")
            probe.Parent = CoreGui
            probe:Destroy()
            target = CoreGui
        end
    end)
    return target or LocalPlayer:WaitForChild("PlayerGui")
end

local function ResolveFont(name, fallback)
    local ok, val = pcall(function() return Enum.Font[name] end)
    if ok and val then return val end
    return fallback
end

local FONT_BOLD = ResolveFont("MontserratBold", Enum.Font.GothamBold)
local FONT_REG  = ResolveFont("MontserratMedium", Enum.Font.GothamMedium)

-- ============================================================================
-- [THEME & ENGINE CONFIG]
-- ============================================================================
local Theme = {
    MainBG     = Color3.fromRGB(10, 10, 13),
    SidebarBG  = Color3.fromRGB(13, 13, 17),
    CardBG     = Color3.fromRGB(17, 17, 22),
    CardHover  = Color3.fromRGB(23, 23, 30),
    Border     = Color3.fromRGB(30, 30, 38),
    BorderSoft = Color3.fromRGB(20, 20, 26),
    Text       = Color3.fromRGB(245, 245, 250),
    TextMuted  = Color3.fromRGB(120, 120, 135),
    ToggleOff  = Color3.fromRGB(26, 26, 33),
    Accent     = Color3.fromRGB(255, 255, 255)
}

local State = {
    CustomSkybox    = false,
    SkyboxId        = "159454299",
    TimeCycle       = false,
    TimeValue       = 14,
    Fog             = false,
    FogDistance     = 120,
    CustomWeather   = false,
    WeatherType     = 1, -- 1: Rain, 2: Sakura, 3: Snow
    WeatherSpeed    = 55,
    WeatherAmount   = 2,
    AngelHalo       = false,
    AuraParticles   = false,
    AuraStyle       = 2, -- 1: Orbs, 2: Bankai, 3: Helix
    ColorCorrection = false,
    BloomEffect     = false,
    AccentHue       = 0.76,
    AccentSat       = 0.0,
    AccentVal       = 1.0
}

local AccentBindings = {}
local function BindAccent(callback)
    table.insert(AccentBindings, callback)
    pcall(callback, Theme.Accent)
end

local function UpdateGlobalColor()
    Theme.Accent = Color3.fromHSV(State.AccentHue, State.AccentSat, State.AccentVal)
    for _, callback in ipairs(AccentBindings) do
        pcall(callback, Theme.Accent)
    end
end

local Connections = {}
local Tabs = {}
local ActiveTab = nil

-- ============================================================================
-- [1. INTRO ANIMATION]
-- ============================================================================
local IntroGui = Instance.new("ScreenGui")
IntroGui.Name = "AssensiaIntro"
IntroGui.Parent = GetSafeParent()
IntroGui.DisplayOrder = 9999
IntroGui.IgnoreGuiInset = true

local IntroBG = Instance.new("Frame", IntroGui)
IntroBG.Size = UDim2.new(1, 0, 1, 0)
IntroBG.BackgroundColor3 = Color3.fromRGB(6, 6, 8)
IntroBG.BorderSizePixel = 0

local UnderLine = Instance.new("Frame", IntroBG)
UnderLine.Size = UDim2.new(0, 0, 0, 2)
UnderLine.Position = UDim2.new(0.5, 0, 0.5, 36)
UnderLine.AnchorPoint = Vector2.new(0.5, 0)
UnderLine.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
UnderLine.BorderSizePixel = 0
UnderLine.ZIndex = 5
Instance.new("UICorner", UnderLine).CornerRadius = UDim.new(1, 0)

local Word = "ASSENSIA"
local Letters = {}
local Spacing = 46
local TotalWidth = (#Word - 1) * Spacing
local StartX = -TotalWidth / 2

for i = 1, #Word do
    local Lbl = Instance.new("TextLabel", IntroBG)
    Lbl.Text = string.sub(Word, i, i)
    Lbl.Font = FONT_BOLD
    Lbl.TextSize = 50
    Lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
    Lbl.BackgroundTransparency = 1
    Lbl.Size = UDim2.new(0, 50, 0, 50)
    Lbl.AnchorPoint = Vector2.new(0.5, 0.5)
    Lbl.Position = UDim2.new(0.5, 0, 0.5, -4)
    Lbl.TextTransparency = 1
    Lbl.ZIndex = 5
    table.insert(Letters, Lbl)
end

for i, Lbl in ipairs(Letters) do
    task.delay(i * 0.06, function()
        Lbl.TextSize = 30
        TweenService:Create(Lbl, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            TextTransparency = 0,
            TextSize = 50
        }):Play()
        TweenService:Create(Lbl, TweenInfo.new(0.65, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {
            Position = UDim2.new(0.5, StartX + (i - 1) * Spacing, 0.5, -4)
        }):Play()
    end)
end

task.delay(#Word * 0.06 + 0.35, function()
    TweenService:Create(UnderLine, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
        Size = UDim2.new(0, TotalWidth + 40, 0, 2)
    }):Play()
end)

task.wait(2.2)

for _, Lbl in ipairs(Letters) do
    TweenService:Create(Lbl, TweenInfo.new(0.28, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {
        TextTransparency = 1,
        TextSize = 60
    }):Play()
end
TweenService:Create(UnderLine, TweenInfo.new(0.25), {Size = UDim2.new(0, 0, 0, 2)}):Play()

local BgFade = TweenService:Create(IntroBG, TweenInfo.new(0.45, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {BackgroundTransparency = 1})
BgFade.Completed:Connect(function()
    IntroGui:Destroy()
end)
BgFade:Play()
task.wait(0.3)

-- ============================================================================
-- [2. MAIN INTERFACE FRAME]
-- ============================================================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "AssensiaVisual_Core"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = GetSafeParent()

local MainWrapper = Instance.new("Frame", ScreenGui)
MainWrapper.Size = UDim2.new(0, 580, 0, 420)
MainWrapper.Position = UDim2.new(0.5, 0, 0.5, 0)
MainWrapper.AnchorPoint = Vector2.new(0.5, 0.5)
MainWrapper.BackgroundColor3 = Theme.MainBG
MainWrapper.BorderSizePixel = 0
MainWrapper.ClipsDescendants = true
Instance.new("UICorner", MainWrapper).CornerRadius = UDim.new(0, 8)

local MainScale = Instance.new("UIScale", MainWrapper)
MainScale.Scale = 0.95

local WindowBorder = Instance.new("UIStroke", MainWrapper)
WindowBorder.Thickness = 1
WindowBorder.Color = Theme.Border
WindowBorder.Transparency = 0

local Sidebar = Instance.new("Frame", MainWrapper)
Sidebar.Size = UDim2.new(0, 155, 1, 0)
Sidebar.BackgroundColor3 = Theme.SidebarBG
Sidebar.BorderSizePixel = 0
Sidebar.ZIndex = 2

local SidebarDivider = Instance.new("Frame", MainWrapper)
SidebarDivider.Size = UDim2.new(0, 1, 1, 0)
SidebarDivider.Position = UDim2.new(0, 155, 0, 0)
SidebarDivider.BackgroundColor3 = Theme.BorderSoft
SidebarDivider.BorderSizePixel = 0
SidebarDivider.ZIndex = 3

local HeaderBox = Instance.new("Frame", Sidebar)
HeaderBox.Size = UDim2.new(1, 0, 0, 52)
HeaderBox.BackgroundTransparency = 1
HeaderBox.ZIndex = 3

local Title = Instance.new("TextLabel", HeaderBox)
Title.Size = UDim2.new(1, -16, 1, 0)
Title.Position = UDim2.new(0, 16, 0, 0)
Title.BackgroundTransparency = 1
Title.Font = FONT_BOLD
Title.Text = "ASSENSIA"
Title.TextSize = 18
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.ZIndex = 3
BindAccent(function(col) Title.TextColor3 = col end)

local TabListFrame = Instance.new("Frame", Sidebar)
TabListFrame.Size = UDim2.new(1, -16, 1, -64)
TabListFrame.Position = UDim2.new(0, 8, 0, 56)
TabListFrame.BackgroundTransparency = 1
TabListFrame.ZIndex = 3

local TabListLayout = Instance.new("UIListLayout", TabListFrame)
TabListLayout.Padding = UDim.new(0, 4)
TabListLayout.SortOrder = Enum.SortOrder.LayoutOrder

local ContentViewport = Instance.new("Frame", MainWrapper)
ContentViewport.Size = UDim2.new(1, -156, 1, 0)
ContentViewport.Position = UDim2.new(0, 156, 0, 0)
ContentViewport.BackgroundTransparency = 1
ContentViewport.ZIndex = 2

-- ============================================================================
-- [3. DRAGGING & MENU ANIMATIONS]
-- ============================================================================
local isDragging, dragStart, startPos
HeaderBox.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        isDragging = true
        dragStart = input.Position
        startPos = MainWrapper.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if isDragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        MainWrapper.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

local function EndDrag() isDragging = false end
HeaderBox.InputEnded:Connect(EndDrag)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        EndDrag()
    end
end)

local MenuOpen = false
local MenuAnimating = false

local function SetMenuVisible(visible)
    if MenuAnimating then return end
    MenuAnimating = true
    MenuOpen = visible

    if MenuOpen then
        MainWrapper.Visible = true
        MainWrapper.Position = UDim2.new(0.5, 0, 0.5, 10)
        MainScale.Scale = 0.95
        TweenService:Create(MainScale, TweenInfo.new(0.28, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Scale = 1}):Play()
        local t = TweenService:Create(MainWrapper, TweenInfo.new(0.28, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            Position = UDim2.new(0.5, 0, 0.5, 0)
        })
        t.Completed:Connect(function() MenuAnimating = false end)
        t:Play()
    else
        TweenService:Create(MainScale, TweenInfo.new(0.2, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {Scale = 0.95}):Play()
        local t = TweenService:Create(MainWrapper, TweenInfo.new(0.2, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {
            Position = UDim2.new(0.5, 0, 0.5, 10)
        })
        t.Completed:Connect(function()
            MainWrapper.Visible = false
            MenuAnimating = false
        end)
        t:Play()
    end
end

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.Insert then
        SetMenuVisible(not MenuOpen)
    end
end)

-- ============================================================================
-- [4. CUSTOM WEATHER ENGINE (RAIN / SAKURA / SNOW)]
-- ============================================================================
local function ApplyWeather()
    if Connections["WeatherLoop"] then
        Connections["WeatherLoop"]:Disconnect()
        Connections["WeatherLoop"] = nil
    end
    if not State.CustomWeather then return end

    Connections["WeatherLoop"] = RunService.Heartbeat:Connect(function()
        local cam = Workspace.CurrentCamera
        if not cam then return end

        for _ = 1, State.WeatherAmount do
            local drop = Instance.new("Part", Workspace)
            drop.CanCollide = false
            drop.Anchored = false
            drop.CastShadow = false

            local spawnOffset = Vector3.new(math.random(-80, 80), math.random(40, 75), math.random(-80, 80))
            drop.Position = cam.CFrame.Position + spawnOffset
            local speed = State.WeatherSpeed

            if State.WeatherType == 1 then -- Rain
                drop.Size = Vector3.new(0.06, 2.4, 0.06)
                drop.Color = Theme.Accent
                drop.Material = Enum.Material.Neon
                drop.Transparency = 0.25

                local vel = Instance.new("BodyVelocity", drop)
                vel.Velocity = Vector3.new(math.random(-2, 2), -speed, math.random(-2, 2))
                vel.MaxForce = Vector3.new(9e9, 9e9, 9e9)

            elseif State.WeatherType == 2 then -- Sakura
                drop.Size = Vector3.new(0.35, 0.05, 0.35)
                drop.Shape = Enum.PartType.Block
                drop.Color = Color3.fromRGB(255, 182, 193):Lerp(Theme.Accent, 0.25)
                drop.Material = Enum.Material.SmoothPlastic
                drop.Transparency = 0.15

                local vel = Instance.new("BodyVelocity", drop)
                local swayX = math.sin(tick() * 2 + math.random()) * 8
                local swayZ = math.cos(tick() * 2 + math.random()) * 8
                vel.Velocity = Vector3.new(swayX, -math.clamp(speed * 0.35, 8, 55), swayZ)
                vel.MaxForce = Vector3.new(9e9, 9e9, 9e9)

                local rot = Instance.new("BodyAngularVelocity", drop)
                rot.AngularVelocity = Vector3.new(math.random(-4, 4), math.random(-4, 4), math.random(-4, 4))
                rot.MaxTorque = Vector3.new(9e9, 9e9, 9e9)

            elseif State.WeatherType == 3 then -- Snow
                drop.Size = Vector3.new(0.24, 0.24, 0.24)
                drop.Shape = Enum.PartType.Ball
                drop.Color = Color3.fromRGB(250, 252, 255)
                drop.Material = Enum.Material.Neon
                drop.Transparency = 0.1

                local vel = Instance.new("BodyVelocity", drop)
                local driftX = math.sin(tick() * 1.5 + math.random()) * 4
                local driftZ = math.cos(tick() * 1.5 + math.random()) * 4
                vel.Velocity = Vector3.new(driftX, -math.clamp(speed * 0.25, 6, 40), driftZ)
                vel.MaxForce = Vector3.new(9e9, 9e9, 9e9)
            end

            Debris:AddItem(drop, 3.5)
        end
    end)
end

-- ============================================================================
-- [5. ANGEL HALO ENGINE]
-- ============================================================================
local haloParts = {}

local function ClearHalo()
    for _, p in ipairs(haloParts) do
        if p and p.Parent then p:Destroy() end
    end
    table.clear(haloParts)
    if Connections["HaloLoop"] then
        Connections["HaloLoop"]:Disconnect()
        Connections["HaloLoop"] = nil
    end
end

BindAccent(function(col)
    for _, p in ipairs(haloParts) do
        if p and p.Parent then
            p.Color = col
        end
    end
end)

local function ApplyAngelHalo(enabled)
    ClearHalo()
    State.AngelHalo = enabled
    if not enabled then return end

    local segCount = 16
    for _ = 1, segCount do
        local p = Instance.new("Part", Workspace)
        p.Name = "AngelHaloSegment"
        p.Material = Enum.Material.Neon
        p.Color = Theme.Accent
        p.Size = Vector3.new(0.08, 0.08, 0.28)
        p.CanCollide = false
        p.Anchored = true
        p.CastShadow = false
        p.Transparency = 0.05
        table.insert(haloParts, p)
    end

    Connections["HaloLoop"] = RunService.Heartbeat:Connect(function()
        local char = LocalPlayer.Character
        local head = char and char:FindFirstChild("Head")
        if not head then return end

        local t = tick()
        local hover = math.sin(t * 2.5) * 0.1
        local centerPos = head.Position + Vector3.new(0, 1.8 + hover, 0)
        local baseCF = CFrame.new(centerPos) * CFrame.Angles(math.rad(8), t * 1.4, 0)
        local radius = 1.1

        for i, seg in ipairs(haloParts) do
            local angle = ((math.pi * 2) / #haloParts) * i
            local offset = Vector3.new(math.cos(angle) * radius, 0, math.sin(angle) * radius)
            local lookTarget = baseCF * CFrame.new(Vector3.new(math.cos(angle + 0.1) * radius, 0, math.sin(angle + 0.1) * radius))
            seg.CFrame = CFrame.new((baseCF * CFrame.new(offset)).Position, lookTarget.Position)
        end
    end)
end

-- ============================================================================
-- [6. AURA ENGINE (BANKAI: 4 KATANAS)]
-- ============================================================================
local auraObjects = {}

local function ClearAura()
    for _, item in ipairs(auraObjects) do
        if typeof(item) == "Instance" then
            item:Destroy()
        elseif type(item) == "table" then
            if item.Model and item.Model.Parent then item.Model:Destroy() end
        end
    end
    table.clear(auraObjects)
    if Connections["AuraLoop"] then
        Connections["AuraLoop"]:Disconnect()
        Connections["AuraLoop"] = nil
    end
end

local function RebuildAura()
    ClearAura()
    if not State.AuraParticles then return end

    if State.AuraStyle == 1 then -- Orbs
        for _ = 1, 8 do
            local p = Instance.new("Part", Workspace)
            p.Material = Enum.Material.Neon
            p.CanCollide = false
            p.Anchored = true
            p.CastShadow = false
            p.Size = Vector3.new(0.35, 0.35, 0.35)
            p.Shape = Enum.PartType.Ball
            p.Transparency = 0.15
            p.Color = Theme.Accent

            local a0 = Instance.new("Attachment", p)
            local a1 = Instance.new("Attachment", p)
            a0.Position = Vector3.new(0, 0.15, 0)
            a1.Position = Vector3.new(0, -0.15, 0)
            local tr = Instance.new("Trail", p)
            tr.Attachment0 = a0
            tr.Attachment1 = a1
            tr.Lifetime = 0.35
            tr.Color = ColorSequence.new(Theme.Accent)
            BindAccent(function(col)
                tr.Color = ColorSequence.new(col)
                p.Color = col
            end)
            table.insert(auraObjects, p)
        end

    elseif State.AuraStyle == 2 then -- Bankai (4 Dynamic Katanas)
        for i = 1, 4 do
            local model = Instance.new("Model", Workspace)

            local blade = Instance.new("Part", model)
            blade.Material = Enum.Material.Neon
            blade.Size = Vector3.new(0.06, 2.4, 0.18)
            blade.CanCollide = false
            blade.Anchored = true
            blade.CastShadow = false
            blade.Color = Theme.Accent

            local tsuba = Instance.new("Part", model)
            tsuba.Material = Enum.Material.SmoothPlastic
            tsuba.Size = Vector3.new(0.22, 0.05, 0.32)
            tsuba.CanCollide = false
            tsuba.Anchored = true
            tsuba.Color = Color3.fromRGB(20, 20, 24)

            local handle = Instance.new("Part", model)
            handle.Material = Enum.Material.SmoothPlastic
            handle.Size = Vector3.new(0.1, 0.7, 0.12)
            handle.CanCollide = false
            handle.Anchored = true
            handle.Color = Color3.fromRGB(30, 30, 36)

            local a0 = Instance.new("Attachment", blade)
            local a1 = Instance.new("Attachment", blade)
            a0.Position = Vector3.new(0, 1.2, 0)
            a1.Position = Vector3.new(0, -1.2, 0)
            local slashTrail = Instance.new("Trail", blade)
            slashTrail.Attachment0 = a0
            slashTrail.Attachment1 = a1
            slashTrail.Lifetime = 0.3
            slashTrail.Color = ColorSequence.new(Theme.Accent)

            BindAccent(function(col)
                blade.Color = col
                slashTrail.Color = ColorSequence.new(col)
            end)

            table.insert(auraObjects, {
                Model       = model,
                Blade       = blade,
                Tsuba       = tsuba,
                Handle      = handle,
                Speed       = 1.8 + (i * 0.45),
                RadiusX     = 3.4 + ((i * 1.7) % 1.5),
                RadiusZ     = 3.4 + ((i * 2.3) % 1.5),
                HeightPhase = i * 1.57,
                WaveSpeed   = 2.2 + (i * 0.3)
            })
        end

    elseif State.AuraStyle == 3 then -- Helix Shards
        for _ = 1, 8 do
            local shard = Instance.new("Part", Workspace)
            shard.Material = Enum.Material.Neon
            shard.Size = Vector3.new(0.2, 0.6, 0.2)
            shard.CanCollide = false
            shard.Anchored = true
            shard.CastShadow = false
            shard.Color = Theme.Accent
            shard.Transparency = 0.2

            local a0 = Instance.new("Attachment", shard)
            local a1 = Instance.new("Attachment", shard)
            a0.Position = Vector3.new(0, 0.3, 0)
            a1.Position = Vector3.new(0, -0.3, 0)
            local tr = Instance.new("Trail", shard)
            tr.Attachment0 = a0
            tr.Attachment1 = a1
            tr.Lifetime = 0.35
            tr.Color = ColorSequence.new(Theme.Accent)

            BindAccent(function(col)
                shard.Color = col
                tr.Color = ColorSequence.new(col)
            end)

            table.insert(auraObjects, shard)
        end
    end

    Connections["AuraLoop"] = RunService.Heartbeat:Connect(function()
        local char = LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not root then return end
        local t = tick()

        if State.AuraStyle == 1 then
            for i, p in ipairs(auraObjects) do
                local angle = ((math.pi * 2) / #auraObjects) * i
                local x = math.sin(t * 2.2 + angle) * 3.4
                local z = math.cos(t * 2.2 + angle) * 3.4
                local y = math.sin((t * 4.4) + angle) * 1.5
                p.CFrame = root.CFrame * CFrame.new(x, y, z)
            end

        elseif State.AuraStyle == 2 then
            for _, sw in ipairs(auraObjects) do
                local theta = t * sw.Speed + sw.HeightPhase
                local x = math.sin(theta) * sw.RadiusX + math.cos(t * 0.8) * 0.5
                local z = math.cos(theta) * sw.RadiusZ + math.sin(t * 0.8) * 0.5
                local y = math.sin(t * sw.WaveSpeed + sw.HeightPhase) * 1.6 + math.cos(theta * 0.5) * 0.4

                local dt = 0.05
                local nextTheta = (t + dt) * sw.Speed + sw.HeightPhase
                local nx = math.sin(nextTheta) * sw.RadiusX + math.cos((t + dt) * 0.8) * 0.5
                local nz = math.cos(nextTheta) * sw.RadiusZ + math.sin((t + dt) * 0.8) * 0.5
                local ny = math.sin((t + dt) * sw.WaveSpeed + sw.HeightPhase) * 1.6 + math.cos(nextTheta * 0.5) * 0.4

                local curWorld  = (root.CFrame * CFrame.new(x, y, z)).Position
                local nextWorld = (root.CFrame * CFrame.new(nx, ny, nz)).Position
                local dir = (nextWorld - curWorld).Unit

                local baseOrientation = CFrame.lookAt(curWorld, curWorld + dir) * CFrame.Angles(math.rad(-90), 0, math.rad(theta * 50))
                sw.Blade.CFrame  = baseOrientation * CFrame.new(0, 0.4, 0)
                sw.Tsuba.CFrame  = baseOrientation * CFrame.new(0, -0.85, 0)
                sw.Handle.CFrame = baseOrientation * CFrame.new(0, -1.22, 0)
            end

        elseif State.AuraStyle == 3 then
            for i, shard in ipairs(auraObjects) do
                local angle = ((math.pi * 2) / 8) * i + (t * 2.2)
                local radius = 3.2
                local height = math.sin(t * 2 + angle) * 1.8
                shard.CFrame = root.CFrame * CFrame.new(math.sin(angle) * radius, height, math.cos(angle) * radius)
                    * CFrame.Angles(t * 3, t * 2, 0)
            end
        end
    end)
end

-- ============================================================================
-- [7. ENVIRONMENT LOGIC]
-- ============================================================================
local function ApplySkybox(enabled)
    for _, child in ipairs(Lighting:GetChildren()) do
        if child:IsA("Sky") and child.Name == "AssensiaSky" then child:Destroy() end
    end
    if enabled then
        local rawId = State.SkyboxId or "159454299"
        local cleanId = string.match(rawId, "%d+") or "159454299"
        local assetUri = "rbxassetid://" .. cleanId

        local sky = Instance.new("Sky")
        sky.Name = "AssensiaSky"
        sky.SkyboxBk, sky.SkyboxDn, sky.SkyboxFt = assetUri, assetUri, assetUri
        sky.SkyboxLf, sky.SkyboxRt, sky.SkyboxUp = assetUri, assetUri, assetUri
        sky.CelestialBodiesShown = true
        sky.Parent = Lighting
    end
end

local function ApplyTimeCycle(enabled)
    if Connections["TimeLoop"] then
        Connections["TimeLoop"]:Disconnect()
        Connections["TimeLoop"] = nil
    end
    if enabled then
        Connections["TimeLoop"] = RunService.Heartbeat:Connect(function()
            pcall(function() Lighting.ClockTime = State.TimeValue end)
        end)
    else
        Lighting.ClockTime = 14
    end
end

local function ApplyFog()
    if Connections["FogLoop"] then
        Connections["FogLoop"]:Disconnect()
        Connections["FogLoop"] = nil
    end
    if State.Fog then
        Connections["FogLoop"] = RunService.Heartbeat:Connect(function()
            pcall(function()
                Lighting.FogStart = 0
                Lighting.FogEnd = State.FogDistance
                Lighting.FogColor = Color3.new(Theme.Accent.R * 0.35, Theme.Accent.G * 0.35, Theme.Accent.B * 0.35)
            end)
        end)
    else
        Lighting.FogStart = 0
        Lighting.FogEnd = 100000
        Lighting.FogColor = Color3.fromRGB(192, 192, 192)
    end
end

local function ApplyColorCorrection(enabled)
    local cc = Lighting:FindFirstChild("AssensiaCC")
    if enabled then
        if not cc then
            cc = Instance.new("ColorCorrectionEffect", Lighting)
            cc.Name = "AssensiaCC"
        end
        cc.Saturation = 0.25
        cc.Contrast = 0.12
        cc.TintColor = Color3.new(Theme.Accent.R * 0.75 + 0.25, Theme.Accent.G * 0.75 + 0.25, Theme.Accent.B * 0.75 + 0.25)
    elseif cc then
        cc:Destroy()
    end
end

local function ApplyBloom(enabled)
    local bloom = Lighting:FindFirstChild("AssensiaBloom")
    if enabled then
        if not bloom then
            bloom = Instance.new("BloomEffect", Lighting)
            bloom.Name = "AssensiaBloom"
        end
        bloom.Intensity = 0.8
        bloom.Size = 22
        bloom.Threshold = 0.85
    elseif bloom then
        bloom:Destroy()
    end
end

BindAccent(function()
    if State.Fog then ApplyFog() end
    if State.ColorCorrection then ApplyColorCorrection(true) end
end)

-- ============================================================================
-- [8. UI FACTORY HELPERS]
-- ============================================================================
local function SwitchTab(targetPage, targetBtn)
    for _, item in ipairs(Tabs) do
        item.Page.Visible = false
        TweenService:Create(item.Btn, TweenInfo.new(0.18), {
            BackgroundTransparency = 1,
            TextColor3 = Theme.TextMuted
        }):Play()
        local indicator = item.Btn:FindFirstChild("ActiveIndicator")
        if indicator then indicator.Visible = false end
    end

    targetPage.Visible = true
    ActiveTab = targetPage
    TweenService:Create(targetBtn, TweenInfo.new(0.18), {
        BackgroundTransparency = 0.9,
        TextColor3 = Theme.Text
    }):Play()

    local activeInd = targetBtn:FindFirstChild("ActiveIndicator")
    if activeInd then activeInd.Visible = true end

    task.defer(function()
        local layout = targetPage:FindFirstChildOfClass("UIListLayout")
        local pad = targetPage:FindFirstChildOfClass("UIPadding")
        if layout and pad then
            targetPage.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + pad.PaddingTop.Offset + pad.PaddingBottom.Offset)
        end
    end)
end

local function CreateTab(title)
    local TabBtn = Instance.new("TextButton", TabListFrame)
    TabBtn.Size = UDim2.new(1, 0, 0, 32)
    TabBtn.BackgroundColor3 = Theme.Accent
    TabBtn.BackgroundTransparency = 1
    TabBtn.Font = FONT_REG
    TabBtn.Text = "   " .. title
    TabBtn.TextColor3 = Theme.TextMuted
    TabBtn.TextSize = 13
    TabBtn.TextXAlignment = Enum.TextXAlignment.Left
    TabBtn.AutoButtonColor = false
    Instance.new("UICorner", TabBtn).CornerRadius = UDim.new(0, 5)

    local Indicator = Instance.new("Frame", TabBtn)
    Indicator.Name = "ActiveIndicator"
    Indicator.Size = UDim2.new(0, 3, 0, 14)
    Indicator.Position = UDim2.new(0, 2, 0.5, -7)
    Indicator.BorderSizePixel = 0
    Indicator.Visible = false
    Instance.new("UICorner", Indicator).CornerRadius = UDim.new(1, 0)
    BindAccent(function(col)
        Indicator.BackgroundColor3 = col
        TabBtn.BackgroundColor3 = col
    end)

    local Page = Instance.new("ScrollingFrame", ContentViewport)
    Page.Size = UDim2.new(1, 0, 1, 0)
    Page.BackgroundTransparency = 1
    Page.BorderSizePixel = 0
    Page.ScrollBarThickness = 2
    Page.Visible = false
    Page.AutomaticCanvasSize = Enum.AutomaticSize.None
    Page.CanvasSize = UDim2.new(0, 0, 0, 0)
    BindAccent(function(col) Page.ScrollBarImageColor3 = col end)

    local Pad = Instance.new("UIPadding", Page)
    Pad.PaddingTop = UDim.new(0, 12)
    Pad.PaddingBottom = UDim.new(0, 12)
    Pad.PaddingLeft = UDim.new(0, 14)
    Pad.PaddingRight = UDim.new(0, 14)

    local ListLayout = Instance.new("UIListLayout", Page)
    ListLayout.Padding = UDim.new(0, 6)
    ListLayout.SortOrder = Enum.SortOrder.LayoutOrder

    local function UpdateCanvas()
        Page.CanvasSize = UDim2.new(0, 0, 0, ListLayout.AbsoluteContentSize.Y + Pad.PaddingTop.Offset + Pad.PaddingBottom.Offset)
    end
    ListLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(UpdateCanvas)

    TabBtn.MouseButton1Click:Connect(function()
        SwitchTab(Page, TabBtn)
    end)

    table.insert(Tabs, {Btn = TabBtn, Page = Page})
    return Page
end

local function CreateToggle(parent, title, stateKey, callback)
    local Card = Instance.new("TextButton", parent)
    Card.Size = UDim2.new(1, 0, 0, 44)
    Card.BackgroundColor3 = Theme.CardBG
    Card.Text = ""
    Card.AutoButtonColor = false
    Instance.new("UICorner", Card).CornerRadius = UDim.new(0, 6)
    local Stroke = Instance.new("UIStroke", Card)
    Stroke.Color = Theme.Border
    Stroke.Thickness = 1

    local NameLbl = Instance.new("TextLabel", Card)
    NameLbl.Size = UDim2.new(1, -70, 1, 0)
    NameLbl.Position = UDim2.new(0, 14, 0, 0)
    NameLbl.BackgroundTransparency = 1
    NameLbl.Font = FONT_BOLD
    NameLbl.Text = title
    NameLbl.TextColor3 = Theme.Text
    NameLbl.TextSize = 13
    NameLbl.TextXAlignment = Enum.TextXAlignment.Left

    local Switch = Instance.new("Frame", Card)
    Switch.Size = UDim2.new(0, 36, 0, 18)
    Switch.Position = UDim2.new(1, -48, 0.5, -9)
    Switch.BackgroundColor3 = State[stateKey] and Theme.Accent or Theme.ToggleOff
    Instance.new("UICorner", Switch).CornerRadius = UDim.new(1, 0)

    local Ball = Instance.new("Frame", Switch)
    Ball.Size = UDim2.new(0, 14, 0, 14)
    Ball.Position = State[stateKey] and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7)
    Ball.BackgroundColor3 = Color3.fromRGB(240, 240, 240)
    Instance.new("UICorner", Ball).CornerRadius = UDim.new(1, 0)

    BindAccent(function(col)
        if State[stateKey] then Switch.BackgroundColor3 = col end
    end)

    Card.MouseEnter:Connect(function()
        TweenService:Create(Card, TweenInfo.new(0.18), {BackgroundColor3 = Theme.CardHover}):Play()
    end)
    Card.MouseLeave:Connect(function()
        TweenService:Create(Card, TweenInfo.new(0.18), {BackgroundColor3 = Theme.CardBG}):Play()
    end)

    Card.MouseButton1Click:Connect(function()
        State[stateKey] = not State[stateKey]
        local isEnabled = State[stateKey]

        TweenService:Create(Switch, TweenInfo.new(0.2, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            BackgroundColor3 = isEnabled and Theme.Accent or Theme.ToggleOff
        }):Play()
        TweenService:Create(Ball, TweenInfo.new(0.2, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            Position = isEnabled and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7)
        }):Play()

        if callback then callback(isEnabled) end
    end)
end

local function CreateSkyboxCard(parent)
    local Card = Instance.new("Frame", parent)
    Card.Size = UDim2.new(1, 0, 0, 72)
    Card.BackgroundColor3 = Theme.CardBG
    Instance.new("UICorner", Card).CornerRadius = UDim.new(0, 6)
    local Stroke = Instance.new("UIStroke", Card)
    Stroke.Color = Theme.Border
    Stroke.Thickness = 1

    local NameLbl = Instance.new("TextLabel", Card)
    NameLbl.Size = UDim2.new(1, -70, 0, 32)
    NameLbl.Position = UDim2.new(0, 14, 0, 4)
    NameLbl.BackgroundTransparency = 1
    NameLbl.Font = FONT_BOLD
    NameLbl.Text = "Custom Skybox"
    NameLbl.TextColor3 = Theme.Text
    NameLbl.TextSize = 13
    NameLbl.TextXAlignment = Enum.TextXAlignment.Left

    local Switch = Instance.new("TextButton", Card)
    Switch.Size = UDim2.new(0, 36, 0, 18)
    Switch.Position = UDim2.new(1, -48, 0, 11)
    Switch.BackgroundColor3 = State.CustomSkybox and Theme.Accent or Theme.ToggleOff
    Switch.Text = ""
    Switch.AutoButtonColor = false
    Instance.new("UICorner", Switch).CornerRadius = UDim.new(1, 0)

    local Ball = Instance.new("Frame", Switch)
    Ball.Size = UDim2.new(0, 14, 0, 14)
    Ball.Position = State.CustomSkybox and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7)
    Ball.BackgroundColor3 = Color3.fromRGB(240, 240, 240)
    Instance.new("UICorner", Ball).CornerRadius = UDim.new(1, 0)

    BindAccent(function(col)
        if State.CustomSkybox then Switch.BackgroundColor3 = col end
    end)

    Switch.MouseButton1Click:Connect(function()
        State.CustomSkybox = not State.CustomSkybox
        TweenService:Create(Switch, TweenInfo.new(0.2), {BackgroundColor3 = State.CustomSkybox and Theme.Accent or Theme.ToggleOff}):Play()
        TweenService:Create(Ball, TweenInfo.new(0.2), {Position = State.CustomSkybox and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7)}):Play()
        ApplySkybox(State.CustomSkybox)
    end)

    local InputBox = Instance.new("TextBox", Card)
    InputBox.Size = UDim2.new(1, -28, 0, 24)
    InputBox.Position = UDim2.new(0, 14, 0, 38)
    InputBox.BackgroundColor3 = Theme.SidebarBG
    InputBox.Font = FONT_REG
    InputBox.Text = State.SkyboxId
    InputBox.PlaceholderText = "ID текстуры скайбокса"
    InputBox.TextColor3 = Theme.Text
    InputBox.PlaceholderColor3 = Theme.TextMuted
    InputBox.TextSize = 11
    InputBox.ClearTextOnFocus = false
    Instance.new("UICorner", InputBox).CornerRadius = UDim.new(0, 4)
    local InpStroke = Instance.new("UIStroke", InputBox)
    InpStroke.Color = Theme.BorderSoft

    InputBox.FocusLost:Connect(function()
        State.SkyboxId = InputBox.Text
        if State.CustomSkybox then ApplySkybox(true) end
    end)
end

local function CreateSlider(parent, title, minVal, maxVal, defaultVal, isPercent, stateKey, callback)
    local Card = Instance.new("Frame", parent)
    Card.Size = UDim2.new(1, 0, 0, 56)
    Card.BackgroundColor3 = Theme.CardBG
    Instance.new("UICorner", Card).CornerRadius = UDim.new(0, 6)
    local Stroke = Instance.new("UIStroke", Card)
    Stroke.Color = Theme.Border
    Stroke.Thickness = 1

    local NameLbl = Instance.new("TextLabel", Card)
    NameLbl.Size = UDim2.new(1, -70, 0, 20)
    NameLbl.Position = UDim2.new(0, 14, 0, 8)
    NameLbl.BackgroundTransparency = 1
    NameLbl.Font = FONT_BOLD
    NameLbl.Text = title
    NameLbl.TextColor3 = Theme.Text
    NameLbl.TextSize = 13
    NameLbl.TextXAlignment = Enum.TextXAlignment.Left

    local ValueLbl = Instance.new("TextLabel", Card)
    ValueLbl.Size = UDim2.new(0, 48, 0, 20)
    ValueLbl.Position = UDim2.new(1, -56, 0, 8)
    ValueLbl.BackgroundTransparency = 1
    ValueLbl.Font = FONT_BOLD
    ValueLbl.Text = isPercent and string.format("%d%%", math.floor(defaultVal * 100)) or tostring(defaultVal)
    ValueLbl.TextSize = 12
    ValueLbl.TextXAlignment = Enum.TextXAlignment.Right
    BindAccent(function(col) ValueLbl.TextColor3 = col end)

    local Track = Instance.new("TextButton", Card)
    Track.Size = UDim2.new(1, -28, 0, 6)
    Track.Position = UDim2.new(0, 14, 0, 36)
    Track.BackgroundColor3 = Theme.SidebarBG
    Track.Text = ""
    Track.AutoButtonColor = false
    Instance.new("UICorner", Track).CornerRadius = UDim.new(1, 0)

    local Progress = Instance.new("Frame", Track)
    local initialRatio = math.clamp((defaultVal - minVal) / (maxVal - minVal), 0, 1)
    Progress.Size = UDim2.new(initialRatio, 0, 1, 0)
    Progress.BackgroundColor3 = Theme.Accent
    Instance.new("UICorner", Progress).CornerRadius = UDim.new(1, 0)

    BindAccent(function(col) Progress.BackgroundColor3 = col end)

    local isDraggingSlider = false
    local function UpdateSlide(xPos)
        local ratio = math.clamp((xPos - Track.AbsolutePosition.X) / Track.AbsoluteSize.X, 0, 1)
        local val = minVal + ratio * (maxVal - minVal)
        if not isPercent then val = math.floor(val + 0.5) end
        State[stateKey] = val

        ValueLbl.Text = isPercent and string.format("%d%%", math.floor(val * 100)) or tostring(val)
        Progress.Size = UDim2.new(ratio, 0, 1, 0)
        if callback then callback(val) end
    end

    Track.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            isDraggingSlider = true
            UpdateSlide(input.Position.X)
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if isDraggingSlider and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            UpdateSlide(input.Position.X)
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            isDraggingSlider = false
        end
    end)
end

local function CreateSelector(parent, title, options, stateKey, callback)
    local Card = Instance.new("TextButton", parent)
    Card.Size = UDim2.new(1, 0, 0, 44)
    Card.BackgroundColor3 = Theme.CardBG
    Card.Text = ""
    Card.AutoButtonColor = false
    Instance.new("UICorner", Card).CornerRadius = UDim.new(0, 6)
    local Stroke = Instance.new("UIStroke", Card)
    Stroke.Color = Theme.Border
    Stroke.Thickness = 1

    local NameLbl = Instance.new("TextLabel", Card)
    NameLbl.Size = UDim2.new(1, -135, 1, 0)
    NameLbl.Position = UDim2.new(0, 14, 0, 0)
    NameLbl.BackgroundTransparency = 1
    NameLbl.Font = FONT_BOLD
    NameLbl.Text = title
    NameLbl.TextColor3 = Theme.Text
    NameLbl.TextSize = 13
    NameLbl.TextXAlignment = Enum.TextXAlignment.Left

    local Badge = Instance.new("Frame", Card)
    Badge.Size = UDim2.new(0, 110, 0, 24)
    Badge.Position = UDim2.new(1, -122, 0.5, -12)
    Badge.BackgroundColor3 = Theme.SidebarBG
    Instance.new("UICorner", Badge).CornerRadius = UDim.new(0, 5)

    local BadgeText = Instance.new("TextLabel", Badge)
    BadgeText.Size = UDim2.new(1, 0, 1, 0)
    BadgeText.BackgroundTransparency = 1
    BadgeText.Font = FONT_BOLD
    BadgeText.Text = options[State[stateKey]] or options[1]
    BadgeText.TextSize = 11
    BindAccent(function(col) BadgeText.TextColor3 = col end)

    Card.MouseEnter:Connect(function()
        TweenService:Create(Card, TweenInfo.new(0.18), {BackgroundColor3 = Theme.CardHover}):Play()
    end)
    Card.MouseLeave:Connect(function()
        TweenService:Create(Card, TweenInfo.new(0.18), {BackgroundColor3 = Theme.CardBG}):Play()
    end)

    Card.MouseButton1Click:Connect(function()
        State[stateKey] = (State[stateKey] % #options) + 1
        BadgeText.Text = options[State[stateKey]]
        if callback then callback(State[stateKey]) end
    end)
end

-- ============================================================================
-- [9. TAB POPULATION]
-- ============================================================================
local TabWorld   = CreateTab("World")
local TabVisuals = CreateTab("Visuals")
local TabTheme   = CreateTab("Theme")
local TabExit    = CreateTab("Settings")

-- TAB: WORLD
CreateSkyboxCard(TabWorld)
CreateToggle(TabWorld, "Time Override", "TimeCycle", ApplyTimeCycle)
CreateSlider(TabWorld, "Clock Time", 0, 24, 14, false, "TimeValue", function(val)
    if State.TimeCycle then Lighting.ClockTime = val end
end)
CreateToggle(TabWorld, "Environment Fog", "Fog", ApplyFog)
CreateSlider(TabWorld, "Fog Distance", 20, 800, 120, false, "FogDistance", ApplyFog)

-- CUSTOM WEATHER SECTION IN WORLD
CreateToggle(TabWorld, "Custom Weather", "CustomWeather", ApplyWeather)
CreateSelector(TabWorld, "Weather Type", {"Rain", "Sakura", "Snow"}, "WeatherType", function()
    if State.CustomWeather then ApplyWeather() end
end)
CreateSlider(TabWorld, "Fall Speed", 15, 140, 55, false, "WeatherSpeed", function() end)
CreateSlider(TabWorld, "Weather Amount", 1, 6, 2, false, "WeatherAmount", function() end)

-- TAB: VISUALS
CreateToggle(TabVisuals, "Angel Halo", "AngelHalo", ApplyAngelHalo)
CreateToggle(TabVisuals, "Aura Particles", "AuraParticles", function()
    RebuildAura()
end)
CreateSelector(TabVisuals, "Aura Style", {"Orbs", "Bankai", "Helix"}, "AuraStyle", function()
    RebuildAura()
end)
CreateToggle(TabVisuals, "Color Correction", "ColorCorrection", ApplyColorCorrection)
CreateToggle(TabVisuals, "Bloom Effect", "BloomEffect", ApplyBloom)

-- TAB: THEME
CreateSlider(TabTheme, "Hue", 0, 1, State.AccentHue, true, "AccentHue", function(val)
    State.AccentHue = val
    UpdateGlobalColor()
end)
CreateSlider(TabTheme, "Saturation", 0, 1, State.AccentSat, true, "AccentSat", function(val)
    State.AccentSat = val
    UpdateGlobalColor()
end)
CreateSlider(TabTheme, "Brightness", 0, 1, State.AccentVal, true, "AccentVal", function(val)
    State.AccentVal = val
    UpdateGlobalColor()
end)

-- TAB: SETTINGS (UNLOAD)
local UnloadCard = Instance.new("TextButton", TabExit)
UnloadCard.Size = UDim2.new(1, 0, 0, 42)
UnloadCard.BackgroundColor3 = Color3.fromRGB(22, 12, 15)
UnloadCard.Text = "Unload Script"
UnloadCard.Font = FONT_BOLD
UnloadCard.TextColor3 = Color3.fromRGB(255, 75, 85)
UnloadCard.TextSize = 12
UnloadCard.AutoButtonColor = false
Instance.new("UICorner", UnloadCard).CornerRadius = UDim.new(0, 6)
local UnloadStroke = Instance.new("UIStroke", UnloadCard)
UnloadStroke.Color = Color3.fromRGB(55, 20, 26)
UnloadStroke.Thickness = 1

UnloadCard.MouseButton1Click:Connect(function()
    ClearHalo()
    ClearAura()
    ApplySkybox(false)
    ApplyTimeCycle(false)
    ApplyFog(false)
    ApplyColorCorrection(false)
    ApplyBloom(false)

    State.CustomWeather = false
    ApplyWeather()

    Lighting.Ambient = Color3.fromRGB(140, 140, 140)
    Lighting.OutdoorAmbient = Color3.fromRGB(140, 140, 140)
    Lighting.FogStart = 0
    Lighting.FogEnd = 100000
    Lighting.FogColor = Color3.fromRGB(192, 192, 192)
    Lighting.ClockTime = 14

    for _, c in pairs(Connections) do
        if c then pcall(function() c:Disconnect() end) end
    end

    local fade = TweenService:Create(MainScale, TweenInfo.new(0.2, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {Scale = 0.92})
    fade.Completed:Connect(function()
        pcall(function() ScreenGui:Destroy() end)
    end)
    fade:Play()
end)

-- Инициализация первой вкладки и отображение
if #Tabs > 0 then
    SwitchTab(Tabs[1].Page, Tabs[1].Btn)
end

SetMenuVisible(true)
