-- LocalScript em StarterGui

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local MarketplaceService = game:GetService("MarketplaceService")

local player = Players.LocalPlayer

-- GUI base
local playerGui = player:WaitForChild("PlayerGui")
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MusicGui"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.Parent = playerGui

-- FRAME PRINCIPAL
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 300, 0, 160)
mainFrame.Position = UDim2.new(0.5, -150, 0.3, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui
Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 10)

-- Função arrastável
local function makeDraggable(uiObject)
	local dragging = false
	local dragInput, dragStart, startPos

	local function onInputBegan(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			dragStart = input.Position
			startPos = uiObject.Position
			input.Changed:Connect(function()
				if input.UserInputState == Enum.UserInputState.End then
					dragging = false
				end
			end)
		end
	end

	local function onInputChanged(input)
		if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
			dragInput = input
		end
	end

	uiObject.InputBegan:Connect(onInputBegan)
	uiObject.InputChanged:Connect(onInputChanged)

	UserInputService.InputChanged:Connect(function(input)
		if input == dragInput and dragging then
			local delta = input.Position - dragStart
			uiObject.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)
end
makeDraggable(mainFrame)

-- Título
local titleLabel = Instance.new("TextLabel")
titleLabel.Text = "🎵 Music Remote Player"
titleLabel.Size = UDim2.new(1, -20, 0, 25)
titleLabel.Position = UDim2.new(0, 10, 0, 10)
titleLabel.BackgroundTransparency = 1
titleLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
titleLabel.TextScaled = true
titleLabel.Font = Enum.Font.SourceSansBold
titleLabel.TextXAlignment = Enum.TextXAlignment.Left
titleLabel.Parent = mainFrame

-- Caixa de texto
local inputBox = Instance.new("TextBox")
inputBox.Name = "MusicID"
inputBox.Size = UDim2.new(1, -20, 0, 40)
inputBox.Position = UDim2.new(0, 10, 0, 45)
inputBox.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
inputBox.TextColor3 = Color3.new(1, 1, 1)
inputBox.ClearTextOnFocus = false
inputBox.PlaceholderText = "Digite o ID da música (ex: 1848354532)"
inputBox.TextScaled = true
inputBox.Parent = mainFrame
Instance.new("UICorner", inputBox).CornerRadius = UDim.new(0, 8)

-- Botão Play
local playBtn = Instance.new("TextButton")
playBtn.Size = UDim2.new(1, -20, 0, 35)
playBtn.Position = UDim2.new(0, 10, 0, 95)
playBtn.Text = "▶️ Play"
playBtn.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
playBtn.TextColor3 = Color3.new(1, 1, 1)
playBtn.TextScaled = true
playBtn.Font = Enum.Font.SourceSansBold
playBtn.Parent = mainFrame
Instance.new("UICorner", playBtn).CornerRadius = UDim.new(0, 8)

-- Botão Fechar
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 25, 0, 25)
closeButton.Position = UDim2.new(1, -35, 0, 10)
closeButton.Text = "×"
closeButton.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
closeButton.TextColor3 = Color3.new(1, 1, 1)
closeButton.Font = Enum.Font.SourceSansBold
closeButton.Parent = mainFrame
Instance.new("UICorner", closeButton).CornerRadius = UDim.new(0, 6)
closeButton.MouseButton1Click:Connect(function()
	screenGui.Enabled = false
end)

-- Botão Minimizar
local minimizeButton = Instance.new("TextButton")
minimizeButton.Size = UDim2.new(0, 25, 0, 25)
minimizeButton.Position = UDim2.new(1, -65, 0, 10)
minimizeButton.Text = "-"
minimizeButton.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
minimizeButton.TextColor3 = Color3.new(1,1,1)
minimizeButton.Font = Enum.Font.SourceSansBold
minimizeButton.Parent = mainFrame
Instance.new("UICorner", minimizeButton).CornerRadius = UDim.new(0,6)

-- Mini frame (quadradinho) quando minimizado
local miniFrame = Instance.new("Frame")
miniFrame.Size = UDim2.new(0, 40, 0, 40)
miniFrame.Position = UDim2.new(0.5, -20, 0.05, 0)
miniFrame.BackgroundColor3 = Color3.fromRGB(50,50,50)
miniFrame.Visible = false
miniFrame.Parent = screenGui
Instance.new("UICorner", miniFrame).CornerRadius = UDim.new(0,6)

