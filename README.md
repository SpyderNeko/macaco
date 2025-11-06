-- LocalScript (colocar em StarterPlayerScripts)
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Limpar se já existir (útil para reaplicar o script sem duplicar GUIs)
if playerGui:FindFirstChild("SeedGeneratorGui") then
    playerGui.SeedGeneratorGui:Destroy()
end

-- ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "SeedGeneratorGui"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui

-- Frame principal
local frame = Instance.new("Frame")
frame.Name = "SeedGenerator by SpyderNeko"
frame.Size = UDim2.new(0, 300, 0, 140)
frame.Position = UDim2.new(0.5, -150, 0.2, 0)
frame.AnchorPoint = Vector2.new(0.5, 0)
frame.BackgroundColor3 = Color3.fromRGB(45, 120, 180)
frame.BorderSizePixel = 0
frame.Parent = screenGui

local uiCorner = Instance.new("UICorner", frame)
uiCorner.CornerRadius = UDim.new(0, 10)
local uiStroke = Instance.new("UIStroke", frame)
uiStroke.Thickness = 1
uiStroke.Transparency = 0.6

-- Título
local title = Instance.new("TextLabel")
title.Name = "Title"
title.Size = UDim2.new(1, -40, 0, 30)
title.Position = UDim2.new(0, 10, 0, 6)
title.BackgroundTransparency = 1
title.Text = "Seed Generator by SpyderNeko"
title.Font = Enum.Font.SourceSansBold
title.TextSize = 20
title.TextColor3 = Color3.fromRGB(255,255,255)
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = frame

-- Botão fechar (X)
local closeBtn = Instance.new("TextButton")
closeBtn.Name = "CloseButton"
closeBtn.Size = UDim2.new(0, 28, 0, 28)
closeBtn.Position = UDim2.new(1, -36, 0, 6)
closeBtn.AnchorPoint = Vector2.new(0, 0)
closeBtn.BackgroundTransparency = 0
closeBtn.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
closeBtn.Text = "X"
closeBtn.Font = Enum.Font.SourceSansBold
closeBtn.TextSize = 18
closeBtn.TextColor3 = Color3.fromRGB(255,255,255)
closeBtn.Parent = frame
local closeCorner = Instance.new("UICorner", closeBtn)
closeCorner.CornerRadius = UDim.new(0, 6)

-- Botão Generate
local button = Instance.new("TextButton")
button.Name = "GenerateStarfruit"
button.Size = UDim2.new(0.9, 0, 0, 48)
button.Position = UDim2.new(0.05, 0, 0, 52)
button.Text = "Generate Starfruit"
button.Font = Enum.Font.SourceSans
button.TextSize = 18
button.Parent = frame
button.BackgroundColor3 = Color3.fromRGB(255,255,255)
button.TextColor3 = Color3.fromRGB(40,40,40)
local btnCorner = Instance.new("UICorner", button)
btnCorner.CornerRadius = UDim.new(0, 8)

-- Mensagem de status (abaixo do botão)
local status = Instance.new("TextLabel")
status.Name = "Status"
status.Size = UDim2.new(0.9, 0, 0, 18)
status.Position = UDim2.new(0.05, 0, 0, 108)
status.BackgroundTransparency = 1
status.Text = ""
status.Font = Enum.Font.SourceSansItalic
status.TextSize = 14
status.TextColor3 = Color3.fromRGB(240,240,240)
status.TextXAlignment = Enum.TextXAlignment.Left
status.Parent = frame

-- ImageButton para reabrir (fica fora do frame, sempre visível quando frame estiver fechado)
local openButton = Instance.new("ImageButton")
openButton.Name = "OpenSeedGeneratorButton"
openButton.Size = UDim2.new(0, 48, 0, 48)
openButton.Position = UDim2.new(0, 12, 1, -60) -- canto inferior esquerdo
openButton.AnchorPoint = Vector2.new(0, 0)
openButton.BackgroundTransparency = 0
openButton.BackgroundColor3 = Color3.fromRGB(45, 120, 180)
openButton.Parent = screenGui
openButton.Image = "" -- substitua por "rbxassetid://ID_DA_IMAGEM" se quiser um ícone
local openCorner = Instance.new("UICorner", openButton)
openCorner.CornerRadius = UDim.new(0, 8)
openButton.Visible = false -- começa escondido porque o frame inicia aberto

