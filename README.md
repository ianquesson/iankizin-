local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

local player = game.Players.LocalPlayer
local tweenService = game:GetService("TweenService")
local runService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")

repeat task.wait() until player.Character
repeat task.wait() until player.Character:FindFirstChild("HumanoidRootPart")

-- INTRO
local IntroGui = Instance.new("ScreenGui", game:GetService("CoreGui"))
local IntroText = Instance.new("TextLabel", IntroGui)
IntroText.Size = UDim2.new(1, 0, 1, 0)
IntroText.BackgroundTransparency = 1
IntroText.Text = "iankizinX hub"
IntroText.TextColor3 = Color3.fromRGB(0, 255, 150)
IntroText.Font = Enum.Font.GothamBold
IntroText.TextSize = 65
IntroText.TextTransparency = 1

local ts = game:GetService("TweenService")
ts:Create(IntroText, TweenInfo.new(1.2), {TextTransparency = 0}):Play()
task.wait(1.8)
ts:Create(IntroText, TweenInfo.new(0.8), {TextTransparency = 1}):Play()
task.wait(0.8)
IntroGui:Destroy()

-- WINDOW
local Window = Fluent:CreateWindow({
    Title = "iankizinX VIP",
    SubTitle = "Farm Edition",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = true,
    Theme = "Darker",
    MinimizeKey = Enum.KeyCode.RightControl
})

-- DASHBOARD
local DashGui = Instance.new("ScreenGui", game:GetService("CoreGui"))
local DashFrame = Instance.new("Frame", DashGui)
DashFrame.Size = UDim2.new(0, 230, 0, 95)
DashFrame.Position = UDim2.new(0.02, 0, 0.75, 0)
DashFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
DashFrame.BackgroundTransparency = 0.3
DashFrame.Visible = false
DashFrame.Active = true
DashFrame.Draggable = true

local NeonDash = Instance.new("UIStroke", DashFrame)
NeonDash.Color = Color3.fromRGB(0, 255, 150)
NeonDash.Thickness = 2
Instance.new("UICorner", DashFrame).CornerRadius = UDim.new(0, 15)

local Avatar = Instance.new("ImageLabel", DashFrame)
Avatar.Size = UDim2.new(0, 65, 0, 65)
Avatar.Position = UDim2.new(0.05, 0, 0.15, 0)
Avatar.BackgroundTransparency = 1
local content = game.Players:GetUserThumbnailAsync(player.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size100x100)
Avatar.Image = content
Instance.new("UICorner", Avatar).CornerRadius = UDim.new(1, 0)

local InfoText = Instance.new("TextLabel", DashFrame)
InfoText.Size = UDim2.new(0, 140, 0, 65)
InfoText.Position = UDim2.new(0.38, 0, 0.15, 0)
InfoText.BackgroundTransparency = 1
InfoText.TextColor3 = Color3.fromRGB(255, 255, 255)
InfoText.Font = Enum.Font.GothamBold
InfoText.TextSize = 13
InfoText.TextXAlignment = Enum.TextXAlignment.Left

runService.RenderStepped:Connect(function(dt)
    if DashFrame.Visible then
        local fps = math.floor(1/dt)
        InfoText.Text = "👤 "..player.Name.."\n⚡ FPS: "..fps.."\n🟢 Status: VIP"
    end
end)

-- BOTÃO MINIMIZAR
local ToggleBtn = Instance.new("TextButton", DashGui)
ToggleBtn.Size = UDim2.new(0, 50, 0, 50)
ToggleBtn.Position = UDim2.new(0.02, 0, 0.05, 0)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
ToggleBtn.Text = "Ian"
ToggleBtn.TextColor3 = Color3.fromRGB(0, 255, 150)
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.Draggable = true
Instance.new("UICorner", ToggleBtn).CornerRadius = UDim.new(1, 0)

local BtnStroke = Instance.new("UIStroke", ToggleBtn)
BtnStroke.Color = Color3.fromRGB(0, 255, 150)

ToggleBtn.MouseButton1Click:Connect(function()
    Window:Minimize()
end)

-- TABS
local Tabs = {
    Farm = Window:AddTab({ Title = "Farm", Icon = "coins" }),
    Player = Window:AddTab({ Title = "Player", Icon = "user" }),
    Visual = Window:AddTab({ Title = "Visual", Icon = "eye" })
}

