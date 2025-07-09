-- Xoá GUI & Blur nếu có pcall(function() game.CoreGui:FindFirstChild("MeMaybeoKeyUI"):Destroy() end) pcall(function() game.Lighting:FindFirstChild("MeMaybeoBlur"):Destroy() end)

local cg = game:GetService("CoreGui") local Lighting = game:GetService("Lighting")

-- Blur nền local blur = Instance.new("BlurEffect", Lighting) blur.Name = "MeMaybeoBlur" blur.Size = 10

-- GUI local gui = Instance.new("ScreenGui", cg) gui.Name = "MeMaybeoKeyUI" gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling gui.ResetOnSpawn = false

-- Khung chính local main = Instance.new("Frame", gui) main.Size = UDim2.new(0, 460, 0, 260) main.Position = UDim2.new(0.5, -230, 0.5, -130) main.BackgroundColor3 = Color3.fromRGB(20, 20, 30) main.BackgroundTransparency = 0.1 main.BorderSizePixel = 0 Instance.new("UICorner", main).CornerRadius = UDim.new(0, 8)

-- Logo local logo = Instance.new("ImageLabel", main) logo.Image = "rbxassetid://9387351497" logo.Size = UDim2.new(0, 32, 0, 32) logo.Position = UDim2.new(0, 20, 0, 10) logo.BackgroundTransparency = 1 logo.ImageColor3 = Color3.new(1, 1, 1)

-- Tiêu đề local title = Instance.new("TextLabel", main) title.Text = "MeMaybeo Hub Key System" title.Size = UDim2.new(1, -120, 0, 32) title.Position = UDim2.new(0, 60, 0, 10) title.BackgroundTransparency = 1 title.TextColor3 = Color3.fromRGB(220, 220, 255) title.Font = Enum.Font.GothamBold title.TextSize = 20 title.TextXAlignment = Enum.TextXAlignment.Left

-- Thanh thu nhỏ local minimizedBar = Instance.new("Frame", gui) minimizedBar.Size = UDim2.new(0, 180, 0, 35) minimizedBar.Position = UDim2.new(1, -190, 0, 10) minimizedBar.BackgroundColor3 = Color3.fromRGB(25, 25, 40) minimizedBar.BackgroundTransparency = 0.2 minimizedBar.Visible = false Instance.new("UICorner", minimizedBar).CornerRadius = UDim.new(0, 6)

local miniLabel = Instance.new("TextLabel", minimizedBar) miniLabel.Text = "MeMaybeoHub" miniLabel.Size = UDim2.new(1, -60, 1, 0) miniLabel.Position = UDim2.new(0, 10, 0, 0) miniLabel.TextColor3 = Color3.fromRGB(220, 220, 255) miniLabel.Font = Enum.Font.GothamBold miniLabel.TextSize = 16 miniLabel.BackgroundTransparency = 1 miniLabel.TextXAlignment = Enum.TextXAlignment.Left

local miniRestore = Instance.new("TextButton", minimizedBar) miniRestore.Text = "◻️" miniRestore.Size = UDim2.new(0, 25, 0, 25) miniRestore.Position = UDim2.new(1, -55, 0.5, -12) miniRestore.BackgroundColor3 = Color3.fromRGB(70, 70, 100) miniRestore.TextColor3 = Color3.new(1,1,1) miniRestore.Font = Enum.Font.GothamBold miniRestore.TextSize = 18 Instance.new("UICorner", miniRestore).CornerRadius = UDim.new(0, 5)

local miniClose = Instance.new("TextButton", minimizedBar) miniClose.Text = "❌" miniClose.Size = UDim2.new(0, 25, 0, 25) miniClose.Position = UDim2.new(1, -28, 0.5, -12) miniClose.BackgroundColor3 = Color3.fromRGB(120, 40, 40) miniClose.TextColor3 = Color3.new(1,1,1) miniClose.Font = Enum.Font.GothamBold miniClose.TextSize = 16 Instance.new("UICorner", miniClose).CornerRadius = UDim.new(0, 5)

-- Nút thu nhỏ local btnMin = Instance.new("TextButton", main) btnMin.Text = "➖" btnMin.Size = UDim2.new(0, 25, 0, 25) btnMin.Position = UDim2.new(1, -90, 0, 5) btnMin.BackgroundColor3 = Color3.fromRGB(40, 40, 60) btnMin.TextColor3 = Color3.new(1,1,1) btnMin.Font = Enum.Font.GothamBold btnMin.TextSize = 16 Instance.new("UICorner", btnMin).CornerRadius = UDim.new(0, 4) btnMin.MouseButton1Click:Connect(function() main.Visible = false minimizedBar.Visible = true end)

