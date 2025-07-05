-- Ngăn GUI trùng lặp
pcall(function() game.CoreGui:FindFirstChild("NBGui"):Destroy() end)
pcall(function() game.CoreGui:FindFirstChild("BoostGui"):Destroy() end)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local lp = Players.LocalPlayer

-- GUI Nhập Key
local keyGui = Instance.new("ScreenGui", game.CoreGui)
keyGui.Name = "NBGui"

local frame = Instance.new("Frame", keyGui)
frame.Size = UDim2.new(0, 220, 0, 130)
frame.Position = UDim2.new(0.5, -110, 0.5, -65)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.Active = true
frame.Draggable = true

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.Text = "🔑 Nhập Key"
title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.Arcade
title.TextScaled = true

local box = Instance.new("TextBox", frame)
box.Size = UDim2.new(0.9, 0, 0, 30)
box.Position = UDim2.new(0.05, 0, 0, 35)
box.PlaceholderText = "Nhập key..."
box.Text = ""
box.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
box.TextColor3 = Color3.new(0, 0, 0)
box.Font = Enum.Font.Arcade
box.TextScaled = true

local submit = Instance.new("TextButton", frame)
submit.Size = UDim2.new(0.9, 0, 0, 30)
submit.Position = UDim2.new(0.05, 0, 0, 75)
submit.Text = "Xác nhận"
submit.BackgroundColor3 = Color3.fromRGB(0, 170, 127)
submit.TextColor3 = Color3.new(1, 1, 1)
submit.Font = Enum.Font.Arcade
submit.TextScaled = true

submit.MouseButton1Click:Connect(function()
	if box.Text == "Dr123" then
		keyGui:Destroy()

		local gui = Instance.new("ScreenGui", game.CoreGui)
		gui.Name = "NBGui"
		gui.ResetOnSpawn = false

		local toggleFrame = Instance.new("Frame", gui)
		toggleFrame.Size = UDim2.new(0, 60, 0, 60)
		toggleFrame.Position = UDim2.new(0, 10, 0.5, -30)
		toggleFrame.BackgroundTransparency = 1
		toggleFrame.Active = true
		toggleFrame.Draggable = true

		local toggleBtn = Instance.new("ImageButton", toggleFrame)
		toggleBtn.Size = UDim2.new(1, 0, 1, 0)
		toggleBtn.Image = "rbxassetid://15679098532"
		toggleBtn.BackgroundTransparency = 1

		local main = Instance.new("Frame", gui)
		main.Size = UDim2.new(0, 210, 0, 240)
		main.Position = UDim2.new(0, 80, 0.5, -120)
		main.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
		main.Active = true
		main.Draggable = true
		main.Visible = false

		toggleBtn.MouseButton1Click:Connect(function()
			main.Visible = not main.Visible
		end)

		local rainbowTitle = Instance.new("TextLabel", main)
		rainbowTitle.Size = UDim2.new(1, 0, 0, 25)
		rainbowTitle.Text = "DreamHub | By Sung a Lo"
		rainbowTitle.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
		rainbowTitle.TextColor3 = Color3.new(1, 1, 1)
		rainbowTitle.Font = Enum.Font.Arcade
		rainbowTitle.TextScaled = true

		local hue = 0
		RunService.RenderStepped:Connect(function()
			hue = (hue + 1) % 360
			rainbowTitle.TextColor3 = Color3.fromHSV(hue / 360, 1, 1)
		end)

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
								nameLabel.Font = Enum.Font.Arcade
								nameLabel.TextScaled = true
								nameLabel.TextColor3 = Color3.new(1, 1, 1)

								espLabels[plr] = nameLabel
							end
						end
					end

					for _, label in pairs(espLabels) do
						local t = tick() % 5
						label.TextColor3 = Color3.fromHSV(t / 5, 1, 1)
					end
				end)
			else
				if espLoop then espLoop:Disconnect() end
				for _, label in pairs(espLabels) do
					if label and label.Parent then
						label.Parent:Destroy()
					end
				end
				espLabels = {}
			end
		end)

		-- Hop Server khác nhau
		local hopBtn = Instance.new("TextButton", main)
		hopBtn.Size = UDim2.new(0.9, 0, 0, 30)
		hopBtn.Position = UDim2.new(0.05, 0, 0, 135)
		hopBtn.BackgroundColor3 = Color3.fromRGB(100, 100, 255)
		hopBtn.TextColor3 = Color3.new(1, 1, 1)
		hopBtn.Font = Enum.Font.Arcade
		hopBtn.Text = "🔁 Hop Server"
		hopBtn.TextScaled = true

		hopBtn.MouseButton1Click:Connect(function()
			hopBtn.Text = "🔄 Tìm server..."
			local success, result = pcall(function()
				local servers = {}
				local cursor = ""
				repeat
					local url = "https://games.roblox.com/v1/games/" .. game.PlaceId .. "/servers/Public?limit=100" .. (cursor ~= "" and "&cursor=" .. cursor or "")
					local response = HttpService:JSONDecode(game:HttpGet(url))
					for _, server in pairs(response.data) do
						if server.playing < server.maxPlayers and server.id ~= game.JobId then
							table.insert(servers, server.id)
						end
					end
					cursor = response.nextPageCursor
				until not cursor or #servers >= 5

				if #servers > 0 then
					TeleportService:TeleportToPlaceInstance(game.PlaceId, servers[math.random(1, #servers)], lp)
				else
					hopBtn.Text = "⚠ Không tìm được!"
					wait(2)
					hopBtn.Text = "🔁 Hop Server"
				end
			end)
			if not success then
				hopBtn.Text = "❌ Lỗi!"
				wait(2)
				hopBtn.Text = "🔁 Hop Server"
			end
		end)

		-- Boost nổi
		local boostFloatingGui = Instance.new("ScreenGui", game.CoreGui)
		boostFloatingGui.Name = "BoostGui"
		boostFloatingGui.ResetOnSpawn = false

		local boostBtn = Instance.new("TextButton", boostFloatingGui)
		boostBtn.Size = UDim2.new(0, 120, 0, 40)
		boostBtn.Position = UDim2.new(0.5, -60, 1, -100)
		boostBtn.AnchorPoint = Vector2.new(0.5, 0)
		boostBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
		boostBtn.BorderSizePixel = 2
		boostBtn.BorderColor3 = Color3.new(1, 1, 1)
		boostBtn.TextColor3 = Color3.new(1, 1, 1)
		boostBtn.Font = Enum.Font.Arcade
		boostBtn.Text = "Boost OFF"
		boostBtn.TextScaled = true
		boostBtn.Draggable = true
		boostBtn.AutoButtonColor = false

		local boostRunning = false
		local boostConn

		local function startBoost()
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
		end

		boostBtn.MouseButton1Click:Connect(function()
			boostRunning = not boostRunning
			if boostRunning then
				boostBtn.Text = "Boost ON"
				boostBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
				startBoost()
			else
				boostBtn.Text = "Boost OFF"
				boostBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
				if boostConn then boostConn:Disconnect() end
			end
		end)

		lp.CharacterAdded:Connect(function()
			if boostRunning then
				task.wait(1)
				if boostConn then boostConn:Disconnect() end
				startBoost()
			end
		end)
	else
		lp:Kick("❌ Sai key rồi!")
	end
end)
