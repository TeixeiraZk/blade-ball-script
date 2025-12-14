-- // 1. CONFIGURAÇÕES & SERVIÇOS
local clonseref = clonseref or function(obj) return obj end
local getgenv = getgenv or function() return _G end
local TweenService = clonseref(game:GetService("TweenService"))
local UserInputService = clonseref(game:GetService("UserInputService"))
local RunService = clonseref(game:GetService("RunService"))
local VirtualInputManager = clonseref(game:GetService("VirtualInputManager"))
local Players = clonseref(game:GetService("Players"))
local Workspace = clonseref(game:GetService("Workspace"))
local CoreGui = game:GetService("CoreGui")

local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

getgenv().BladeConfig = {
    AutoParry = false,
    SpamParry = false,
    Visualizer = false, -- NOVA OPÇÃO
    BaseRange = 12,
    IsParrying = false,
    LastParryTick = 0,
    MenuOpen = true
}

-- Limpeza Geral
if getgenv().AstralUI then getgenv().AstralUI:Destroy() end
if getgenv().AstralRingPart then getgenv().AstralRingPart:Destroy() end -- Limpa o anel antigo
if CoreGui:FindFirstChild("AstralV29") then CoreGui.AstralV29:Destroy() end

--------------------------------------------------------------------------------
-- 2. ENGINE GRÁFICA (MINIMALIST + VISUALIZER UI)
--------------------------------------------------------------------------------
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "AstralX"
ScreenGui.Parent = CoreGui
ScreenGui.ResetOnSpawn = false
getgenv().AstralUI = ScreenGui

local Theme = {
    Bg = Color3.fromRGB(8, 10, 15),
    ItemBg = Color3.fromRGB(15, 20, 30),
    Text = Color3.fromRGB(240, 255, 255),
    TextDim = Color3.fromRGB(120, 140, 160),
    NeonCyan = Color3.fromRGB(0, 255, 255),
    NeonRed = Color3.fromRGB(255, 50, 50),
    DeepBlue = Color3.fromRGB(0, 60, 255)
}

-- :: FRAME TRASEIRO ::
local GalaxyBorder = Instance.new("Frame")
GalaxyBorder.Name = "GalaxyBorder"
GalaxyBorder.Parent = ScreenGui
GalaxyBorder.AnchorPoint = Vector2.new(0.5, 0.5)
GalaxyBorder.Position = UDim2.new(0.5, -140, 0.5, -180)
GalaxyBorder.Size = UDim2.new(0, 304, 0, 310) -- Altura ajustada para 3 itens
GalaxyBorder.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
GalaxyBorder.BorderSizePixel = 0
GalaxyBorder.Active = true
GalaxyBorder.Draggable = true 

local OuterCorner = Instance.new("UICorner")
OuterCorner.CornerRadius = UDim.new(0, 20)
OuterCorner.Parent = GalaxyBorder

local BorderGradient = Instance.new("UIGradient")
BorderGradient.Parent = GalaxyBorder
BorderGradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Theme.DeepBlue),
    ColorSequenceKeypoint.new(0.5, Theme.NeonCyan),
    ColorSequenceKeypoint.new(1, Theme.DeepBlue)
})

task.spawn(function()
    local rot = 0
    while task.wait() do
        if not BorderGradient.Parent then break end
        rot = rot + 2
        BorderGradient.Rotation = rot
    end
end)

-- :: CONTEÚDO ::
local MainFrame = Instance.new("Frame")
MainFrame.Name = "Content"
MainFrame.Parent = GalaxyBorder
MainFrame.BackgroundColor3 = Theme.Bg
MainFrame.Size = UDim2.new(1, -4, 1, -4) 
MainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
MainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
MainFrame.BorderSizePixel = 0

local InnerCorner = Instance.new("UICorner")
InnerCorner.CornerRadius = UDim.new(0, 18)
InnerCorner.Parent = MainFrame

-- [[ ESTRELA DE CÓDIGO ]]
local CodeStar = Instance.new("Frame")
CodeStar.Parent = MainFrame
CodeStar.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
CodeStar.AnchorPoint = Vector2.new(0.5, 0.5)
CodeStar.Position = UDim2.new(0, 30, 0, 27)
CodeStar.Size = UDim2.new(0, 20, 0, 20)
CodeStar.Rotation = 45
CodeStar.BorderSizePixel = 0

local StarCorner = Instance.new("UICorner")
StarCorner.CornerRadius = UDim.new(0, 4)
StarCorner.Parent = CodeStar

local StarGrad = Instance.new("UIGradient")
StarGrad.Parent = CodeStar
StarGrad.Color = BorderGradient.Color
StarGrad.Rotation = -45

-- Título
local Title = Instance.new("TextLabel")
Title.Parent = MainFrame
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 55, 0, 15)
Title.Size = UDim2.new(1, -65, 0, 25)
Title.Font = Enum.Font.GothamBlack
Title.Text = "ASTRAL X"
Title.TextColor3 = Theme.Text
Title.TextSize = 22
Title.TextXAlignment = Enum.TextXAlignment.Left

