# Roblox-script
My own script whit AI
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer

local noclipEnabled = false
local espEnabled = false
local infiniteJumpEnabled = false
local fullbrightEnabled = false
local antiAfkEnabled = false
local espConnections = {}
local antiAfkConnection = nil
local originalAmbient = Lighting.Ambient
local originalBrightness = Lighting.Brightness
local originalOutdoorAmbient = Lighting.OutdoorAmbient

-- === INTRO ANIMATION ===
local introGui = Instance.new("ScreenGui")
introGui.Name = "CrytixIntro"
introGui.ResetOnSpawn = false
introGui.Parent = player.PlayerGui

local introFrame = Instance.new("Frame")
introFrame.Size = UDim2.new(1, 0, 1, 0)
introFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
introFrame.BackgroundTransparency = 0
introFrame.BorderSizePixel = 0
introFrame.Parent = introGui

local cLabel = Instance.new("TextLabel")
cLabel.Size = UDim2.new(0, 200, 0, 200)
cLabel.Position = UDim2.new(0.5, -100, 0.5, -100)
cLabel.BackgroundTransparency = 1
cLabel.Text = "C"
cLabel.Font = Enum.Font.GothamBold
cLabel.TextSize = 0
cLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
cLabel.TextTransparency = 1
cLabel.Parent = introFrame

local subLabel = Instance.new("TextLabel")
subLabel.Size = UDim2.new(0, 300, 0, 40)
subLabel.Position = UDim2.new(0.5, -150, 0.5, 80)
subLabel.BackgroundTransparency = 1
subLabel.Text = "Crytix Hub"
subLabel.Font = Enum.Font.GothamBold
subLabel.TextSize = 20
subLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
subLabel.TextTransparency = 1
subLabel.Parent = introFrame

-- Animate C appearing
local function playIntro()
    -- Fade in C
    local t1 = TweenService:Create(cLabel, TweenInfo.new(0.4, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        TextSize = 120,
        TextTransparency = 0
    })
    t1:Play()
    t1.Completed:Wait()

    task.wait(0.2)

    -- Pulse C
    local t2 = TweenService:Create(cLabel, TweenInfo.new(0.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        TextSize = 140
    })
    t2:Play()
    t2.Completed:Wait()
    local t3 = TweenService:Create(cLabel, TweenInfo.new(0.15, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {
        TextSize = 120
    })
    t3:Play()
    t3.Completed:Wait()

    task.wait(0.1)

    -- Fade in subtitle
    local t4 = TweenService:Create(subLabel, TweenInfo.new(0.4, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        TextTransparency = 0
    })
    t4:Play()
    t4.Completed:Wait()

    task.wait(0.8)

    -- Fade everything out
    local t5 = TweenService:Create(cLabel, TweenInfo.new(0.4), { TextTransparency = 1 })
    local t6 = TweenService:Create(subLabel, TweenInfo.new(0.4), { TextTransparency = 1 })
    local t7 = TweenService:Create(introFrame, TweenInfo.new(0.4), { BackgroundTransparency = 1 })
    t5:Play() t6:Play() t7:Play()
    t7.Completed:Wait()

    introGui:Destroy()
end

task.spawn(playIntro)

-- === GUI ===
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "CrytixHub"
screenGui.ResetOnSpawn = false
screenGui.Parent = player.PlayerGui

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 280, 0, 360)
mainFrame.Position = UDim2.new(0.5, -140, 0.5, -180)
mainFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui
local mc = Instance.new("UICorner")
mc.CornerRadius = UDim.new(0, 8)
mc.Parent = mainFrame

local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 36)
titleBar.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame
local tbc = Instance.new("UICorner")
tbc.CornerRadius = UDim.new(0, 8)
tbc.Parent = titleBar

local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(1, -40, 1, 0)
titleLabel.Position = UDim2.new(0, 10, 0, 0)
titleLabel.BackgroundTransparency = 1
titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
titleLabel.Text = "Crytix Hub"
titleLabel.Font = Enum.Font.GothamBold
titleLabel.TextSize = 15
titleLabel.TextXAlignment = Enum.TextXAlignment.Left
titleLabel.Parent = titleBar

