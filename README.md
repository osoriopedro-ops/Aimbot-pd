# Aimbot-pd
Aimbot for pc and mobile
-- PDR AIM V3 - PC
-- Uso: seu próprio jogo no Roblox Studio
-- Coloque em StarterPlayer > StarterPlayerScripts

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

local Settings = {
	Enabled = false,

	FOV = 80,
	MinFOV = 20,
	MaxFOV = 300,
	FOVStep = 10,

	PredictionEnabled = true,
	Prediction = 0.12,
	MinPrediction = 0,
	MaxPrediction = 1,
	PredictionStep = 0.02,

	TeamCheck = false,
	WallCheck = false,

	MaxDistance = 300,
	Smoothness = 0.18
}

local minimized = false
local menuVisible = true

--==================================================
-- GUI
--==================================================

local gui = Instance.new("ScreenGui")
gui.Name = "PDRAimV3_PC"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

--==================================================
-- FOV
--==================================================

local fov = Instance.new("Frame")
fov.Name = "FOV"
fov.AnchorPoint = Vector2.new(.5,.5)
fov.Position = UDim2.fromScale(.5,.5)
fov.Size = UDim2.fromOffset(Settings.FOV * 2, Settings.FOV * 2)
fov.BackgroundTransparency = 1
fov.Parent = gui

local fovCorner = Instance.new("UICorner")
fovCorner.CornerRadius = UDim.new(1,0)
fovCorner.Parent = fov

local fovStroke = Instance.new("UIStroke")
fovStroke.Thickness = 2
fovStroke.Parent = fov

--==================================================
-- MAIN
--==================================================

local main = Instance.new("Frame")
main.Name = "PDR_AIM"
main.Size = UDim2.fromOffset(230,180)
main.Position = UDim2.fromOffset(30,150)
main.BackgroundColor3 = Color3.fromRGB(10,10,10)
main.BorderSizePixel = 0
main.Active = true
main.Parent = gui

local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0,10)
mainCorner.Parent = main

--==================================================
-- HEADER
--==================================================

local header = Instance.new("Frame")
header.Size = UDim2.new(1,0,0,38)
header.BackgroundTransparency = 1
header.Active = true
header.Parent = main

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,-80,1,0)
title.Position = UDim2.fromOffset(8,0)
title.BackgroundTransparency = 1
title.Text = "PDR AIM V3"
title.TextColor3 = Color3.new(1,1,1)
title.TextSize = 17
title.Font = Enum.Font.GothamBold
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = header

--==================================================
-- MINIMIZAR
--==================================================

local minimize = Instance.new("TextButton")
minimize.Size = UDim2.fromOffset(28,28)
minimize.Position = UDim2.new(1,-67,0,5)
minimize.Text = "—"
minimize.TextSize = 19
minimize.TextColor3 = Color3.new(1,1,1)
minimize.BackgroundColor3 = Color3.fromRGB(45,45,45)
minimize.BorderSizePixel = 0
minimize.Parent = header

local minCorner = Instance.new("UICorner")
minCorner.CornerRadius = UDim.new(0,7)
minCorner.Parent = minimize

--==================================================
-- FECHAR
--==================================================

local close = Instance.new("TextButton")
close.Size = UDim2.fromOffset(28,28)
close.Position = UDim2.new(1,-34,0,5)
close.Text = "×"
close.TextSize = 21
close.TextColor3 = Color3.new(1,1,1)
close.BackgroundColor3 = Color3.fromRGB(45,45,45)
close.BorderSizePixel = 0
close.Parent = header

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0,7)
closeCorner.Parent = close

--==================================================
-- SCROLL
--==================================================

local scroll = Instance.new("ScrollingFrame")
scroll.Size = UDim2.new(1,-10,1,-43)
scroll.Position = UDim2.fromOffset(5,40)
scroll.BackgroundTransparency = 1
scroll.BorderSizePixel = 0
scroll.ScrollBarThickness = 6
scroll.ScrollBarImageTransparency = .1
scroll.CanvasSize = UDim2.new(0,0,0,450)
scroll.ScrollingDirection = Enum.ScrollingDirection.Y
scroll.Parent = main

local layout = Instance.new("UIListLayout")
layout.Padding = UDim.new(0,5)
layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
layout.Parent = scroll