local TitleGrad = Instance.new("UIGradient")
TitleGrad.Parent = Title
TitleGrad.Color = ColorSequence.new{ColorSequenceKeypoint.new(0, Theme.DeepBlue), ColorSequenceKeypoint.new(1, Theme.NeonCyan)}

local SubTitle = Instance.new("TextLabel")
SubTitle.Parent = MainFrame
SubTitle.BackgroundTransparency = 1
SubTitle.Position = UDim2.new(0, 55, 0, 38)
SubTitle.Size = UDim2.new(1, -65, 0, 15)
SubTitle.Font = Enum.Font.GothamMedium
SubTitle.Text = "Morra"
SubTitle.TextColor3 = Theme.TextDim
SubTitle.TextSize = 12
SubTitle.TextXAlignment = Enum.TextXAlignment.Left

-- Container
local Container = Instance.new("Frame")
Container.Parent = MainFrame
Container.BackgroundTransparency = 1
Container.Position = UDim2.new(0, 15, 0, 70)
Container.Size = UDim2.new(1, -30, 1, -100)

local UIList = Instance.new("UIListLayout")
UIList.Parent = Container
UIList.Padding = UDim.new(0, 12)
UIList.SortOrder = Enum.SortOrder.LayoutOrder

-- Footer
local Footer = Instance.new("TextLabel")
Footer.Parent = MainFrame
Footer.BackgroundTransparency = 1
Footer.Position = UDim2.new(0, 0, 1, -20)
Footer.Size = UDim2.new(1, 0, 0, 20)
Footer.Font = Enum.Font.Gotham
Footer.Text = "Toggle UI: Right Control"
Footer.TextColor3 = Theme.TextDim
Footer.TextSize = 11

-- [[ COMPONENTES ]] --
local TweenInf = TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out)

local function CreateToggle(Text, ConfigKey, Order)
    local Wrapper = Instance.new("Frame")
    Wrapper.Parent = Container
    Wrapper.BackgroundColor3 = Theme.ItemBg
    Wrapper.Size = UDim2.new(1, 0, 0, 48)
    Wrapper.LayoutOrder = Order
    Instance.new("UICorner", Wrapper).CornerRadius = UDim.new(0, 12)
    
    local Label = Instance.new("TextLabel")
    Label.Parent = Wrapper
    Label.BackgroundTransparency = 1
    Label.Position = UDim2.new(0, 15, 0, 0)
    Label.Size = UDim2.new(0.6, 0, 1, 0)
    Label.Font = Enum.Font.GothamBold
    Label.Text = Text
    Label.TextColor3 = Theme.Text
    Label.TextSize = 14
    Label.TextXAlignment = Enum.TextXAlignment.Left
    
    local Button = Instance.new("TextButton")
    Button.Parent = Wrapper
    Button.BackgroundColor3 = Color3.fromRGB(25, 30, 40)
    Button.Position = UDim2.new(1, -60, 0.5, -12)
    Button.Size = UDim2.new(0, 44, 0, 24)
    Button.Text = ""
    Button.AutoButtonColor = false
    Instance.new("UICorner", Button).CornerRadius = UDim.new(1, 0)
    
    local Knob = Instance.new("Frame")
    Knob.Parent = Button
    Knob.BackgroundColor3 = Theme.Text
    Knob.Position = UDim2.new(0, 2, 0.5, -10)
    Knob.Size = UDim2.new(0, 20, 0, 20)
    Instance.new("UICorner", Knob).CornerRadius = UDim.new(1, 0)
    
    local BtnGrad = Instance.new("UIGradient")
    BtnGrad.Parent = Button
    BtnGrad.Color = BorderGradient.Color
    BtnGrad.Enabled = false
    
    local function Update()
        if getgenv().BladeConfig[ConfigKey] then
            BtnGrad.Enabled = true
            TweenService:Create(Knob, TweenInf, {Position = UDim2.new(1, -22, 0.5, -10)}):Play()
        else
            BtnGrad.Enabled = false
            TweenService:Create(Button, TweenInf, {BackgroundColor3 = Color3.fromRGB(25, 30, 40)}):Play()
            TweenService:Create(Knob, TweenInf, {Position = UDim2.new(0, 2, 0.5, -10)}):Play()
        end
    end
    
    Button.MouseButton1Click:Connect(function()
        getgenv().BladeConfig[ConfigKey] = not getgenv().BladeConfig[ConfigKey]
        Update()
    end)
    Update()
    return Update
end

-- Itens do Menu
CreateToggle("Auto Parry", "AutoParry", 1)
CreateToggle("Range", "Range", 2)
local UpdateSpam = CreateToggle("GOD CLASH", "SpamParry", 3)

-- Toggle Visible (Nuclear Fix)
local function ToggleMenu()
    getgenv().BladeConfig.MenuOpen = not getgenv().BladeConfig.MenuOpen
    if getgenv().BladeConfig.MenuOpen then
        GalaxyBorder.Visible = true
        GalaxyBorder.BackgroundTransparency = 1
        TweenService:Create(GalaxyBorder, TweenInfo.new(0.3), {BackgroundTransparency = 0}):Play()
    else
        GalaxyBorder.Visible = false
    end
