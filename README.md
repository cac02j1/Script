-- Xóa GUI trùng
pcall(function() game.CoreGui:FindFirstChild("NBGui"):Destroy() end)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local lp = Players.LocalPlayer
local PlaceId = game.PlaceId

-- GUI chính
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "NBGui"
gui.ResetOnSpawn = false

-- Nút toggle GUI
local toggleFrame = Instance.new("Frame", gui)
toggleFrame.Size = UDim2.new(0, 60, 0, 60)
toggleFrame.Position = UDim2.new(0, 10, 0.5, -30)
toggleFrame.BackgroundTransparency = 1
toggleFrame.Active = true
toggleFrame.Draggable = true

local toggleBtn = Instance.new("ImageButton", toggleFrame)
toggleBtn.Size = UDim2.new(1, 0, 1, 0)
toggleBtn.Image = "rbxassetid://87017226532045"
toggleBtn.BackgroundTransparency = 1

-- GUI nhỏ
local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 210, 0, 210)
main.Position = UDim2.new(0, 80, 0.5, -105)
main.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
main.Active = true
main.Draggable = true
main.Visible = false

toggleBtn.MouseButton1Click:Connect(function()
	main.Visible = not main.Visible
end)

-- Tiêu đề Rainbow
local rainbowTitle = Instance.new("TextLabel", main)
rainbowTitle.Size = UDim2.new(1, 0, 0, 25)
rainbowTitle.Text = "DreamHub | By Sung a Lo"
rainbowTitle.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
rainbowTitle.TextColor3 = Color3.new(1, 1, 1)
rainbowTitle.Font = Enum.Font.SourceSansBold
rainbowTitle.TextScaled = true

local hue = 0
RunService.RenderStepped:Connect(function()
	hue = (hue + 1) % 360
	rainbowTitle.TextColor3 = Color3.fromHSV(hue / 360, 1, 1)
end)

-- Hàm tạo Toggle
function createToggle(name, posY, onToggle)
	local btn = Instance.new("TextButton", main)
	btn.Size = UDim2.new(0.9, 0, 0, 30)
	btn.Position = UDim2.new(0.05, 0, 0, posY)
	btn.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.Font = Enum.Font.Arcade
	btn.TextScaled = true

	local state = false
	local function update()
		btn.Text = name .. (state and " ✅ ON" or " ❌ OFF")
		btn.BackgroundColor3 = state and Color3.fromRGB(0, 200, 100) or Color3.fromRGB(255, 80, 80)
	end

	btn.MouseButton1Click:Connect(function()
		state = not state
		update()
		onToggle(state)
	end)

	update()
end

-- Hàm tạo Button thường
function createButton(name, posY, callback)
	local btn = Instance.new("TextButton", main)
	btn.Size = UDim2.new(0.9, 0, 0, 30)
	btn.Position = UDim2.new(0.05, 0, 0, posY)
	btn.BackgroundColor3 = Color3.fromRGB(100, 100, 160)
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.Font = Enum.Font.Arcade
	btn.TextScaled = true
	btn.Text = name
	btn.MouseButton1Click:Connect(callback)
end

-- Noclip
local noclipConn
createToggle("Noclip", 30, function(on)
	if on then
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
createToggle("Infinite Jump", 65, function(on)
	if on then
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

-- ESP Rainbow
local espLabels = {}
local espLoop

createToggle("ESP Rainbow", 100, function(on)
	if on then
		espLoop = RunService.RenderStepped:Connect(function()
			for _, plr in pairs(Players:GetPlayers()) do
				if plr ~= lp and plr.Character and plr.Character:FindFirstChild("Head") then
					if not espLabels[plr] then
						local bill = Instance.new("BillboardGui", plr.Character.Head)
						bill.Size = UDim2.new(0, 100, 0, 25)
						bill.StudsOffset = Vector3.new(0, 2.5, 0)
						bill.AlwaysOnTop = true

						local nameLabel = Instance.new("TextLabel", bill)
						nameLabel.Size = UDim2.new(1, 0, 1, 0)
						nameLabel.BackgroundTransparency = 1
						nameLabel.Text = plr.Name
						nameLabel.Font = Enum.Font.SourceSansBold
						nameLabel.TextScaled = true
						nameLabel.TextColor3 = Color3.new(1,1,1)

						espLabels[plr] = nameLabel
					end
				end
			end

			for _, label in pairs(espLabels) do
				local t = tick() % 5
				label.TextColor3 = Color3.fromHSV(t/5, 1, 1)
			end
		end)
	else
		if espLoop then espLoop:Disconnect() end
		for _, label in pairs(espLabels) do
			label.Parent:Destroy()
		end
		espLabels = {}
	end
end)

-- Hop Server
function hopServer()
	local servers = {}
	local success, result = pcall(function()
		return HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/" .. PlaceId .. "/servers/Public?sortOrder=Asc&limit=100"))
	end)
	if success and result and result.data then
		for _, v in pairs(result.data) do
			if v.playing < v.maxPlayers and v.id ~= game.JobId then
				table.insert(servers, v.id)
			end
		end
	end
	if #servers > 0 then
		local randomServer = servers[math.random(1, #servers)]
		TeleportService:TeleportToPlaceInstance(PlaceId, randomServer)
	else
		warn("❌ No available servers to hop.")
	end
end

createButton("Hop Server", 135, function()
	hopServer()
end)

-- Nút Boost riêng nhỏ
local boostBtn = Instance.new("TextButton", gui)
boostBtn.Size = UDim2.new(0, 85, 0, 30)
boostBtn.Position = UDim2.new(0, 10, 1, -40)
boostBtn.BackgroundColor3 = Color3.fromRGB(90, 150, 255)
boostBtn.Font = Enum.Font.Arcade
boostBtn.TextSize = 18
boostBtn.TextColor3 = Color3.new(1, 1, 1)
boostBtn.Text = "BOOST"
boostBtn.ZIndex = 10

local boostConn
local boostOn = false

boostBtn.MouseButton1Click:Connect(function()
	boostOn = not boostOn
	if boostOn then
		boostBtn.Text = "ON BOOST"
		boostBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
		boostConn = RunService.RenderStepped:Connect(function()
			local char = lp.Character
			if char and char:FindFirstChild("HumanoidRootPart") then
				local hum = char:FindFirstChild("Humanoid")
				local hrp = char:FindFirstChild("HumanoidRootPart")
				if hum.MoveDirection.Magnitude > 0 then
					hrp.Velocity = hum.MoveDirection * 50 + Vector3.new(0, hrp.Velocity.Y, 0)
				end
			end
		end)
	else
		boostBtn.Text = "BOOST"
		boostBtn.BackgroundColor3 = Color3.fromRGB(90, 150, 255)
		if boostConn then boostConn:Disconnect() end
	end
end)
