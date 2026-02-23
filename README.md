local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local ContentProvider = game:GetService("ContentProvider")

local player = Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ArgoMenu_Translucent_V33"
screenGui.ResetOnSpawn = false
screenGui.Parent = player.PlayerGui

-- BIẾN ĐIỀU KHIỂN
local toggleKey = Enum.KeyCode.RightControl
local cloneKey = nil
local isBinding = false
local targetUserId = nil
local isFull = false
local normalSize = UDim2.new(0, 320, 0, 450)
local normalPos = UDim2.new(0.5, -160, 0.5, -225)

-- FONT CHỮ ĐƠN GIẢN
local SIMPLE_FONT = Enum.Font.SourceSans

-- CẤU HÌNH ẢNH
local IMAGE_ID = "81487620002600"
local PHOTO_URL = "https://www.roblox.com/asset-thumbnail/image?assetId="..IMAGE_ID.."&width=420&height=420&format=png"

-- HÀM TẠO PHONG CÁCH TRONG SUỐT
local function applyGlassStyle(obj, transparency, cornerRadius)
	obj.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- Màu nền tối để giữ độ tương phản
	obj.BackgroundTransparency = transparency or 0.7 -- Tăng độ trong suốt để nhìn xuyên thấu
	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, cornerRadius or 10)
	corner.Parent = obj
	
	local stroke = Instance.new("UIStroke")
	stroke.Thickness = 1
	stroke.Color = Color3.new(1, 1, 1)
	stroke.Transparency = 0.7
	stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
	stroke.Parent = obj
end

-- GIAO DIỆN CHÍNH
local mainFrame = Instance.new("Frame")
mainFrame.Size = normalSize
mainFrame.Position = normalPos
mainFrame.ClipsDescendants = true 
mainFrame.BackgroundColor3 = Color3.new(0, 0, 0)
mainFrame.Parent = screenGui
applyGlassStyle(mainFrame, 0.65, 15) -- 0.65 giúp nhìn xuyên qua được game phía sau

-- NỀN ẢNH (VỪA RÕ MẶT VỪA TRONG SUỐT)
local bgImage = Instance.new("ImageLabel")
bgImage.Size = UDim2.new(1, 0, 1, 0)
bgImage.BackgroundTransparency = 1
bgImage.Image = PHOTO_URL
bgImage.ImageTransparency = 0.5 -- Điều chỉnh để thấy cả ảnh và game phía sau
bgImage.ScaleType = Enum.ScaleType.Crop
bgImage.ZIndex = 0
bgImage.Parent = mainFrame
Instance.new("UICorner", bgImage).CornerRadius = UDim.new(0, 15)

-- THANH TIÊU ĐỀ
local topBar = Instance.new("Frame")
topBar.Size = UDim2.new(1, 0, 0, 40)
topBar.BackgroundTransparency = 1
topBar.ZIndex = 10
topBar.Parent = mainFrame

local title = Instance.new("TextLabel")
title.Text = "   ARGO MENU"
title.Size = UDim2.new(0.5, 0, 1, 0)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = SIMPLE_FONT
title.TextSize = 17
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = topBar

-- NÚT ĐIỀU KHIỂN TRÊN
local function createTopBtn(text, pos)
	local btn = Instance.new("TextButton")
	btn.Text = text
	btn.Size = UDim2.new(0, 26, 0, 26)
	btn.Position = pos
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.Font = SIMPLE_FONT
	btn.TextSize = 14
	btn.ZIndex = 12
	btn.Parent = topBar
	applyGlassStyle(btn, 0.5, 6)
	return btn
end
local hideBtn = createTopBtn("-", UDim2.new(1, -70, 0.5, -13))
local fullBtn = createTopBtn("▢", UDim2.new(1, -35, 0.5, -13))

-- NỘI DUNG
local content = Instance.new("Frame")
content.Size = UDim2.new(1, -40, 1, -70)
content.Position = UDim2.new(0, 20, 0, 50)
content.BackgroundTransparency = 1
content.Parent = mainFrame

-- AVATAR
local avatarContainer = Instance.new("Frame")
avatarContainer.Size = UDim2.new(0, 80, 0, 80)
avatarContainer.Position = UDim2.new(0.5, -40, 0, 5)
avatarContainer.BackgroundTransparency = 0.3
avatarContainer.BackgroundColor3 = Color3.new(0,0,0)
avatarContainer.Parent = content
Instance.new("UICorner", avatarContainer).CornerRadius = UDim.new(1, 0)

local avatarImage = Instance.new("ImageLabel")
avatarImage.Size = UDim2.new(1, 0, 1, 0)
avatarImage.Image = "rbxthumb://type=AvatarHeadShot&id="..player.UserId.."&w=150&h=150"
avatarImage.BackgroundTransparency = 1
avatarImage.Parent = avatarContainer
Instance.new("UICorner", avatarImage).CornerRadius = UDim.new(1, 0)

-- HÀM TẠO NÚT
local function createButton(text, pos, size, customColor)
	local btn = Instance.new("TextButton")
	btn.Text = text
	btn.Size = size or UDim2.new(1, 0, 0, 38)
	btn.Position = pos
	btn.Font = SIMPLE_FONT
	btn.TextSize = 15
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.Parent = content
	applyGlassStyle(btn, 0.5, 8) -- Nút cũng hơi trong suốt cho đồng bộ
	if customColor then 
		btn.BackgroundColor3 = customColor
		btn.BackgroundTransparency = 0.3 
	end
	return btn
end

local userBox = Instance.new("TextBox")
userBox.Size = UDim2.new(0.75, 0, 0, 35)
userBox.Position = UDim2.new(0, 0, 0, 100)
userBox.PlaceholderText = "Enter Username..."
userBox.Text = ""
userBox.Font = SIMPLE_FONT
userBox.TextSize = 14
userBox.TextColor3 = Color3.new(1, 1, 1)
userBox.Parent = content
applyGlassStyle(userBox, 0.6, 6)

local checkBtn = createButton("CHECK", UDim2.new(0.8, 0, 0, 100), UDim2.new(0.2, 0, 0, 35))
local cloneBtn = createButton("CREATE CLONE", UDim2.new(0, 0, 0, 150), UDim2.new(0.68, 0, 0, 40))
local bindBtn = createButton("BIND", UDim2.new(0.72, 0, 0, 150), UDim2.new(0.28, 0, 0, 40))

-- NÚT DELETE TỐI DƯỚI CÙNG
local deleteBtn = createButton(
	"DELETE ALL CLONES", 
	UDim2.new(0, 0, 1, -40), 
	UDim2.new(1, 0, 0, 40), 
	Color3.fromRGB(30, 30, 35)
)

-- LOGIC CLONE (Giữ nguyên)
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

-- XỬ LÝ NHẤN PHÍM
UserInputService.InputBegan:Connect(function(input, gpe)
	if input.KeyCode == toggleKey then
		mainFrame.Visible = not mainFrame.Visible
	elseif isBinding and not gpe then
		cloneKey = input.KeyCode
		bindBtn.Text = cloneKey.Name:upper()
		isBinding = false
	elseif cloneKey and input.KeyCode == cloneKey and not gpe then
		doClone()
	end
end)

-- KÉO THẢ & FULLSCREEN (Giữ nguyên)
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
