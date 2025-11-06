-- Script seguro e educativo: "Plant Duplicator"
-- Este script apenas demonstra como criar um botão e mostrar uma mensagem ao clicar.

-- Criar GUI
local ScreenGui = Instance.new("ScreenGui")
local Button = Instance.new("TextButton")

ScreenGui.Name = "PlantDuplicator"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

Button.Parent = ScreenGui
Button.Size = UDim2.new(0, 200, 0, 50)
Button.Position = UDim2.new(0.5, -100, 0.5, -25)
Button.Text = "Duplicate Plant"
Button.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
Button.TextScaled = true

-- Função de clique (somente demonstração)
Button.MouseButton1Click:Connect(function()
	-- Apenas mostra uma mensagem no console e na tela
	local message = 'The plant "[6.9 kg] Mango" was successfully duplicated (simulation).'
	warn(message)
	
	local MessageLabel = Instance.new("TextLabel")
	MessageLabel.Parent = ScreenGui
	MessageLabel.Size = UDim2.new(0, 400, 0, 100)
	MessageLabel.Position = UDim2.new(0.5, -200, 0.6, 0)
	MessageLabel.Text = message
	MessageLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
	MessageLabel.BackgroundTransparency = 1
	MessageLabel.TextScaled = true
	
	task.wait(3)
	MessageLabel:Destroy()
end)