-- Botão para restaurar GUI
local reopenButton = Instance.new("TextButton")
reopenButton.Size = UDim2.new(1,0,1,0)
reopenButton.Position = UDim2.new(0,0,0,0)
reopenButton.Text = "+"
reopenButton.BackgroundTransparency = 1
reopenButton.TextColor3 = Color3.new(1,1,1)
reopenButton.Font = Enum.Font.SourceSansBold
reopenButton.TextScaled = true
reopenButton.Parent = miniFrame

-- Tornar miniFrame arrastável (incluindo o botão +)
local function makeDraggableWithChildren(uiObject)
	local dragging = false
	local dragInput, dragStart, startPos

	local function onInputBegan(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			dragStart = input.Position
			startPos = uiObject.Position
			input.Changed:Connect(function()
				if input.UserInputState == Enum.UserInputState.End then
					dragging = false
				end
			end)
		end
	end

	local function onInputChanged(input)
		if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
			dragInput = input
		end
	end

	uiObject.InputBegan:Connect(onInputBegan)
	uiObject.InputChanged:Connect(onInputChanged)

	for _, child in ipairs(uiObject:GetDescendants()) do
		if child:IsA("GuiObject") then
			child.InputBegan:Connect(onInputBegan)
			child.InputChanged:Connect(onInputChanged)
		end
	end

	UserInputService.InputChanged:Connect(function(input)
		if input == dragInput and dragging then
			local delta = input.Position - dragStart
			uiObject.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)
end
makeDraggableWithChildren(miniFrame)

-- Ações dos botões minimizar/restaurar
minimizeButton.MouseButton1Click:Connect(function()
	mainFrame.Visible = false
	miniFrame.Visible = true
end)

reopenButton.MouseButton1Click:Connect(function()
	mainFrame.Visible = true
	miniFrame.Visible = false
end)

-- Função remotes
local function playMusicRemote(tipo, id)
	local rep = ReplicatedStorage
	if tipo == "🚘" then
		rep:WaitForChild("RE"):WaitForChild("1Player1sCa1r"):FireServer("VehicleMusicPlay", id)
	elseif tipo == "🛹" then
		rep:WaitForChild("RE"):WaitForChild("1NoMoto1rVehicle1s"):FireServer("PickingScooterMusicText", id)
	elseif tipo == "🏍️" then
		rep:WaitForChild("RE"):WaitForChild("1Player1sCa1r"):FireServer("PickingCarMusicText", id)
	elseif tipo == "📻" then
		rep:WaitForChild("RE"):WaitForChild("PlayerToolEvent"):FireServer("ToolMusicText", id)
	elseif tipo == "🏡" then
		rep:WaitForChild("RE"):WaitForChild("1Player1sHous1e"):FireServer("PickHouseMusicText", id)
	end
end

-- Popup seleção
local function showSelectorPopup(title, options, callback)
	local popup = Instance.new("Frame")
	popup.Size = UDim2.new(0, 200, 0, 160)
	popup.Position = UDim2.new(0.5, -100, 0.5, -80)
	popup.BackgroundColor3 = Color3.fromRGB(45,45,45)
	popup.Parent = screenGui
	Instance.new("UICorner", popup).CornerRadius = UDim.new(0,10)

	local titleLbl = Instance.new("TextLabel")
	titleLbl.Text = title
	titleLbl.Size = UDim2.new(1,0,0,30)
	titleLbl.BackgroundTransparency = 1
	titleLbl.TextColor3 = Color3.new(1,1,1)
	titleLbl.TextScaled = true
	titleLbl.Font = Enum.Font.SourceSansBold
	titleLbl.Parent = popup

	for i, emoji in ipairs(options) do
		local btn = Instance.new("TextButton")
		btn.Size = UDim2.new(0.9,0,0,25)
		btn.Position = UDim2.new(0.05,0,0,30+(i-1)*27)
		btn.Text = emoji
		btn.BackgroundColor3 = Color3.fromRGB(70,70,70)
		btn.TextScaled = true
		btn.TextColor3 = Color3.new(1,1,1)
		btn.Font = Enum.Font.SourceSansBold
		btn.Parent = popup
		Instance.new("UICorner", btn).CornerRadius = UDim.new(0,6)

		btn.MouseButton1Click:Connect(function()
			callback(emoji)
			popup:Destroy()
		end)
	end
end

-- Clicar Play
playBtn.MouseButton1Click:Connect(function()
	local input = inputBox.Text:gsub("rbxassetid://","")
	local id = tonumber(input)
	if not id then
		warn("ID INVÁLIDO.")
		return
	end
	showSelectorPopup("Escolha o tipo:", {"🚘","🛹","🏍️","📻","🏡"}, function(tipo)
		playMusicRemote(tipo,id)
	end)
end)