local function createButton(text)

	local b = Instance.new("TextButton")

	b.Size = UDim2.new(1,-15,0,35)
	b.Text = text
	b.TextSize = 14
	b.Font = Enum.Font.Gotham
	b.TextColor3 = Color3.new(1,1,1)
	b.BackgroundColor3 = Color3.fromRGB(38,38,38)
	b.BorderSizePixel = 0
	b.Parent = scroll

	local c = Instance.new("UICorner")
	c.CornerRadius = UDim.new(0,7)
	c.Parent = b

	return b
end

local status = Instance.new("TextLabel")
status.Size = UDim2.new(1,-15,0,28)
status.BackgroundTransparency = 1
status.TextColor3 = Color3.fromRGB(190,190,190)
status.TextSize = 12
status.Parent = scroll

local aim = createButton("AIM: OFF")
local fovMinus = createButton("FOV  -")
local fovPlus = createButton("FOV  +")
local prediction = createButton("PREDICTION: ON")
local predMinus = createButton("PREDICT  -")
local predPlus = createButton("PREDICT  +")
local team = createButton("TEAM CHECK: OFF")
local wall = createButton("WALL CHECK: OFF")
local distance = createButton("DISTÂNCIA: 300")
local smooth = createButton("SUAVIDADE: 18%")

--==================================================
-- ATUALIZAÇÃO
--==================================================

local function update()

	fov.Size = UDim2.fromOffset(
		Settings.FOV * 2,
		Settings.FOV * 2
	)

	status.Text =
		"FOV: "..Settings.FOV..
		" | Prediction: "..
		string.format("%.2f",Settings.Prediction)

end

--==================================================
-- BOTÕES
--==================================================

aim.MouseButton1Click:Connect(function()

	Settings.Enabled = not Settings.Enabled

	aim.Text =
		Settings.Enabled
		and "AIM: ON"
		or "AIM: OFF"

end)

fovMinus.MouseButton1Click:Connect(function()

	Settings.FOV = math.max(
		Settings.MinFOV,
		Settings.FOV - Settings.FOVStep
	)

	update()

end)

fovPlus.MouseButton1Click:Connect(function()

	Settings.FOV = math.min(
		Settings.MaxFOV,
		Settings.FOV + Settings.FOVStep
	)

	update()

end)

prediction.MouseButton1Click:Connect(function()

	Settings.PredictionEnabled =
		not Settings.PredictionEnabled

	prediction.Text =
		Settings.PredictionEnabled
		and "PREDICTION: ON"
		or "PREDICTION: OFF"

end)

predMinus.MouseButton1Click:Connect(function()

	Settings.Prediction = math.max(
		Settings.MinPrediction,
		Settings.Prediction - Settings.PredictionStep
	)

	update()

end)

predPlus.MouseButton1Click:Connect(function()

	Settings.Prediction = math.min(
		Settings.MaxPrediction,
		Settings.Prediction + Settings.PredictionStep
	)

	update()

end)

team.MouseButton1Click:Connect(function()

	Settings.TeamCheck = not Settings.TeamCheck

	team.Text =
		Settings.TeamCheck
		and "TEAM CHECK: ON"
		or "TEAM CHECK: OFF"

end)

wall.MouseButton1Click:Connect(function()

	Settings.WallCheck = not Settings.WallCheck

	wall.Text =
		Settings.WallCheck
		and "WALL CHECK: ON"
		or "WALL CHECK: OFF"

end)

distance.MouseButton1Click:Connect(function()

	Settings.MaxDistance += 50

	if Settings.MaxDistance > 500 then
		Settings.MaxDistance = 100
	end

	distance.Text =
		"DISTÂNCIA: "..Settings.MaxDistance

end)

smooth.MouseButton1Click:Connect(function()

	Settings.Smoothness += .05

	if Settings.Smoothness > .5 then
		Settings.Smoothness = .05
	end

	smooth.Text =
		"SUAVIDADE: "..
		math.floor(Settings.Smoothness * 100).."%"	

end)

--==================================================
-- ARRASTAR COM MOUSE
--==================================================

local dragging = false
local dragStart
local startPosition

header.InputBegan:Connect(function(input)

	if input.UserInputType ==
		Enum.UserInputType.MouseButton1 then

		dragging = true
		dragStart = input.Position
		startPosition = main.Position

		input.Changed:Connect(function()

			if input.UserInputState ==
				Enum.UserInputState.End then

				dragging = false

			end

		end)

	end

end)