-- Label pequeno no botão de abrir (opcional)
local openLabel = Instance.new("TextLabel")
openLabel.Parent = openButton
openLabel.Size = UDim2.new(1, 0, 1, 0)
openLabel.BackgroundTransparency = 1
openLabel.Text = "Seeds"
openLabel.Font = Enum.Font.SourceSansBold
openLabel.TextSize = 12
openLabel.TextColor3 = Color3.fromRGB(255,255,255)

-- Função para clonar o seed (uso em ambiente de desenvolvimento/studio)
local function giveStarfruitSeed()
    local assets = ReplicatedStorage:FindFirstChild("Assets")
    local backpack = player:FindFirstChild("Backpack") or player:WaitForChild("Backpack")
    if assets and assets:FindFirstChild("Seeds") and assets.Seeds:FindFirstChild("Starfruit Seed") then
        local seedTemplate = assets.Seeds["Starfruit Seed"]
        local clone = seedTemplate:Clone()
        clone.Parent = backpack
        return true, 'The plant "[Starfruit]" was successfully generated.'
    else
        return false, "Starfruit Seed not found in ReplicatedStorage.Assets.Seeds"
    end
end

-- Conexão do botão Generate
button.MouseButton1Click:Connect(function()
    button.AutoButtonColor = false
    button.Text = "Processing..."
    status.Text = ""
    local ok, msg = pcall(giveStarfruitSeed)
    if ok and msg then
        status.Text = msg
    else
        status.Text = tostring(msg or "Erro ao gerar Seed")
    end
    wait(1.6)
    button.Text = "Generate Starfruit"
    button.AutoButtonColor = true
end)

-- Fechar o painel
closeBtn.MouseButton1Click:Connect(function()
    frame.Visible = false
    openButton.Visible = true
end)

-- Abrir pelo imagebutton
openButton.MouseButton1Click:Connect(function()
    frame.Visible = true
    openButton.Visible = false
end)

-- ---------------------
-- Sistema de drag (mouse + touch)
-- ---------------------
local dragging = false
local dragInput, dragStart, startPos

local function updatePosition(input)
    if not dragging then return end
    local delta = input.Position - dragStart
    local newX = startPos.X.Offset + delta.X
    local newY = startPos.Y.Offset + delta.Y

    -- clamp para manter dentro da viewport
    local screenW = workspace.CurrentCamera.ViewportSize.X
    local screenH = workspace.CurrentCamera.ViewportSize.Y
    local fw = frame.AbsoluteSize.X
    local fh = frame.AbsoluteSize.Y

    if newX < 0 then newX = 0 end
    if newY < 0 then newY = 0 end
    if newX + fw > screenW then newX = screenW - fw end
    if newY + fh > screenH then newY = screenH - fh end

    frame.Position = UDim2.new(0, newX, 0, newY)
end

local function inputBegan(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        -- guardar posição inicial em AbsolutePosition/AbsoluteSize
        startPos = frame.Position
        dragInput = input

        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end

-- Bind input events ao título (apenas título arrasta para evitar clique em botões)
title.InputBegan:Connect(function(input)
    inputBegan(input)
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        updatePosition(input)
    end
end)

-- Para touch devices (suporte adicional)
title.TouchPan:Connect(function() end) -- placeholder (roblox touch pan pode variar; InputChanged cobre ambos)

-- Garante que o botão de abrir esteja oculto quando frame for aberto por código
frame:GetPropertyChangedSignal("Visible"):Connect(function()
    if frame.Visible then
        openButton.Visible = false
    else
        openButton.Visible = true
    end
end)

-- Fim do script
