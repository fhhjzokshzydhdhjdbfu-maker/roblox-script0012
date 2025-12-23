-- PLAYER VISION LINE ESP (DEV)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

-- ===== UI =====
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.ResetOnSpawn = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.fromOffset(260,200)
frame.Position = UDim2.new(0.5,-130,0.25,0)
frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
frame.Active = true
frame.Draggable = true
Instance.new("UICorner", frame).CornerRadius = UDim.new(0,18)

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1,0,0,40)
title.BackgroundTransparency = 1
title.Text = "PLAYER VISION UI"
title.TextColor3 = Color3.new(1,1,1)
title.Font = Enum.Font.GothamBold
title.TextScaled = true

-- ===== Buttons =====
local espBtn = Instance.new("TextButton", frame)
espBtn.Size = UDim2.new(0.8,0,0,32)
espBtn.Position = UDim2.new(0.1,0,0,50)
espBtn.Text = "VISION : OFF"
espBtn.TextScaled = true
espBtn.BackgroundColor3 = Color3.fromRGB(40,40,40)
espBtn.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", espBtn)

local sizeBtn = Instance.new("TextButton", frame)
sizeBtn.Size = UDim2.new(0.8,0,0,32)
sizeBtn.Position = UDim2.new(0.1,0,0,90)
sizeBtn.Text = "LINE SIZE : 10%"
sizeBtn.TextScaled = true
sizeBtn.BackgroundColor3 = Color3.fromRGB(40,40,40)
sizeBtn.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", sizeBtn)

local colorBtn = Instance.new("TextButton", frame)
colorBtn.Size = UDim2.new(0.8,0,0,32)
colorBtn.Position = UDim2.new(0.1,0,0,130)
colorBtn.Text = "COLOR : WHITE"
colorBtn.TextScaled = true
colorBtn.BackgroundColor3 = Color3.fromRGB(40,40,40)
colorBtn.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", colorBtn)

local hideBtn = Instance.new("TextButton", frame)
hideBtn.Size = UDim2.fromOffset(30,30)
hideBtn.Position = UDim2.new(1,-35,0,5)
hideBtn.Text = "×"
hideBtn.TextScaled = true
hideBtn.BackgroundColor3 = Color3.fromRGB(60,60,60)
Instance.new("UICorner", hideBtn)

-- ===== Ball =====
local ball = Instance.new("TextButton", gui)
ball.Size = UDim2.fromOffset(45,45)
ball.Position = UDim2.new(0,15,0.5,-22)
ball.BackgroundColor3 = Color3.fromRGB(0,120,255)
ball.Text = "👁"
ball.TextScaled = true
ball.TextColor3 = Color3.new(1,1,1)
ball.Visible = false
ball.Active = true
ball.Draggable = true
Instance.new("UICorner", ball).CornerRadius = UDim.new(1,0)

-- ===== ESP LINE LOGIC =====
local ESP_ON = false
local lineSize = 2
local colors = {
	Color3.new(1,1,1),
	Color3.fromRGB(255,0,0),
	Color3.fromRGB(0,255,0),
	Color3.fromRGB(0,170,255)
}
local colorIndex = 1
local lines = {}

local function getLine(player)
	if not lines[player] then
		local l = Drawing.new("Line")
		l.Visible = false
		l.Thickness = lineSize
		l.Color = colors[colorIndex]
		l.Transparency = 1
		lines[player] = l
	end
	return lines[player]
end

RunService.RenderStepped:Connect(function()
	for _,p in ipairs(Players:GetPlayers()) do
		if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
			local head = p.Character.Head
			local torso = p.Character:FindFirstChild("HumanoidRootPart")
			local line = getLine(p)

			if ESP_ON and torso then
				local hPos, hOn = Camera:WorldToViewportPoint(head.Position)
				local tPos, tOn = Camera:WorldToViewportPoint(torso.Position)

				if hOn and tOn then
					line.Visible = true
					line.From = Vector2.new(hPos.X, hPos.Y)
					line.To = Vector2.new(tPos.X, tPos.Y)
					line.Thickness = lineSize
					line.Color = colors[colorIndex]
				else
					line.Visible = false
				end
			else
				line.Visible = false
			end
		end
	end
end)

-- ===== UI Actions =====
espBtn.MouseButton1Click:Connect(function()
	ESP_ON = not ESP_ON
	espBtn.Text = ESP_ON and "VISION : ON" or "VISION : OFF"
end)

sizeBtn.MouseButton1Click:Connect(function()
	lineSize = math.clamp(lineSize + 1, 1, 10)
	sizeBtn.Text = "LINE SIZE : "..(lineSize*10).."%"
end)

colorBtn.MouseButton1Click:Connect(function()
	colorIndex = colorIndex % #colors + 1
	colorBtn.Text = "COLOR : "..(colorIndex==1 and "WHITE" or "CUSTOM")
end)

hideBtn.MouseButton1Click:Connect(function()
	frame.Visible = false
	ball.Visible = true
end)

ball.MouseButton1Click:Connect(function()
	frame.Visible = true
	ball.Visible = false
end)
