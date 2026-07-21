 -- Serviços do Roblox
local CoreGui = game:GetService("CoreGui")
local VirtualInputManager = game:GetService("VirtualInputManager")
local Players = game:GetService("Players")

local player = Players.LocalPlayer

-- Limpa interfaces antigas
if CoreGui:FindFirstChild("AutoClickerGui") then
    CoreGui.AutoClickerGui:Destroy()
end

-- Estado Global
_G.AutoClicker = false
_G.Intervalo = 0.05 -- Intervalo padrão

-- Tela Principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "AutoClickerGui"
ScreenGui.Parent = CoreGui

-- Botão Ocultar/Exibir (Arrastável)
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

-- Janela Principal (Aumentada para caber a opção de tempo)
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 180, 0, 110)
Frame.Position = UDim2.new(0.05, 0, 0.4, 0)
Frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Frame.Active = true
Frame.Draggable = true
Frame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = Frame

-- Botão Liga/Desliga
local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(0, 160, 0, 40)
ToggleButton.Position = UDim2.new(0, 10, 0, 10)
ToggleButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
ToggleButton.Text = "Auto Clicker: OFF"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.TextSize = 15
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.Parent = Frame

local ButtonCorner = Instance.new("UICorner")
ButtonCorner.CornerRadius = UDim.new(0, 6)
ButtonCorner.Parent = ToggleButton

-- Campo de Texto para Ajustar o Intervalo
local IntervalBox = Instance.new("TextBox")
IntervalBox.Size = UDim2.new(0, 160, 0, 40)
IntervalBox.Position = UDim2.new(0, 10, 0, 58)
IntervalBox.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
IntervalBox.Text = "Intervalo: " .. tostring(_G.Intervalo) .. "s"
IntervalBox.TextColor3 = Color3.fromRGB(255, 255, 255)
IntervalBox.TextSize = 13
IntervalBox.Font = Enum.Font.SourceSans
IntervalBox.ClearTextOnFocus = true
IntervalBox.Parent = Frame

local BoxCorner = Instance.new("UICorner")
BoxCorner.CornerRadius = UDim.new(0, 6)
BoxCorner.Parent = IntervalBox

-- Atualiza o valor do intervalo digitado pelo usuário
IntervalBox.FocusLost:Connect(function(enterPressed)
    local valor = tonumber(IntervalBox.Text)
    if valor and valor > 0 then
        _G.Intervalo = valor
        IntervalBox.Text = "Intervalo: " .. tostring(_G.Intervalo) .. "s"
    else
        IntervalBox.Text = "Intervalo: " .. tostring(_G.Intervalo) .. "s"
    end
end)

-- Alternar visibilidade
OpenButton.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
    OpenButton.Text = Frame.Visible and "Menu [ - ]" or "Menu [ + ]"
end)

-- Função do Auto Clicker (EXATAMENTE a mesma versão anterior)
local function executarClique()
    task.spawn(function()
        while _G.AutoClicker do
            -- 1. Se estiver segurando um item, ativa
            local char = player.Character
            if char then
                local tool = char:FindFirstChildOfClass("Tool")
                if tool then
                    tool:Activate()
                end
            end

            -- 2. Clique FÍSICO de Toque Mobile no centro da tela
            local camera = workspace.CurrentCamera
            if camera then
                local x = camera.ViewportSize.X / 2
                local y = camera.ViewportSize.Y / 2

                pcall(function()
                    VirtualInputManager:SendTouchEvent(12345, 0, x, y)
                    VirtualInputManager:SendTouchEvent(12345, 2, x, y)
                end)
            end

            task.wait(_G.Intervalo)
        end
    end)
end

-- Liga / Desliga
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
