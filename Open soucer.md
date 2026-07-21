-- Serviços do Roblox
local CoreGui = game:GetService("CoreGui")
local VirtualUser = game:GetService("VirtualUser")

-- Limpa interfaces antigas
if CoreGui:FindFirstChild("AutoClickerGui") then
    CoreGui.AutoClickerGui:Destroy()
end

-- Estado Global
_G.AutoClicker = false
_G.Intervalo = 0.1

-- Tela Principal (ScreenGui)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "AutoClickerGui"
ScreenGui.Parent = CoreGui

-- Botão para Ocultar/Exibir o Painel (Arraste-o para onde preferir)
local OpenButton = Instance.new("TextButton")
OpenButton.Size = UDim2.new(0, 80, 0, 30)
OpenButton.Position = UDim2.new(0.05, 0, 0.34, 0)
OpenButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
OpenButton.Text = "Menu [ - ]"
OpenButton.TextColor3 = Color3.fromRGB(255, 255, 255)
OpenButton.TextSize = 13
OpenButton.Font = Enum.Font.SourceSansBold
OpenButton.Active = true
OpenButton.Draggable = true
OpenButton.Parent = ScreenGui

local OpenCorner = Instance.new("UICorner")
OpenCorner.CornerRadius = UDim.new(0, 6)
OpenCorner.Parent = OpenButton

-- Painel Principal (Janela do Auto Clicker)
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 180, 0, 70)
Frame.Position = UDim2.new(0.05, 0, 0.4, 0)
Frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Frame.Active = true
Frame.Draggable = true
Frame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = Frame

-- Botão de Ligar/Desligar Auto Clicker
local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(0, 160, 0, 45)
ToggleButton.Position = UDim2.new(0, 10, 0, 12)
ToggleButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
ToggleButton.Text = "Auto Clicker: OFF"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.TextSize = 15
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.Parent = Frame

local ButtonCorner = Instance.new("UICorner")
ButtonCorner.CornerRadius = UDim.new(0, 6)
ButtonCorner.Parent = ToggleButton

-- Lógica para Ocultar e Mostrar a Janela
OpenButton.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
    if Frame.Visible then
        OpenButton.Text = "Menu [ - ]"
    else
        OpenButton.Text = "Menu [ + ]"
    end
end)

-- Função de Execução dos Cliques
local function executarClique()
    task.spawn(function()
        while _G.AutoClicker do
            local camera = workspace.CurrentCamera
            if camera then
                VirtualUser:Button1Down(Vector2.new(0, 0), camera.CFrame)
                task.wait(0.02)
                VirtualUser:Button1Up(Vector2.new(0, 0), camera.CFrame)
            end
            task.wait(_G.Intervalo)
        end
    end)
end

-- Lógica de Ativação do Auto Clicker
ToggleButton.MouseButton1Click:Connect(function()
    _G.AutoClicker = not _G.AutoClicker
    
    if _G.AutoClicker then
        ToggleButton.Text = "Auto Clicker: ON"
        ToggleButton.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
        executarClique()
    else
        ToggleButton.Text = "Auto Clicker: OFF"
        ToggleButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
    end
end)