local hideBtn = Instance.new("TextButton")
hideBtn.Size = UDim2.new(0, 28, 0, 28)
hideBtn.Position = UDim2.new(1, -32, 0, 4)
hideBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
hideBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
hideBtn.Text = "−"
hideBtn.Font = Enum.Font.GothamBold
hideBtn.TextSize = 18
hideBtn.BorderSizePixel = 0
hideBtn.Parent = titleBar
local hc = Instance.new("UICorner")
hc.CornerRadius = UDim.new(0, 6)
hc.Parent = hideBtn

local tabBar = Instance.new("Frame")
tabBar.Size = UDim2.new(1, -20, 0, 32)
tabBar.Position = UDim2.new(0, 10, 0, 42)
tabBar.BackgroundTransparency = 1
tabBar.Parent = mainFrame
local tabList = Instance.new("UIListLayout")
tabList.FillDirection = Enum.FillDirection.Horizontal
tabList.SortOrder = Enum.SortOrder.LayoutOrder
tabList.Padding = UDim.new(0, 6)
tabList.Parent = tabBar

local function createTabBtn(label, order)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.5, -3, 1, 0)
    btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    btn.TextColor3 = Color3.fromRGB(180, 180, 180)
    btn.Text = label
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 13
    btn.BorderSizePixel = 0
    btn.LayoutOrder = order
    btn.Parent = tabBar
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 6)
    c.Parent = btn
    return btn
end

local tabMain  = createTabBtn("Main", 1)
local tabOther = createTabBtn("Other", 2)

local function createContentFrame()
    local f = Instance.new("ScrollingFrame")
    f.Size = UDim2.new(1, -20, 1, -90)
    f.Position = UDim2.new(0, 10, 0, 82)
    f.BackgroundTransparency = 1
    f.BorderSizePixel = 0
    f.ScrollBarThickness = 3
    f.ScrollBarImageColor3 = Color3.fromRGB(80, 80, 80)
    f.CanvasSize = UDim2.new(0, 0, 0, 0)
    f.AutomaticCanvasSize = Enum.AutomaticSize.Y
    f.Parent = mainFrame
    local l = Instance.new("UIListLayout")
    l.SortOrder = Enum.SortOrder.LayoutOrder
    l.Padding = UDim.new(0, 8)
    l.Parent = f
    return f
end

local mainContent  = createContentFrame()
local otherContent = createContentFrame()
otherContent.Visible = false

-- === Helpers ===
local function createToggle(parent, labelText, order)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 38)
    btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    btn.TextColor3 = Color3.fromRGB(200, 200, 200)
    btn.Text = "[ OFF ]  " .. labelText
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 13
    btn.BorderSizePixel = 0
    btn.LayoutOrder = order
    btn.Parent = parent
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 6)
    c.Parent = btn
    return btn
end

local function createRow(parent, labelText, defaultVal, order)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, 0, 0, 38)
    frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    frame.BorderSizePixel = 0
    frame.LayoutOrder = order
    frame.Parent = parent
    local fc = Instance.new("UICorner")
    fc.CornerRadius = UDim.new(0, 6)
    fc.Parent = frame
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(0.6, 0, 1, 0)
    lbl.BackgroundTransparency = 1
    lbl.TextColor3 = Color3.fromRGB(200, 200, 200)
    lbl.Text = "  " .. labelText
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = 12
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = frame
    local box = Instance.new("TextBox")
    box.Size = UDim2.new(0.35, 0, 0.7, 0)
    box.Position = UDim2.new(0.62, 0, 0.15, 0)
    box.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    box.TextColor3 = Color3.fromRGB(255, 255, 255)
    box.Text = tostring(defaultVal)
    box.Font = Enum.Font.Gotham
    box.TextSize = 13
    box.BorderSizePixel = 0
    box.Parent = frame
    local bc = Instance.new("UICorner")
    bc.CornerRadius = UDim.new(0, 5)
    bc.Parent = box
    return box