miniRestore.MouseButton1Click:Connect(function() main.Visible = true minimizedBar.Visible = false end)

-- Nút đóng local btnX = Instance.new("TextButton", main) btnX.Text = "❌" btnX.Size = UDim2.new(0, 25, 0, 25) btnX.Position = UDim2.new(1, -30, 0, 5) btnX.BackgroundColor3 = Color3.fromRGB(100, 40, 40) btnX.TextColor3 = Color3.new(1,1,1) btnX.Font = Enum.Font.GothamBold btnX.TextSize = 16 Instance.new("UICorner", btnX).CornerRadius = UDim.new(0, 4) btnX.MouseButton1Click:Connect(function() gui:Destroy() blur:Destroy() end) miniClose.MouseButton1Click:Connect(function() gui:Destroy() blur:Destroy() end)

-- Subtitle local subtitle = Instance.new("TextLabel", main) subtitle.Text = "Get Key (Linkvertise ONLY IN DISCORD)" subtitle.Size = UDim2.new(1, -40, 0, 25) subtitle.Position = UDim2.new(0, 20, 0, 50) subtitle.BackgroundTransparency = 1 subtitle.TextColor3 = Color3.fromRGB(180, 180, 255) subtitle.Font = Enum.Font.Gotham subtitle.TextSize = 15 subtitle.TextXAlignment = Enum.TextXAlignment.Left

-- Nhập key local input = Instance.new("TextBox", main) input.Size = UDim2.new(1, -40, 0, 40) input.Position = UDim2.new(0, 20, 0, 90) input.PlaceholderText = "Enter your key..." input.Text = "" input.Font = Enum.Font.Gotham input.TextSize = 16 input.TextColor3 = Color3.new(1, 1, 1) input.PlaceholderColor3 = Color3.fromRGB(180, 180, 180) input.BackgroundColor3 = Color3.fromRGB(30, 30, 45) Instance.new("UICorner", input).CornerRadius = UDim.new(0, 6)

-- Nút check key local checkBtn = Instance.new("TextButton", main) checkBtn.Size = UDim2.new(1, -40, 0, 35) checkBtn.Position = UDim2.new(0, 20, 0, 140) checkBtn.Text = "Check Key" checkBtn.Font = Enum.Font.GothamBold checkBtn.TextSize = 16 checkBtn.TextColor3 = Color3.new(1, 1, 1) checkBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 110) Instance.new("UICorner", checkBtn).CornerRadius = UDim.new(0, 6)

-- Xử lý nhập key checkBtn.MouseButton1Click:Connect(function() if input.Text == "123" then checkBtn.Text = "✅ Correct! Loading..."

-- Thông báo thành công
	local notify = Instance.new("Frame", gui)
	notify.Size = UDim2.new(0, 300, 0, 50)
	notify.Position = UDim2.new(0.5, -150, 1, -60)
	notify.BackgroundColor3 = Color3.fromRGB(30, 255, 120)
	notify.BackgroundTransparency = 0.15
	Instance.new("UICorner", notify).CornerRadius = UDim.new(0, 8)

	local icon = Instance.new("ImageLabel", notify)
	icon.Image = "rbxassetid://87017226532045"
	icon.Size = UDim2.new(0, 32, 0, 32)
	icon.Position = UDim2.new(0, 10, 0.5, -16)
	icon.BackgroundTransparency = 1

	local label = Instance.new("TextLabel", notify)
	label.Text = "Executed Successfully!"
	label.Size = UDim2.new(1, -50, 1, 0)
	label.Position = UDim2.new(0, 50, 0, 0)
	label.TextColor3 = Color3.new(1, 1, 1)
	label.Font = Enum.Font.GothamBold
	label.TextSize = 16
	label.BackgroundTransparency = 1
	label.TextXAlignment = Enum.TextXAlignment.Left

	task.delay(3, function()
		notify:Destroy()
		gui:Destroy()
		blur:Destroy()
		-- Gọt script chính ở đây
		-- loadstring(game:HttpGet("LINK_TO_YOUR_MAIN_SCRIPT"))()
	end)
else
	checkBtn.Text = "❌ Wrong Key!"
	wait(1.2)
	checkBtn.Text = "Check Key"
end

end)

