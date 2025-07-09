-- Xoá GUI cũ
pcall(function() game.CoreGui:FindFirstChild("DreamHub"):Destroy() end)

-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local lp = Players.LocalPlayer

-- GUI chính
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "DreamHub"
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Toggle GUI
local toggleBtn = Instance.new("ImageButton", gui)
toggleBtn.Size = UDim2.new(0, 60, 0, 60)
toggleBtn.Position = UDim2.new(0, 10, 0.5, -30)
toggleBtn.Image = "rbxassetid://10002032288"
toggleBtn.BackgroundTransparency = 1
toggleBtn.Draggable = true

-- Frame chính
local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 240, 0, 300)
main.Position = UDim2.new(0, 80, 0.5, -150)
main.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
main.Visible = false
main.Active = true
main.Draggable = true

toggleBtn.MouseButton1Click:Connect(function()
	main.Visible = not main.Visible
end)

-- Tiêu đề
local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1, 0, 0, 30)
title.Text = "DreamHub | By Sung a Lo"
title.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
title.Font = Enum.Font.Arcade
title.TextScaled = true
title.TextColor3 = Color3.new(1, 1, 1)

-- Tạo toggle item
local yOffset = 35
local function createToggle(name, callback)
	local container = Instance.new("Frame", main)
	container.Size = UDim2.new(1, -10, 0, 40)
	container.Position = UDim2.new(0, 5, 0, yOffset)
	container.BackgroundTransparency = 1

	local label = Instance.new("TextLabel", container)
	label.Text = name
	label.Font = Enum.Font.Arcade
	label.TextScaled = true
	label.TextColor3 = Color3.new(1, 1, 1)
	label.Size = UDim2.new(0.75, 0, 1, 0)
	label.BackgroundTransparency = 1

	local toggle = Instance.new("TextButton", container)
	toggle.Size = UDim2.new(0, 30, 0, 30)
	toggle.Position = UDim2.new(1, -35, 0.5, -15)
	toggle.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
	toggle.Text = ""
	toggle.AutoButtonColor = false
	toggle.BorderSizePixel = 0
	toggle.AnchorPoint = Vector2.new(0, 0.5)
	local uicorner = Instance.new("UICorner", toggle)
	uicorner.CornerRadius = UDim.new(1, 0)

	local active = false
	toggle.MouseButton1Click:Connect(function()
		active = not active
		toggle.BackgroundColor3 = active and Color3.fromRGB(0, 200, 100) or Color3.fromRGB(100, 100, 100)
		callback(active)
	end)

	yOffset = yOffset + 45
end

-- Noclip
local noclipConn
createToggle("Noclip", function(state)
	if state then
		noclipConn = RunService.Stepped:Connect(function()
			if lp.Character then
				for _, part in pairs(lp.Character:GetDescendants()) do
					if part:IsA("BasePart") then
						part.CanCollide = false
					end
				end
			end
		end)
	elseif noclipConn then
		noclipConn:Disconnect()
	end
end)

-- Infinite Jump
local infConn
createToggle("Infinite Jump", function(state)
	if state then
		infConn = UIS.JumpRequest:Connect(function()
			local char = lp.Character
			if char and char:FindFirstChild("HumanoidRootPart") then
				char.HumanoidRootPart.Velocity = Vector3.new(0, 50, 0)
			end
		end)
	elseif infConn then
		infConn:Disconnect()
	end
end)

-- Float
local floatConn
local floatForce
createToggle("Float", function(state)
	if state then
		local hrp = lp.Character and lp.Character:FindFirstChild("HumanoidRootPart")
		if hrp then
			floatForce = Instance.new("BodyVelocity", hrp)
			floatForce.Velocity = Vector3.new(0, 100, 0)
			floatForce.MaxForce = Vector3.new(0, math.huge, 0)
			floatConn = RunService.RenderStepped:Connect(function()
				hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(3), 0)
			end)
		end
	else
		if floatConn then floatConn:Disconnect() end
		if floatForce then floatForce:Destroy() end
	end
end)

-- Check Brainrot
local restoreParts = {}
createToggle("Check Brainrot", function(state)
	for _, v in pairs(workspace:GetDescendants()) do
		if (v:IsA("BasePart") or v:IsA("UnionOperation") or v:IsA("MeshPart"))
		and not v:IsDescendantOf(lp.Character) then
			if state then
				restoreParts[v] = {v.LocalTransparencyModifier, v.Material}
				v.LocalTransparencyModifier = 0.65
				v.Material = Enum.Material.SmoothPlastic
			elseif restoreParts[v] then
				v.LocalTransparencyModifier = restoreParts[v][1]
				v.Material = restoreParts[v][2]
			end
		end
	end
	if not state then restoreParts = {} end
end)

-- BOOST kéo được
local boostFrame = Instance.new("Frame", gui)
boostFrame.Size = UDim2.new(0, 130, 0, 45)
boostFrame.Position = UDim2.new(0.5, -65, 0.6, 0)
boostFrame.BackgroundTransparency = 1
boostFrame.Active = true
boostFrame.Draggable = true

local boostBtn = Instance.new("TextButton", boostFrame)
boostBtn.Size = UDim2.new(1, 0, 1, 0)
boostBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
boostBtn.Font = Enum.Font.Arcade
boostBtn.TextSize = 22
boostBtn.TextColor3 = Color3.new(1, 1, 1)
boostBtn.Text = "BOOST"
boostBtn.ZIndex = 10

local boostOn = false
local boostConn
boostBtn.MouseButton1Click:Connect(function()
	boostOn = not boostOn
	if boostOn then
		boostBtn.Text = "BOOST ✅"
		boostBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
		boostConn = RunService.RenderStepped:Connect(function()
			local char = lp.Character
			if char and char:FindFirstChild("HumanoidRootPart") then
				local hum = char:FindFirstChild("Humanoid")
				local hrp = char:FindFirstChild("HumanoidRootPart")
				if hum.MoveDirection.Magnitude > 0 then
					hrp.Velocity = hum.MoveDirection * 60 + Vector3.new(0, hrp.Velocity.Y, 0)
				end
			end
		end)
	else
		boostBtn.Text = "BOOST"
		boostBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
		if boostConn then boostConn:Disconnect() end
	end
end)
