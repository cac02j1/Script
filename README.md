-- Xoá GUI cũ nếu có
pcall(function() game.CoreGui:FindFirstChild("DreamHub"):Destroy() end)

-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local HttpService = game:GetService("HttpService")
local TeleportService = game:GetService("TeleportService")
local lp = Players.LocalPlayer
local placeId = game.PlaceId

-- GUI chính
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "DreamHub"
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Nút toggle GUI
local toggleBtn = Instance.new("ImageButton", gui)
toggleBtn.Size = UDim2.new(0, 60, 0, 60)
toggleBtn.Position = UDim2.new(0, 10, 0.5, -30)
toggleBtn.Image = "rbxassetid://10002032288"
toggleBtn.BackgroundTransparency = 1
toggleBtn.Draggable = true

-- Frame chính
local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 260, 0, 340)
main.Position = UDim2.new(0, 80, 0.5, -170)
main.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
main.Visible = false
main.Active = true
main.Draggable = true

toggleBtn.MouseButton1Click:Connect(function()
	main.Visible = not main.Visible
end)

-- Tiêu đề
local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1, 0, 0, 35)
title.Text = "DreamHub | By Sung a Lo"
title.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
title.Font = Enum.Font.Arcade
title.TextScaled = true
title.TextColor3 = Color3.new(1, 1, 1)

-- Toggle Function
local yOffset = 40
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
	Instance.new("UICorner", toggle).CornerRadius = UDim.new(1, 0)

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
	local char = lp.Character
	if state then
		local hrp = char and char:FindFirstChild("HumanoidRootPart")
		if hrp then
			floatForce = Instance.new("BodyVelocity")
			floatForce.Velocity = Vector3.new(0, 40, 0)
			floatForce.MaxForce = Vector3.new(0, 100000, 0)
			floatForce.P = 1000
			floatForce.Name = "FloatVelocity"
			floatForce.Parent = hrp
			floatConn = RunService.RenderStepped:Connect(function()
				if floatForce and hrp then
					floatForce.Velocity = Vector3.new(0, 40, 0)
				end
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
		if (v:IsA("BasePart") or v:IsA("UnionOperation") or v:IsA("MeshPart")) and not v:IsDescendantOf(lp.Character) then
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

-- ESP Viền Người
local espConnections = {}
local function createESPBox(part)
	local box = Instance.new("BoxHandleAdornment")
	box.Name = "ESPBox"
	box.Adornee = part
	box.AlwaysOnTop = true
	box.ZIndex = 5
	box.Size = part.Size + Vector3.new(0.1, 0.1, 0.1)
	box.Transparency = 0.3
	box.Color3 = Color3.fromRGB(0, 255, 255)
	box.Parent = part
end

createToggle("ESP Viền Người", function(state)
	if state then
		for _, plr in pairs(Players:GetPlayers()) do
			if plr ~= lp and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
				createESPBox(plr.Character.HumanoidRootPart)
			end
		end
		local con = Players.PlayerAdded:Connect(function(plr)
			plr.CharacterAdded:Connect(function(char)
				wait(1)
				if char:FindFirstChild("HumanoidRootPart") then
					createESPBox(char.HumanoidRootPart)
				end
			end)
		end)
		table.insert(espConnections, con)
	else
		for _, plr in pairs(Players:GetPlayers()) do
			if plr.Character then
				for _, obj in pairs(plr.Character:GetDescendants()) do
					if obj:IsA("BoxHandleAdornment") and obj.Name == "ESPBox" then
						obj:Destroy()
					end
				end
			end
		end
		for _, con in ipairs(espConnections) do
			pcall(function() con:Disconnect() end)
		end
		espConnections = {}
	end
end)

-- HOP SERVER (cuối GUI)
local tried = {}
local function hopToAnotherServer()
	local cursor = ""
	local function getServer()
		local url = "https://games.roblox.com/v1/games/" .. placeId .. "/servers/Public?sortOrder=Asc&limit=100" .. (cursor ~= "" and "&cursor=" .. cursor or "")
		local success, response = pcall(function()
			return game:HttpGet(url)
		end)
		if not success then return nil end

		local data = HttpService:JSONDecode(response)
		for _, server in pairs(data.data) do
			if server.playing < server.maxPlayers and server.id ~= game.JobId and not tried[server.id] then
				tried[server.id] = true
				return server.id
			end
		end
		cursor = data.nextPageCursor or ""
		return nil
	end

	local serverId = nil
	repeat serverId = getServer() until serverId or cursor == ""
	if serverId then
		TeleportService:TeleportToPlaceInstance(placeId, serverId, lp)
	else
		warn("Không tìm được server phù hợp.")
	end
end

local hopBtn = Instance.new("TextButton", main)
hopBtn.Size = UDim2.new(1, -10, 0, 40)
hopBtn.Position = UDim2.new(0, 5, 1, -45)
hopBtn.Text = "HOP SERVER"
hopBtn.Font = Enum.Font.Arcade
hopBtn.TextScaled = true
hopBtn.TextColor3 = Color3.new(1, 1, 1)
hopBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
hopBtn.BorderSizePixel = 0
Instance.new("UICorner", hopBtn).CornerRadius = UDim.new(0, 8)
hopBtn.MouseButton1Click:Connect(function()
	hopBtn.Text = "Hopping..."
	hopToAnotherServer()
end)

-- BOOST (tách riêng)
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

local boostConn
local boostOn = false
boostBtn.MouseButton1Click:Connect(function()
	boostOn = not boostOn
	if boostOn then
		boostBtn.Text = "BOOST ✅"
		boostBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
		boostConn = RunService.RenderStepped:Connect(function()
			local char = lp.Character
			if char and char:FindFirstChild("Humanoid") and char:FindFirstChild("HumanoidRootPart") then
				local hum = char.Humanoid
				local hrp = char.HumanoidRootPart
				if hum.MoveDirection.Magnitude > 0 then
					hrp.Velocity = hum.MoveDirection * 50 + Vector3.new(0, hrp.Velocity.Y, 0)
				end
			end
		end)
	else
		boostBtn.Text = "BOOST"
		boostBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
		if boostConn then boostConn:Disconnect() end
	end
end)