UserInputService.InputChanged:Connect(function(input)

	if not dragging then
		return
	end

	if input.UserInputType ==
		Enum.UserInputType.MouseMovement then

		local delta =
			input.Position - dragStart

		main.Position = UDim2.new(
			startPosition.X.Scale,
			startPosition.X.Offset + delta.X,

			startPosition.Y.Scale,
			startPosition.Y.Offset + delta.Y
		)

	end

end)

--==================================================
-- MINIMIZAR
--==================================================

minimize.MouseButton1Click:Connect(function()

	minimized = true

	scroll.Visible = false

	main.Size = UDim2.fromOffset(230,45)

end)

--==================================================
-- REABRIR
--==================================================

local reopen = Instance.new("TextButton")
reopen.Size = UDim2.fromOffset(55,55)
reopen.Position = UDim2.fromOffset(20,100)
reopen.Text = "PDR"
reopen.TextSize = 15
reopen.Font = Enum.Font.GothamBold
reopen.TextColor3 = Color3.new(1,1,1)
reopen.BackgroundColor3 = Color3.fromRGB(15,15,15)
reopen.BorderSizePixel = 0
reopen.Visible = false
reopen.Parent = gui

local reopenCorner = Instance.new("UICorner")
reopenCorner.CornerRadius = UDim.new(1,0)
reopenCorner.Parent = reopen

reopen.MouseButton1Click:Connect(function()

	minimized = false

	main.Size = UDim2.fromOffset(230,180)
	scroll.Visible = true
	reopen.Visible = false

end)

--==================================================
-- FECHAR / REABRIR
--==================================================

close.MouseButton1Click:Connect(function()

	main.Visible = false
	fov.Visible = false
	reopen.Visible = true

end)

--==================================================
-- RIGHT SHIFT
--==================================================

UserInputService.InputBegan:Connect(function(input, processed)

	if processed then
		return
	end

	if input.KeyCode == Enum.KeyCode.RightShift then

		menuVisible = not menuVisible

		main.Visible = menuVisible
		fov.Visible = menuVisible

	end

end)

--==================================================
-- LOCK-ON PARA NPCs DO SEU JOGO
--==================================================

local function canSeeTarget(root)

	if not Settings.WallCheck then
		return true
	end

	local origin = camera.CFrame.Position
	local direction = root.Position - origin

	local params = RaycastParams.new()

	params.FilterType =
		Enum.RaycastFilterType.Exclude

	params.FilterDescendantsInstances = {
		player.Character
	}

	local result =
		workspace:Raycast(
			origin,
			direction,
			params
		)

	if not result then
		return true
	end

	return result.Instance:IsDescendantOf(root.Parent)

end

local function getTarget()

	local bestTarget = nil
	local bestDistance = Settings.FOV

	for _, object in ipairs(
		workspace:GetDescendants()
	) do

		if object:IsA("Model")
			and object ~= player.Character then

			local humanoid =
				object:FindFirstChildOfClass("Humanoid")

			local root =
				object:FindFirstChild("HumanoidRootPart")

			if humanoid
				and root
				and humanoid.Health > 0 then

				local targetPlayer =
					Players:GetPlayerFromCharacter(object)

				if Settings.TeamCheck
					and targetPlayer
					and targetPlayer.Team ==
					player.Team then

					continue
				end

				local distance =
					(camera.CFrame.Position -
					root.Position).Magnitude

				if distance >
					Settings.MaxDistance then

					continue
				end

				local screenPos, visible =
					camera:WorldToViewportPoint(
						root.Position
					)

				if visible then

					local center =
						Vector2.new(
							camera.ViewportSize.X/2,
							camera.ViewportSize.Y/2
						)

					local pos =
						Vector2.new(
							screenPos.X,
							screenPos.Y
						)

					local screenDistance =
						(pos-center).Magnitude

					if screenDistance <
						bestDistance
						and canSeeTarget(root) then

						bestDistance =
							screenDistance

						bestTarget = root

					end

				end

			end

		end

	end

	return bestTarget

end

--==================================================
-- AIM
--==================================================

RunService.RenderStepped:Connect(function()

	if not Settings.Enabled then
		return
	end

	local target = getTarget()

	if not target then
		return
	end

	local targetPosition =
		target.Position

	if Settings.PredictionEnabled then

		targetPosition =
			targetPosition +
			target.AssemblyLinearVelocity *
			Settings.Prediction

	end

	local desired =
		CFrame.lookAt(
			camera.CFrame.Position,
			targetPosition
		)

	camera.CFrame =
		camera.CFrame:Lerp(
			desired,
			Settings.Smoothness
		)

end)

update()