end

local function setToggleVisual(btn, state, label)
    if state then
        btn.Text = "[ ON ]   " .. label
        btn.BackgroundColor3 = Color3.fromRGB(25, 60, 25)
        btn.TextColor3 = Color3.fromRGB(100, 255, 100)
    else
        btn.Text = "[ OFF ]  " .. label
        btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        btn.TextColor3 = Color3.fromRGB(200, 200, 200)
    end
end

local function setActiveTab(isMain)
    mainContent.Visible = isMain
    otherContent.Visible = not isMain
    if isMain then
        tabMain.BackgroundColor3  = Color3.fromRGB(50, 50, 50)
        tabMain.TextColor3        = Color3.fromRGB(255, 255, 255)
        tabOther.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        tabOther.TextColor3       = Color3.fromRGB(180, 180, 180)
    else
        tabOther.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
        tabOther.TextColor3       = Color3.fromRGB(255, 255, 255)
        tabMain.BackgroundColor3  = Color3.fromRGB(30, 30, 30)
        tabMain.TextColor3        = Color3.fromRGB(180, 180, 180)
    end
end
setActiveTab(true)

tabMain.MouseButton1Click:Connect(function()  setActiveTab(true)  end)
tabOther.MouseButton1Click:Connect(function() setActiveTab(false) end)

-- === MAIN TAB ITEMS ===
local noclipBtn       = createToggle(mainContent, "Noclip", 1)
local walkBox         = createRow(mainContent, "WalkSpeed (default 16):", 16, 2)
local jumpBox         = createRow(mainContent, "JumpPower (default 50):", 50, 3)
local infiniteJumpBtn = createToggle(mainContent, "Infinite Jump", 4)
local espBtn          = createToggle(mainContent, "ESP", 5)

-- === OTHER TAB ITEMS ===
local antiAfkBtn    = createToggle(otherContent, "Anti-AFK", 1)
local fullbrightBtn = createToggle(otherContent, "Fullbright", 2)
local fovBox        = createRow(otherContent, "FOV (default 70, 1-360):", 70, 3)

local hintFrame = Instance.new("Frame")
hintFrame.Size = UDim2.new(1, 0, 0, 110)
hintFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
hintFrame.BorderSizePixel = 0
hintFrame.LayoutOrder = 4
hintFrame.Parent = otherContent
local hintCorner = Instance.new("UICorner")
hintCorner.CornerRadius = UDim.new(0, 6)
hintCorner.Parent = hintFrame
local hintLabel = Instance.new("TextLabel")
hintLabel.Size = UDim2.new(1, -10, 1, -10)
hintLabel.Position = UDim2.new(0, 5, 0, 5)
hintLabel.BackgroundTransparency = 1
hintLabel.TextColor3 = Color3.fromRGB(160, 160, 160)
hintLabel.Font = Enum.Font.Gotham
hintLabel.TextSize = 11
hintLabel.TextWrapped = true
hintLabel.TextXAlignment = Enum.TextXAlignment.Left
hintLabel.TextYAlignment = Enum.TextYAlignment.Top
hintLabel.Text = "💬 Chat Commands (prefix ';'):\n;noclip → ;clip\n;esp → ;noesp\n;ijump → ;noijump\n;afk → ;noafk\n;fullbright → ;darkmode\n;ws [num]  ;jp [num]  ;fov [num]"
hintLabel.Parent = hintFrame

-- === Hide/Show ===
local isHidden = false
hideBtn.MouseButton1Click:Connect(function()
    isHidden = not isHidden
    tabBar.Visible = not isHidden
    mainContent.Visible = false
    otherContent.Visible = false
    mainFrame.Size = isHidden and UDim2.new(0, 280, 0, 40) or UDim2.new(0, 280, 0, 360)
    hideBtn.Text = isHidden and "+" or "−"
    if not isHidden then setActiveTab(true) end
end)

