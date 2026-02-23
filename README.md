local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local ContentProvider = game:GetService("ContentProvider")

local player = Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "Argo_MistyFinal_V12"
screenGui.ResetOnSpawn = false
screenGui.Parent = player.PlayerGui

-- CONFIG
local toggleKey = Enum.KeyCode.RightControl
local cloneKey = nil
local isBinding = false
local targetUserId = nil
local isFull = false
local normalSize = UDim2.new(0, 330, 0, 470)
local normalPos = UDim2.new(0.5, -165, 0.5, -235)
local SIMPLE_FONT = Enum.Font.SourceSansBold

-- IMAGE
local IMAGE_ID = "81487620002600"
local PHOTO_URL = "https://www.roblox.com/asset-thumbnail/image?assetId="..IMAGE_ID.."&width=420&height=420&format=png"

-- HÀM TẠO HIỆU ỨNG KÍNH MỜ SƯƠNG MÙ
local function applyMistyGlass(obj, transparency, cornerRadius)
	obj.BackgroundColor3 = Color3.fromRGB(220, 220, 230)
	obj.BackgroundTransparency = transparency or 0.7
	
	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, cornerRadius or 12)
	corner.Parent = obj
	
	local stroke = Instance.new("UIStroke")
	stroke.Thickness = 1.2
	stroke.Color = Color3.new(1, 1, 1)
	stroke.Transparency = 0.75
	stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
	stroke.Parent = obj

	-- LỚP BÓNG SÁNG HẤT TỪ TRÊN XUỐNG CÁCH VIỀN
	local mistFrame = Instance.new("Frame")
	mistFrame.Name = "MistyGlow"
	mistFrame.Size = UDim2.new(1, -6, 1, -6)
	mistFrame.Position = UDim2.new(0, 3, 0, 3)
	mistFrame.BackgroundTransparency = 0
	mistFrame.ZIndex = obj.ZIndex
	mistFrame.Parent = obj
	Instance.new("UICorner", mistFrame).CornerRadius = UDim.new(0, cornerRadius - 3)
	
	local mistGrad = Instance.new("UIGradient")
	mistGrad.Color = ColorSequence.new({
		ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
		ColorSequenceKeypoint.new(1, Color3.fromRGB(180, 180, 190))
	})
	mistGrad.Transparency = NumberSequence.new({
		NumberSequenceKeypoint.new(0, 0.4), 
		NumberSequenceKeypoint.new(1, 0.95)
	})
	mistGrad.Rotation = 90
	mistGrad.Parent = mistFrame
end

-- MAIN FRAME
local mainFrame = Instance.new("Frame")
mainFrame.Size = normalSize
mainFrame.Position = normalPos
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui
applyMistyGlass(mainFrame, 0.65, 20) 

-- ẢNH NỀN (ĐÃ LÀM RÕ HƠN)
local bgImage = Instance.new("ImageLabel")
bgImage.Size = UDim2.new(1, 0, 1, 0)
bgImage.BackgroundTransparency = 1
bgImage.Image = PHOTO_URL
bgImage.ImageTransparency = 0.25 -- Giảm độ trong suốt để ảnh RÕ NÉT hơn
bgImage.ScaleType = Enum.ScaleType.Crop
bgImage.ZIndex = 0
bgImage.Parent = mainFrame
Instance.new("UICorner", bgImage).CornerRadius = UDim.new(0, 20)

-- TOP BAR
local topBar = Instance.new("Frame")
topBar.Size = UDim2.new(1, 0, 0, 50)
topBar.BackgroundTransparency = 1
topBar.ZIndex = 10
topBar.Parent = mainFrame

local title = Instance.new("TextLabel")
title.Text = "   ARGO MENU"
title.Size = UDim2.new(0.5, 0, 1, 0)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = SIMPLE_FONT
title.TextSize = 18
title.ZIndex = 11
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = topBar

local function createTopBtn(text, pos)
	local btn = Instance.new("TextButton")
	btn.Text = text
	btn.Size = UDim2.new(0, 28, 0, 28)
	btn.Position = pos
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.Font = SIMPLE_FONT
	btn.TextSize = 14
	btn.ZIndex = 12
	btn.Parent = topBar
	applyMistyGlass(btn, 0.6, 8)
	return btn
end
local hideBtn = createTopBtn("-", UDim2.new(1, -75, 0.5, -14))
local fullBtn = createTopBtn("▢", UDim2.new(1, -35, 0.5, -14))

-- CONTENT
local content = Instance.new("Frame")
content.Size = UDim2.new(1, -40, 1, -100)
content.Position = UDim2.new(0, 20, 0, 70)
content.BackgroundTransparency = 1
content.Parent = mainFrame

