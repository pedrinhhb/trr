local player = game.Players.LocalPlayer

-- Remove GUI duplicada
if player.PlayerGui:FindFirstChild("SeedSpawnerGUI") then
	player.PlayerGui.SeedSpawnerGUI:Destroy()
end

local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "SeedSpawnerGUI"
gui.ResetOnSpawn = false

-- Fundo escuro para a área da interface (opcional para melhor visual)
local background = Instance.new("Frame", gui)
background.Size = UDim2.new(0, 320, 0, 180)
background.Position = UDim2.new(0.5, -160, 0.3, -90)
background.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
background.BorderSizePixel = 0
background.Active = true
background.Draggable = true -- para poder mover a GUI se quiser

-- Título
local title = Instance.new("TextLabel", background)
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.Text = "🌱 Spawn de Sementes"
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.TextColor3 = Color3.new(1, 1, 1)
title.BackgroundColor3 = Color3.fromRGB(34, 139, 34)
title.BorderSizePixel = 0

-- Botão para abrir dropdown
local dropdownButton = Instance.new("TextButton", background)
dropdownButton.Size = UDim2.new(1, -20, 0, 35)
dropdownButton.Position = UDim2.new(0, 10, 0, 50)
dropdownButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
dropdownButton.TextColor3 = Color3.new(1, 1, 1)
dropdownButton.Font = Enum.Font.Gotham
dropdownButton.TextSize = 18
dropdownButton.Text = "Escolher Semente ▼"
dropdownButton.AutoButtonColor = true

-- ScrollingFrame para opções (dropdown)
local scrollingFrame = Instance.new("ScrollingFrame", background)
scrollingFrame.Size = UDim2.new(1, -20, 0, 90)
scrollingFrame.Position = UDim2.new(0, 10, 0, 90)
scrollingFrame.CanvasSize = UDim2.new(0, 0, 0, 0) -- vai atualizar depois
scrollingFrame.ScrollBarThickness = 6
scrollingFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
scrollingFrame.BorderSizePixel = 0
scrollingFrame.Visible = false
scrollingFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y

local sementes = {
	"Tomato", "Carrot", "Cabbage", "Potato", "Onion", "Broccoli", "Corn",
	"Sunflower", "Daisy", "Rose", "Tulip", "Lily",
	"Apple", "Watermelon", "Pumpkin", "Banana", "Strawberry", "Grape",
	"Fire Flower", "Ice Flower", "Crystal Carrot", "Golden Tomato",
	"Glitched Seed", "Ghost Onion", "Radioactive Potato", "Rainbow Flower",
	"Candy Blossom", "Moon Blossom"
}

local optionButtons = {}

-- Criar botão para cada semente
for i, nome in ipairs(sementes) do
	local btn = Instance.new("TextButton", scrollingFrame)
	btn.Size = UDim2.new(1, 0, 0, 30)
	btn.Position = UDim2.new(0, 0, 0, (i-1)*30)
	btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
	btn.TextColor3 = Color3.new(1,1,1)
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 16
	btn.Text = nome
	btn.AutoButtonColor = true
	
	btn.MouseButton1Click:Connect(function()
		dropdownButton.Text = nome
		scrollingFrame.Visible = false
	end)
	
	table.insert(optionButtons, btn)
end

-- Ajustar CanvasSize conforme quantidade de opções
scrollingFrame.CanvasSize = UDim2.new(0, 0, 0, #sementes * 30)

-- Abrir/fechar dropdown ao clicar no botão
dropdownButton.MouseButton1Click:Connect(function()
	scrollingFrame.Visible = not scrollingFrame.Visible
end)

-- Botão para spawnar semente
local spawnButton = Instance.new("TextButton", background)
spawnButton.Size = UDim2.new(1, -20, 0, 40)
spawnButton.Position = UDim2.new(0, 10, 1, -50)
spawnButton.BackgroundColor3 = Color3.fromRGB(34, 139, 34)
spawnButton.TextColor3 = Color3.new(1, 1, 1)
spawnButton.Font = Enum.Font.GothamBold
spawnButton.TextSize = 20
spawnButton.Text = "🌱 Spawnar Semente"

-- Função que spawna a semente real (adaptar conforme o jogo)
local function spawnarSementeReal(nome)
	local seedFolder = game.ReplicatedStorage:FindFirstChild("Seeds")
	if not seedFolder then
		warn("Pasta Seeds não encontrada em ReplicatedStorage.")
		return
	end

	local original = seedFolder:FindFirstChild(nome)
	if not original then
		warn("Semente '" .. nome .. "' não encontrada na pasta Seeds.")
		return
	end

	local clone = original:Clone()
	local char = player.Character or player.CharacterAdded:Wait()
	local pos = char:WaitForChild("HumanoidRootPart").Position + Vector3.new(0, 2, -5)

	if clone:IsA("Model") and clone.PrimaryPart then
		clone:SetPrimaryPartCFrame(CFrame.new(pos))
	elseif clone:IsA("BasePart") then
		clone.Position = pos
	end

	clone.Parent = workspace
end

-- Ao clicar em spawnar
spawnButton.MouseButton1Click:Connect(function()
	local escolhido = dropdownButton.Text
	if table.find(sementes, escolhido) then
		spawnarSementeReal(escolhido)
	else
		warn("Escolha uma semente válida!")
	end
end)
