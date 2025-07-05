-- ESP com Team Check - Simples e funcional
-- A interface é feita com Drawing API (nativo)

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

local ESPEnabled = true
local TeamCheck = true -- Ativa a verificação de times
local ESPColorEnemy = Color3.fromRGB(255, 0, 0)
local ESPColorAlly = Color3.fromRGB(0, 255, 0)

local ESPObjects = {}

-- Função para criar a UI do ESP
local function CreateESPBox(player)
	if player == LocalPlayer then return end
	local box = Drawing.new("Text")
	box.Visible = false
	box.Center = true
	box.Outline = true
	box.Size = 13
	box.Color = ESPColorEnemy
	box.Text = ""
	box.Font = Drawing.Fonts.UI
	ESPObjects[player] = box
end

-- Remove ESP quando o jogador sai
Players.PlayerRemoving:Connect(function(player)
	if ESPObjects[player] then
		ESPObjects[player]:Remove()
		ESPObjects[player] = nil
	end
end)

-- Cria ESP para jogadores já presentes
for _, player in pairs(Players:GetPlayers()) do
	CreateESPBox(player)
end

-- Cria ESP quando um novo jogador entra
Players.PlayerAdded:Connect(CreateESPBox)

-- Atualiza o ESP constantemente
RunService.RenderStepped:Connect(function()
	if not ESPEnabled then
		for _, box in pairs(ESPObjects) do
			box.Visible = false
		end
		return
	end

	for player, box in pairs(ESPObjects) do
		local character = player.Character
		local humanoidRootPart = character and character:FindFirstChild("HumanoidRootPart")
		if character and humanoidRootPart and player.Team and LocalPlayer.Team then
			if TeamCheck and player.Team == LocalPlayer.Team then
				box.Visible = false
			else
				local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(humanoidRootPart.Position)
				if onScreen then
					box.Position = Vector2.new(pos.X, pos.Y)
					box.Text = player.Name
					box.Color = (player.Team == LocalPlayer.Team) and ESPColorAlly or ESPColorEnemy
					box.Visible = true
				else
					box.Visible = false
				end
			end
		else
			box.Visible = false
		end
	end
end)

-- Toggle de exemplo (pressione "P" para ativar/desativar o ESP)
local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(function(input, gpe)
	if gpe then return end
	if input.KeyCode == Enum.KeyCode.P then
		ESPEnabled = not ESPEnabled
	end
end)

print("ESP carregado com sucesso.")# Berlan