-- === Noclip ===
RunService.Stepped:Connect(function()
    if not noclipEnabled then return end
    local char = player.Character
    if not char then return end
    for _, part in pairs(char:GetDescendants()) do
        if part:IsA("BasePart") then part.CanCollide = false end
    end
end)

local function enableNoclip()
    noclipEnabled = true
    setToggleVisual(noclipBtn, true, "Noclip")
end
local function disableNoclip()
    noclipEnabled = false
    setToggleVisual(noclipBtn, false, "Noclip")
    local char = player.Character
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then part.CanCollide = true end
        end
    end
end
noclipBtn.MouseButton1Click:Connect(function()
    if noclipEnabled then disableNoclip() else enableNoclip() end
end)

-- === WalkSpeed ===
local function applyWalkSpeed(val)
    local char = player.Character
    if not char then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not hum then return end
    local v = val or tonumber(walkBox.Text)
    if v then hum.WalkSpeed = v walkBox.Text = tostring(v)
    else walkBox.Text = "16" hum.WalkSpeed = 16 end
end
walkBox.FocusLost:Connect(function() applyWalkSpeed() end)

-- === JumpPower ===
local function applyJumpPower(val)
    local char = player.Character
    if not char then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not hum then return end
    local v = val or tonumber(jumpBox.Text)
    if v then hum.JumpPower = v jumpBox.Text = tostring(v)
    else jumpBox.Text = "50" hum.JumpPower = 50 end
end
jumpBox.FocusLost:Connect(function() applyJumpPower() end)

-- === Infinite Jump ===
local function enableInfiniteJump()
    infiniteJumpEnabled = true
    setToggleVisual(infiniteJumpBtn, true, "Infinite Jump")
end
local function disableInfiniteJump()
    infiniteJumpEnabled = false
    setToggleVisual(infiniteJumpBtn, false, "Infinite Jump")
end
infiniteJumpBtn.MouseButton1Click:Connect(function()
    if infiniteJumpEnabled then disableInfiniteJump() else enableInfiniteJump() end
end)