-- ===== FUNÇÕES ORIGINAIS =====
local LoopAtivo = false
local checkpoint

local function applyGod(char)
    local hum = char:WaitForChild("Humanoid")
    hum:GetPropertyChangedSignal("Health"):Connect(function()
        if LoopAtivo then
            hum.Health = hum.MaxHealth
        end
    end)
end

applyGod(player.Character)

player.CharacterAdded:Connect(function(c)
    task.wait(1)
    applyGod(c)
end)

runService.Stepped:Connect(function()
    if player.Character and LoopAtivo then
        local hum = player.Character:FindFirstChild("Humanoid")
        local hrp = player.Character:FindFirstChild("HumanoidRootPart")

        if hum then
            hum.PlatformStand = false
            hum.Sit = false
        end

        if hrp then
            hrp.Anchored = false
        end
    end
end)

local function flyTo(cf)
    local hrp = player.Character:FindFirstChild("HumanoidRootPart")
    if hrp then
        tweenService:Create(hrp, TweenInfo.new(1), {CFrame = cf}):Play()
    end
end

local function isBrainrot(name)
    name = name:lower()
    return name:find("brainrot") or name:find("br")
end

-- ===== NOCLIP =====
local noclip = false

local function setCollision(state)
    if player.Character then
        for _, part in pairs(player.Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = state
            end
        end
    end
end

runService.Stepped:Connect(function()
    if noclip and player.Character then
        setCollision(false)
    end
end)

Tabs.Player:AddToggle("NoClip", {Title = "🚶 No Clip"}):OnChanged(function(v)
    noclip = v

    if not v then
        setCollision(true)
        local hum = player.Character and player.Character:FindFirstChild("Humanoid")
        if hum then
            hum:ChangeState(Enum.HumanoidStateType.GettingUp)
        end
    end
end)

-- ===== PULO INFINITO (ADICIONADO PERFEITO) =====
local infiniteJump = false

Tabs.Player:AddToggle("InfJump", {
    Title = "🦘 Pulo Infinito"
}):OnChanged(function(v)
    infiniteJump = v
end)

UIS.JumpRequest:Connect(function()
    if infiniteJump and player.Character then
        local hum = player.Character:FindFirstChildOfClass("Humanoid")
        if hum and hum:GetState() ~= Enum.HumanoidStateType.Dead then
            hum:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end
end)

-- ===== MENU =====
Tabs.Farm:AddToggle("Money", {Title = "💰 Auto Money"}):OnChanged(function(state)
    _G.money = state
    task.spawn(function()
        while _G.money do
            for _, v in pairs(workspace:GetDescendants()) do
                if v:IsA("BasePart") and (v.Name:lower():find("collect") or v.Name:lower():find("claim")) then
                    firetouchinterest(player.Character.HumanoidRootPart, v, 0)
                    firetouchinterest(player.Character.HumanoidRootPart, v, 1)
                end
            end
            task.wait(1)
        end
    end)
end)

Tabs.Farm:AddButton({
    Title = "🚀 Ir Base 13",
    Callback = function()
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("BasePart") and (v.Name:find("13") or v.Name:lower():find("final")) then
                flyTo(v.CFrame * CFrame.new(0,10,0))
                break
            end
        end
    end
})

Tabs.Farm:AddToggle("Farm", {Title = "🔥 Master Farm"}):OnChanged(function(state)
    LoopAtivo = state

    if state then
        checkpoint = player.Character.HumanoidRootPart.CFrame

        player.Backpack.ChildAdded:Connect(function(item)
            if LoopAtivo and isBrainrot(item.Name) then
                task.wait(0.1)
                flyTo(checkpoint)
            end
        end)

        player.Character.ChildAdded:Connect(function(item)
            if LoopAtivo and isBrainrot(item.Name) then
                task.wait(0.1)
                flyTo(checkpoint)
            end
        end)

        task.spawn(function()
            while LoopAtivo do
                for _, v in pairs(workspace:GetDescendants()) do
                    if v:IsA("ProximityPrompt") then
                        local p = v.Parent
                        if p and isBrainrot(p.Name) then
                            fireproximityprompt(v)
                        end
                    end
                end
                task.wait(1)
            end
        end)
    end
end)

Tabs.Visual:AddToggle("Dash", {Title = "Dashboard FPS"}):OnChanged(function(v)
    DashFrame.Visible = v
end)

Window:SelectTab(1)