end

UserInputService.InputBegan:Connect(function(input, p)
    if not p then
        if input.KeyCode == Enum.KeyCode.T then
            getgenv().BladeConfig.SpamParry = not getgenv().BladeConfig.SpamParry
            UpdateSpam()
        elseif input.KeyCode == Enum.KeyCode.RightControl then
            ToggleMenu()
        end
    end
end)

--------------------------------------------------------------------------------
-- 3. SISTEMA DE VISUALIZER (ASTRAL RING)
--------------------------------------------------------------------------------
local AstralRing = Instance.new("Part")
AstralRing.Name = "AstralRing"
AstralRing.Shape = Enum.PartType.Cylinder
AstralRing.Material = Enum.Material.ForceField -- Efeito Sci-Fi
AstralRing.Transparency = 0.5
AstralRing.CanCollide = false
AstralRing.Anchored = true
AstralRing.CastShadow = false
AstralRing.Parent = Workspace
getgenv().AstralRingPart = AstralRing

-- Função para atualizar o anel
local function UpdateVisualizer(TargetingMe, CalculatedRange)
    if not getgenv().BladeConfig.Visualizer or not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        AstralRing.Transparency = 1 -- Invisível se desligado
        AstralRing.Position = Vector3.new(0, -1000, 0)
        return
    end

    local Root = LocalPlayer.Character.HumanoidRootPart
    
    -- Configurações Visuais
    AstralRing.Transparency = 0.6
    if TargetingMe then
        AstralRing.Color = Theme.NeonRed -- PERIGO
        AstralRing.Material = Enum.Material.Neon -- Brilha mais
    else
        AstralRing.Color = Theme.NeonCyan -- SEGURO
        AstralRing.Material = Enum.Material.ForceField
    end

    -- Posicionar no chão (Pés do personagem)
    -- O cilindro no Roblox é deitado por padrão, precisamos girar 90 graus no Z
    AstralRing.CFrame = Root.CFrame * CFrame.new(0, -2.5, 0) * CFrame.Angles(0, 0, math.rad(90))
    
    -- Tamanho: X é a altura do cilindro (espessura do disco), Y e Z são o diâmetro
    -- Diâmetro = Raio * 2
    local Diameter = CalculatedRange * 2
    AstralRing.Size = Vector3.new(0.2, Diameter, Diameter)
end


--------------------------------------------------------------------------------
-- 4. LÓGICA DE COMBATE
--------------------------------------------------------------------------------
local function GetBall()
    local Balls = Workspace:FindFirstChild("Balls")
    if Balls then for _, B in pairs(Balls:GetChildren()) do if B:GetAttribute("realBall") then return B end end end
    return nil
end

local function ParryLogic()
    local Now = tick()
    if getgenv().BladeConfig.IsParrying or (Now - getgenv().BladeConfig.LastParryTick < 0.20) then return end
    getgenv().BladeConfig.IsParrying = true
    getgenv().BladeConfig.LastParryTick = Now
    
    pcall(function()
        VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.F, false, game)
        VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.F, false, game)
    end)
    
    task.wait(0.05)
    getgenv().BladeConfig.IsParrying = false
end

local function IsTargetingMe(Ball)
    local Target = Ball:GetAttribute("target")
    if Target == LocalPlayer.Name then return true end
    if not Target or Target == "" then
        local Root = LocalPlayer.Character.HumanoidRootPart
        local Dot = (Root.Position - Ball.Position).Unit:Dot(Ball.Velocity.Unit)
        if Dot > 0.85 then return true end
    end
    return false
end

-- THREADS
task.spawn(function()
    while true do
        if getgenv().BladeConfig.SpamParry then
            for i=1,5 do
                VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.F, false, game)
                VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.F, false, game)
            end
            task.wait()
        else
            task.wait(0.1)
        end
    end
end)

RunService.RenderStepped:Connect(function()
    if getgenv().BladeConfig.SpamParry then return end 
    
    local ValidBall = false
    local Targeting = false
    local CurrentReach = getgenv().BladeConfig.BaseRange

    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local Ball = GetBall()
        if Ball and Ball.Velocity.Magnitude > 0 then
            ValidBall = true
            
            -- Lógica V9: Distância + Velocidade
            CurrentReach = getgenv().BladeConfig.BaseRange + (Ball.Velocity.Magnitude / 4.0)
            Targeting = IsTargetingMe(Ball)
            
            -- Lógica de Parry
            if getgenv().BladeConfig.AutoParry and (tick() - getgenv().BladeConfig.LastParryTick) > 0.15 then
                if Targeting then
                    if (LocalPlayer.Character.HumanoidRootPart.Position - Ball.Position).Magnitude <= CurrentReach then
                        ParryLogic()
                    end
                end
            end
        end
    end
    
    -- Atualiza o Visualizer todo frame
    UpdateVisualizer(Targeting, CurrentReach)
end)

game.StarterGui:SetCore("SendNotification", {Title="Astral X", Text="Muito Ruim Slk akakak! Betinha ", Duration=5})
