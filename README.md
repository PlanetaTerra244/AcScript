-- CONFIG OTIMIZAÇÃO
local ToDisable = {
	Textures = true,
	VisualEffects = true,
	Parts = true,
	Particles = true,
	Sky = true
}
local Stuff = {}

for _, v in next, game:GetDescendants() do
	if ToDisable.Parts and (v:IsA("Part") or v:IsA("UnionOperation") or v:IsA("BasePart")) then
		v.Material = Enum.Material.SmoothPlastic
		table.insert(Stuff, v)
	end
	if ToDisable.Particles and (v:IsA("ParticleEmitter") or v:IsA("Smoke") or v:IsA("Explosion") or v:IsA("Sparkles") or v:IsA("Fire")) then
		v.Enabled = false
		table.insert(Stuff, v)
	end
	if ToDisable.VisualEffects and (v:IsA("BloomEffect") or v:IsA("BlurEffect") or v:IsA("DepthOfFieldEffect") or v:IsA("SunRaysEffect")) then
		v.Enabled = false
		table.insert(Stuff, v)
	end
	if ToDisable.Textures and (v:IsA("Decal") or v:IsA("Texture")) then
		v.Texture = ""
		table.insert(Stuff, v)
	end
	if ToDisable.Sky and v:IsA("Sky") then
		v.Parent = nil
		table.insert(Stuff, v)
	end
end
local player = game.Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local clickInterval = 0.05
local autoClicking = false

-- GUI CRIAÇÃO
local gui = Instance.new("ScreenGui")
gui.Name = "AutoClikerGui"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 220, 0, 120)
frame.Position = UDim2.new(0.5, -110, 0.5, -60)
frame.BackgroundColor3 = Color3.fromRGB(35,35,35)
frame.BorderSizePixel = 0
frame.Active = true
frame.AnchorPoint = Vector2.new(0.5,0.5)
frame.Visible = true
frame.Parent = gui

-- Sombra/Glow sutil
local uiStroke = Instance.new("UIStroke")
uiStroke.Color = Color3.fromRGB(180,255,80)
uiStroke.Thickness = 2
uiStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
uiStroke.Parent = frame

local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0,12)
uiCorner.Parent = frame

-- Título
local title = Instance.new("TextLabel")
title.Text = "Auto Cliker"
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.BackgroundTransparency = 1
title.Position = UDim2.new(0,0,0,6)
title.Size = UDim2.new(1,0,0,30)
title.TextColor3 = Color3.fromRGB(180, 255, 80)
title.Parent = frame

-- Subtítulo
local subtitle = Instance.new("TextLabel")
subtitle.Text = "from Mario and Biel"
subtitle.Font = Enum.Font.Gotham
subtitle.TextSize = 15
subtitle.BackgroundTransparency = 1
subtitle.Position = UDim2.new(0,0,0,34)
subtitle.Size = UDim2.new(1,0,0,20)
subtitle.TextColor3 = Color3.fromRGB(200, 200, 200)
subtitle.Parent = frame

-- Botão de AutoClick
local clickBtn = Instance.new("TextButton")
clickBtn.Text = "Ligar AutoClicker"
clickBtn.Font = Enum.Font.GothamMedium
clickBtn.TextSize = 18
clickBtn.Position = UDim2.new(0.1,0,0.6,0)
clickBtn.Size = UDim2.new(0.8,0,0.25,0)
clickBtn.BackgroundColor3 = Color3.fromRGB(80, 180, 80)
clickBtn.TextColor3 = Color3.new(1,1,1)
clickBtn.Parent = frame

local btnCorner = Instance.new("UICorner")
btnCorner.CornerRadius = UDim.new(0,10)
btnCorner.Parent = clickBtn

-- Botão Minimizar
local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Text = "_"
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.TextSize = 22
minimizeBtn.Position = UDim2.new(1, -35, 0, 3)
minimizeBtn.Size = UDim2.new(0,32,0,24)
minimizeBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
minimizeBtn.TextColor3 = Color3.fromRGB(200,200,200)
minimizeBtn.Parent = frame

local minCorner = Instance.new("UICorner")
minCorner.CornerRadius = UDim.new(0,8)
minCorner.Parent = minimizeBtn

-- Botão pequeno "AC" aparece quando minimizar (agora uma bola)
local acBtn = Instance.new("TextButton")
acBtn.Text = "AC"
acBtn.Font = Enum.Font.GothamBold
acBtn.TextSize = 16
acBtn.BackgroundColor3 = Color3.fromRGB(35,35,35)
acBtn.TextColor3 = Color3.fromRGB(180,255,80)
acBtn.BorderSizePixel = 0
acBtn.Size = UDim2.new(0, 38, 0, 38) -- largura e altura iguais para bola
acBtn.Position = UDim2.new(0.5, -19, 0.5, -19) -- posição centralizada
acBtn.Visible = false
acBtn.Parent = gui

local acCorner = Instance.new("UICorner")
acCorner.CornerRadius = UDim.new(1, 0) -- raio máximo para círculo perfeito
acCorner.Parent = acBtn

-- Minimizar e restaurar
local isMinimized = false

minimizeBtn.MouseButton1Click:Connect(function()
	isMinimized = not isMinimized
	frame.Visible = not isMinimized
	acBtn.Visible = isMinimized
end)

acBtn.MouseButton1Click:Connect(function()
	isMinimized = false
	frame.Visible = true
	acBtn.Visible = false
end)

clickBtn.MouseButton1Click:Connect(function()
	autoClicking = not autoClicking
	clickBtn.Text = autoClicking and "Desligar AutoClicker" or "Ligar AutoClicker"
	clickBtn.BackgroundColor3 = autoClicking and Color3.fromRGB(180,80,80) or Color3.fromRGB(80,180,80)
end)

-- Animação hover nos botões
local function hoverBtn(btn, enterColor, leaveColor)
	btn.MouseEnter:Connect(function() btn.BackgroundColor3 = enterColor end)
	btn.MouseLeave:Connect(function() btn.BackgroundColor3 = leaveColor end)
end
hoverBtn(clickBtn, Color3.fromRGB(140,220,140), Color3.fromRGB(80,180,80))
hoverBtn(minimizeBtn, Color3.fromRGB(100,100,100), Color3.fromRGB(60,60,60))
hoverBtn(acBtn, Color3.fromRGB(80,180,80), Color3.fromRGB(35,35,35))

-- DRAG ANYWHERE NA TELA
local function makeDraggable(object)
	local dragging, dragInput, mousePos, framePos

	object.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			mousePos = input.Position
			framePos = object.Position
			input.Changed:Connect(function()
				if input.UserInputState == Enum.UserInputState.End then
					dragging = false
				end
			end)
		end
	end)

	object.InputChanged:Connect(function(input)
		if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
			local delta = input.Position - mousePos
			object.Position = UDim2.new(framePos.X.Scale, framePos.X.Offset + delta.X, framePos.Y.Scale, framePos.Y.Offset + delta.Y)
		end
	end)
end

makeDraggable(frame)
makeDraggable(acBtn)

-- AUTO CLICKER LOOP
RunService.RenderStepped:Connect(function()
	if autoClicking then
		local char = player.Character
		if char then
			local tool = char:FindFirstChildOfClass("Tool")
			if tool then
				tool:Activate()
			end
		end
		wait(clickInterval)
	end
end)
