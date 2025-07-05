-- Ngăn GUI cũ
pcall(function() game.CoreGui:FindFirstChild("NBGui"):Destroy() end)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local lp = Players.LocalPlayer

-- GUI nhập key
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
title.Font = Enum.Font.SourceSansBold
title.TextScaled = true

local box = Instance.new("TextBox", frame)
box.Size = UDim2.new(0.9, 0, 0, 30)
box.Position = UDim2.new(0.05, 0, 0, 35)
box.PlaceholderText = "Nhập key..."
box.Text = ""
box.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
box.TextColor3 = Color3.new(0, 0, 0)
box.Font = Enum.Font.SourceSans
box.TextScaled = true

local submit = Instance.new("TextButton", frame)
submit.Size = UDim2.new(0.9, 0, 0, 30)
submit.Position = UDim2.new(0.05, 0, 0, 75)
submit.Text = "Xác nhận"
submit.BackgroundColor3 = Color3.fromRGB(0, 170, 127)
submit.TextColor3 = Color3.new(1, 1, 1)
submit.Font = Enum.Font.SourceSansBold
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
		toggleBtn.Image = "rbxassetid://87017226532045"
		toggleBtn.BackgroundTransparency = 1

		local main = Instance.new("Frame", gui)
		main.Size = UDim2.new(0, 220, 0, 210)
		main.Position = UDim2.new(0, 80, 0.5, -105)
		main.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
		main.Active = true
		main.Draggable = true
		main.Visible = false

		toggleBtn.MouseButton1Click:Connect(function()
			main.Visible = not main.Visible
		end)

		local rainbowTitle = Instance.new("TextLabel", main)
		rainbowTitle.Size = UDim2.new(1, 0, 0, 30)
		rainbowTitle.Text = "DreamHub | By Sung a Lo"
		rainbowTitle.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
		rainbowTitle.Font = Enum.Font.SourceSansBold
		rainbowTitle.TextScaled = true

		local hue = 0
		RunService.RenderStepped:Connect(function()
			hue = (hue + 1) % 360
			rainbowTitle.TextColor3 = Color3.fromHSV(hue / 360, 1, 1)
		end)

		local boostEnabled = false

		function createToggle(name, posY, onToggle)
			local btn = Instance.new("TextButton", main)
			btn.Size = UDim2.new(0.9, 0, 0, 30)
			btn.Position = UDim2.new(0.05, 0, 0, posY)
			btn.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
			btn.TextColor3 = Color3.new(1, 1, 1)
			btn.Font = Enum.Font.SourceSansBold
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

		-- Noclip
		local noclipConn
		createToggle("Noclip", 40, function(on)
			if on then
				noclipConn = RunService.Stepped:Connect(function()
					if lp.Character then
						for _, p in pairs(lp.Character:GetDescendants()) do
							if p:IsA("BasePart") then
								p.CanCollide = false
							end
						end
					end
				end)
			elseif noclipConn then noclipConn:Disconnect() end
		end)

		-- Boost
		createToggle("Boost", 80, function(on)
			if on then
				boostEnabled = true
				pcall(function() game.CoreGui:FindFirstChild("SpeedGui"):Destroy() end)

				local gui2 = Instance.new("ScreenGui", game.CoreGui)
				gui2.Name = "SpeedGui"

				local btn = Instance.new("TextButton", gui2)
				btn.Size = UDim2.new(0, 100, 0, 35)
				btn.Position = UDim2.new(0.5, -50, 0.7, -60)
				btn.Text = "Boost OFF"
				btn.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
				btn.TextColor3 = Color3.new(1, 1, 1)
				btn.TextScaled = true
				btn.Font = Enum.Font.GothamBold
				btn.Active = true
				btn.Draggable = true

				local speedEnabled = false
				local hum

				local function updateHum()
					local char = lp.Character or lp.CharacterAdded:Wait()
					hum = char:WaitForChild("Humanoid")
				end

				updateHum()
				lp.CharacterAdded:Connect(function()
					wait(0.5)
					updateHum()
				end)

				RunService.Stepped:Connect(function()
					if speedEnabled and hum then
						hum.WalkSpeed = 55
					end
				end)

				btn.MouseButton1Click:Connect(function()
					speedEnabled = not speedEnabled
					boostEnabled = speedEnabled
					if speedEnabled then
						btn.Text = "Boost ON"
						btn.BackgroundColor3 = Color3.fromRGB(255, 80, 80)
					else
						btn.Text = "Boost OFF"
						btn.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
						if hum then hum.WalkSpeed = 16 end
					end
				end)
			else
				boostEnabled = false
			end
		end)

		-- Infinite Jump Boosted
		local infConn
		createToggle("Infinite Jump", 120, function(on)
			if on then
				infConn = UIS.JumpRequest:Connect(function()
					local char = lp.Character
					if char and char:FindFirstChild("HumanoidRootPart") and char:FindFirstChild("Humanoid") then
						local hrp = char.HumanoidRootPart
						local hum = char.Humanoid
						local moveDir = hum.MoveDirection

						local powerY = boostEnabled and 90 or 50
						local speedXZ = boostEnabled and 65 or 0

						hrp.Velocity = Vector3.new(moveDir.X * speedXZ, powerY, moveDir.Z * speedXZ)
					end
				end)
			elseif infConn then infConn:Disconnect() end
		end)

		-- ESP Rainbow
		local espLabels = {}
		local espLoop
		createToggle("ESP Rainbow", 160, function(on)
			if on then
				espLoop = RunService.RenderStepped:Connect(function()
					for _, plr in pairs(Players:GetPlayers()) do
						if plr ~= lp and plr.Character and plr.Character:FindFirstChild("Head") then
							if not espLabels[plr] then
								local bill = Instance.new("BillboardGui", plr.Character.Head)
								bill.Size = UDim2.new(0, 120, 0, 25)
								bill.StudsOffset = Vector3.new(0, 2.5, 0)
								bill.AlwaysOnTop = true

								local nameLabel = Instance.new("TextLabel", bill)
								nameLabel.Size = UDim2.new(1, 0, 1, 0)
								nameLabel.BackgroundTransparency = 1
								nameLabel.Text = plr.Name
								nameLabel.Font = Enum.Font.SourceSansBold
								nameLabel.TextScaled = true
								espLabels[plr] = nameLabel
							end
						end
					end
					for _, label in pairs(espLabels) do
						label.TextColor3 = Color3.fromHSV((tick() % 5) / 5, 1, 1)
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

	else
		lp:Kick("❌ Sai key rồi!")
	end
end)