-- AVATAR
local avatarContainer = Instance.new("Frame")
avatarContainer.Size = UDim2.new(0, 85, 0, 85)
avatarContainer.Position = UDim2.new(0.5, -42.5, 0, 0)
avatarContainer.ZIndex = 2
avatarContainer.Parent = content
applyMistyGlass(avatarContainer, 0.4, 42)

local avatarImage = Instance.new("ImageLabel")
avatarImage.Size = UDim2.new(1, 0, 1, 0)
avatarImage.Image = "rbxthumb://type=AvatarHeadShot&id="..player.UserId.."&w=150&h=150"
avatarImage.BackgroundTransparency = 1
avatarImage.ZIndex = 3
avatarImage.Parent = avatarContainer
Instance.new("UICorner", avatarImage).CornerRadius = UDim.new(1, 0)

-- NÚT BẤM ĐỒNG BỘ MÀU SẮC
local function createButton(text, pos, size)
	local btn = Instance.new("TextButton")
	btn.Text = text
	btn.Size = size or UDim2.new(1, 0, 0, 42)
	btn.Position = pos
	btn.Font = SIMPLE_FONT
	btn.TextSize = 15
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.ZIndex = 5
	btn.Parent = content
	applyMistyGlass(btn, 0.55, 10) -- Dùng chung hiệu ứng mờ sương
	return btn
end

local userBox = Instance.new("TextBox")
userBox.Size = UDim2.new(0.75, 0, 0, 38)
userBox.Position = UDim2.new(0, 0, 0, 110)
userBox.PlaceholderText = "Username..."
userBox.Text = ""
userBox.Font = SIMPLE_FONT
userBox.TextSize = 14
userBox.TextColor3 = Color3.new(1, 1, 1)
userBox.ZIndex = 5
userBox.Parent = content
applyMistyGlass(userBox, 0.7, 8)

local checkBtn = createButton("✔", UDim2.new(0.8, 0, 0, 110), UDim2.new(0.2, 0, 0, 38))
local cloneBtn = createButton("CREATE CLONE", UDim2.new(0, 0, 0, 165), UDim2.new(0.68, 0, 0, 45))
local bindBtn = createButton("KEY", UDim2.new(0.72, 0, 0, 165), UDim2.new(0.28, 0, 0, 45))

-- NÚT DELETE GIỜ ĐÃ GIỐNG CÁC NÚT KHÁC
local deleteBtn = createButton("DELETE ALL CLONES", UDim2.new(0, 0, 1, -45), nil)

-- LOGIC
local function doClone()
	if targetUserId then
		pcall(function()
			local model = Players:CreateHumanoidModelFromUserIdAsync(targetUserId)
			model.Name = "Clone_" .. userBox.Text
			model.Parent = workspace
			local hrp = player.Character.HumanoidRootPart
			model:MoveTo(hrp.Position + hrp.CFrame.LookVector * 5)
		end)
	end
end

checkBtn.MouseButton1Click:Connect(function()
	local success, userId = pcall(function() return Players:GetUserIdFromNameAsync(userBox.Text) end)
	if success then 
		avatarImage.Image = "rbxthumb://type=AvatarHeadShot&id="..userId.."&w=150&h=150"
		targetUserId = userId
	end
end)
cloneBtn.MouseButton1Click:Connect(doClone)
bindBtn.MouseButton1Click:Connect(function() isBinding = true bindBtn.Text = "..." end)
deleteBtn.MouseButton1Click:Connect(function()
	for _, v in pairs(workspace:GetChildren()) do if v.Name:find("Clone_") then v:Destroy() end end
end)

-- INTERACTION
UserInputService.InputBegan:Connect(function(input, gpe)
	if input.KeyCode == toggleKey then mainFrame.Visible = not mainFrame.Visible
	elseif isBinding and not gpe then
		cloneKey = input.KeyCode
		bindBtn.Text = cloneKey.Name:upper()
		isBinding = false
	elseif cloneKey and input.KeyCode == cloneKey and not gpe then doClone()
	end
end)

hideBtn.MouseButton1Click:Connect(function() mainFrame.Visible = false end)
fullBtn.MouseButton1Click:Connect(function()
	if not isFull then
		normalPos = mainFrame.Position
		mainFrame:TweenSizeAndPosition(UDim2.new(1, 0, 1, 0), UDim2.new(0, 0, 0, 0), "Out", "Quart", 0.4, true)
		fullBtn.Text = "❐"
	else
		mainFrame:TweenSizeAndPosition(normalSize, normalPos, "Out", "Quart", 0.4, true)
		fullBtn.Text = "▢"
	end
	isFull = not isFull
end)

local dragging, dragStart, startPos
topBar.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 and not isFull then
		dragging = true dragStart = input.Position startPos = mainFrame.Position
	end
end)
UserInputService.InputChanged:Connect(function(input)
	if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
		local delta = input.Position - dragStart
		mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)
UserInputService.InputEnded:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end end)

spawn(function() pcall(function() ContentProvider:PreloadAsync({bgImage}) end) end)
