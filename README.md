-- Configurações iniciais
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humRoot = character:WaitForChild("HumanoidRootPart")
local hum = character:WaitForChild("Humanoid")

-- Variável para controlar o estado do auto farm
local isAutoFarmEnabled = false

-- Função para caminhar até um ponto específico
local function walkToPosition(targetPosition)
    hum:MoveTo(targetPosition)
end

-- Função para pegar o saco de lixo
local function pickUpTrash()
    -- Localiza o objeto "Saco de lixo"
    local trashObject = workspace:FindFirstChild("Saco de lixo")
    if trashObject then
        -- Simula o clique para pegar o lixo
        fireclickdetector(trashObject:FindFirstChildOfClass("ClickDetector"))
    end
end

-- Função para entregar o lixo
local function deliverTrash()
    -- Localiza o NPC ou objeto de entrega de lixo
    local deliveryNPC = workspace:FindFirstChild("Entregar lixo")
    if deliveryNPC then
        -- Simula o clique para entregar o lixo
        fireclickdetector(deliveryNPC:FindFirstChildOfClass("ClickDetector"))
    end
end

-- Loop principal do auto farm
local function startAutoFarm()
    while isAutoFarmEnabled do
        -- Caminha até o local do lixo
        walkToPosition(workspace["Saco de lixo"].Position)

        -- Espera chegar ao local do lixo
        repeat wait() until (humRoot.Position - workspace["Saco de lixo"].Position).Magnitude < 5

        -- Pega o saco de lixo
        pickUpTrash()

        -- Caminha até o NPC de entrega
        walkToPosition(workspace["Entregar lixo"].Position)

        -- Espera chegar ao NPC de entrega
        repeat wait() until (humRoot.Position - workspace["Entregar lixo"].Position).Magnitude < 5

        -- Entrega o lixo
        deliverTrash()

        -- Aguarda um pouco antes de repetir o ciclo
        wait(2) -- Ajuste esse tempo conforme necessário
    end
end

-- Criação do Toggle UI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AutoFarmUI"
screenGui.Parent = player.PlayerGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 150, 0, 50)
frame.Position = UDim2.new(1, -170, 0, 50) -- Posição no canto superior direito
frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
frame.BackgroundTransparency = 0.8
frame.Parent = screenGui

local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(1, 0, 1, 0)
toggleButton.Text = "Auto Farm: OFF"
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.Font = Enum.Font.ArialBold
toggleButton.Parent = frame

-- Evento de clique no botão
toggleButton.MouseButton1Click:Connect(function()
    isAutoFarmEnabled = not isAutoFarmEnabled
    if isAutoFarmEnabled then
        toggleButton.Text = "Auto Farm: ON"
        startAutoFarm()
    else
        toggleButton.Text = "Auto Farm: OFF"
    end
end)

-- Inicialização
print("Auto Farm UI criado. Use o botão na tela para ativar/desativar.")