UserInputService.JumpRequest:Connect(function()
    if not infiniteJumpEnabled then return end
    local char = player.Character
    if not char then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    if hum and hum:GetState() ~= Enum.HumanoidStateType.Dead then
        hum:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

-- === ESP ===
local function addESP(target)
    task.wait(0.5)
    if not espEnabled then return end
    local char = target.Character
    if not char then return end
    if char:FindFirstChild("ESP_Box") then return end
    local box = Instance.new("SelectionBox")
    box.Name = "ESP_Box"
    box.Adornee = char
    box.Color3 = Color3.fromRGB(255, 0, 0)
    box.LineThickness = 0.05
    box.SurfaceTransparency = 1
    box.SurfaceColor3 = Color3.fromRGB(255, 0, 0)
    box.Parent = char
end
local function clearESP()
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= player and p.Character then
            local box = p.Character:FindFirstChild("ESP_Box")
            if box then box:Destroy() end
        end
    end
end
local function enableESP()
    espEnabled = true
    setToggleVisual(espBtn, true, "ESP")
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= player then addESP(p) end
    end
    table.insert(espConnections, Players.PlayerAdded:Connect(function(p)
        p.CharacterAdded:Connect(function() addESP(p) end)
    end))
end
local function disableESP()
    espEnabled = false
    setToggleVisual(espBtn, false, "ESP")
    clearESP()
    for _, c in pairs(espConnections) do c:Disconnect() end
    espConnections = {}
end
espBtn.MouseButton1Click:Connect(function()
    if espEnabled then disableESP() else enableESP() end
end)

-- === Anti-AFK ===
local function enableAntiAfk()
    antiAfkEnabled = true
    setToggleVisual(antiAfkBtn, true, "Anti-AFK")
    antiAfkConnection = player.Idled:Connect(function()
        local vip = game:GetService("VirtualInputManager")
        vip:SendKeyEvent(true, "W", false, game)
        task.wait(0.1)
        vip:SendKeyEvent(false, "W", false, game)
    end)
end
local function disableAntiAfk()
    antiAfkEnabled = false
    setToggleVisual(antiAfkBtn, false, "Anti-AFK")
    if antiAfkConnection then antiAfkConnection:Disconnect() antiAfkConnection = nil end
end
antiAfkBtn.MouseButton1Click:Connect(function()
    if antiAfkEnabled then disableAntiAfk() else enableAntiAfk() end
end)

-- === Fullbright ===
local function enableFullbright()
    fullbrightEnabled = true
    setToggleVisual(fullbrightBtn, true, "Fullbright")
    Lighting.Ambient = Color3.fromRGB(255, 255, 255)
    Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
    Lighting.Brightness = 2
    for _, effect in pairs(Lighting:GetChildren()) do
        if effect:IsA("BloomEffect") or effect:IsA("BlurEffect") or
           effect:IsA("ColorCorrectionEffect") or effect:IsA("SunRaysEffect") then
            effect.Enabled = false
        end
    end
end
local function disableFullbright()
    fullbrightEnabled = false
    setToggleVisual(fullbrightBtn, false, "Fullbright")
    Lighting.Ambient = originalAmbient
    Lighting.OutdoorAmbient = originalOutdoorAmbient
    Lighting.Brightness = originalBrightness
    for _, effect in pairs(Lighting:GetChildren()) do
        if effect:IsA("BloomEffect") or effect:IsA("BlurEffect") or
           effect:IsA("ColorCorrectionEffect") or effect:IsA("SunRaysEffect") then
            effect.Enabled = true
        end
    end
end
fullbrightBtn.MouseButton1Click:Connect(function()
    if fullbrightEnabled then disableFullbright() else enableFullbright() end
end)

-- === FOV ===
local function applyFOV(val)
    local cam = workspace.CurrentCamera
    if not cam then return end
    local v = val or tonumber(fovBox.Text)
    if v then
        v = math.clamp(v, 1, 360)
        cam.FieldOfView = v
        fovBox.Text = tostring(v)
    else
        fovBox.Text = "70"
        cam.FieldOfView = 70
    end
end
fovBox.FocusLost:Connect(function() applyFOV() end)

-- === Chat Commands ===
player.Chatted:Connect(function(msg)
    local raw = msg:lower():match("^%s*(.-)%s*$")
    if raw:sub(1, 1) ~= ";" then return end
    local parts = {}
    for word in raw:sub(2):gmatch("%S+") do table.insert(parts, word) end
    local cmd = parts[1]
    local arg = tonumber(parts[2])

    if     cmd == "noclip"      then enableNoclip()
    elseif cmd == "clip"        then disableNoclip()
    elseif cmd == "esp"         then enableESP()
    elseif cmd == "noesp"       then disableESP()
    elseif cmd == "ijump"       then enableInfiniteJump()
    elseif cmd == "noijump"     then disableInfiniteJump()
    elseif cmd == "afk"         then enableAntiAfk()
    elseif cmd == "noafk"       then disableAntiAfk()
    elseif cmd == "fullbright"  then enableFullbright()
    elseif cmd == "darkmode"    then disableFullbright()
    elseif cmd == "ws"  and arg then walkBox.Text = tostring(arg) applyWalkSpeed(arg)
    elseif cmd == "jp"  and arg then jumpBox.Text = tostring(arg) applyJumpPower(arg)
    elseif cmd == "fov" and arg then fovBox.Text  = tostring(arg) applyFOV(arg)
    end
end)

-- === Respawn ===
player.CharacterAdded:Connect(function(char)
    noclipEnabled = false
    setToggleVisual(noclipBtn, false, "Noclip")
    task.wait(0.5)
    local hum = char:FindFirstChildOfClass("Humanoid")
    if hum then
        hum.WalkSpeed = tonumber(walkBox.Text) or 16
        hum.JumpPower = tonumber(jumpBox.Text) or 50
    end
end)
