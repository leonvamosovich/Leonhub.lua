-- MM2 // LEEK COMPACT HUB (Role ESP + Gun Drop ESP + Speed + Mini-Dock)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local oldGui = playerGui:FindFirstChild("MM2_LeekCleanHub")
if oldGui then
	oldGui:Destroy()
end

local state = {
	minimized = false,
	roleEsp = false,
	gunEsp = false,
	speedBoost = false,
	infJump = false,
	noclip = false,
	walkSpeed = 24
}

local function makeCorner(obj, r)
	local c = Instance.new("UICorner")
	c.CornerRadius = UDim.new(0, r or 6)
	c.Parent = obj
end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MM2_LeekCleanHub"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.Parent = playerGui

-- Мини-плашка в свёрнутом режиме
local miniChip = Instance.new("TextButton")
miniChip.Size = UDim2.new(0, 108, 0, 28)
miniChip.Position = UDim2.new(0.03, 0, 0.30, 0)
miniChip.BackgroundColor3 = Color3.fromRGB(20, 23, 30)
miniChip.Text = "MM2 // LEEK +"
miniChip.TextColor3 = Color3.fromRGB(235, 240, 255)
miniChip.Font = Enum.Font.GothamBold
miniChip.TextSize = 11
miniChip.Active = true
miniChip.Draggable = true
miniChip.Visible = false
miniChip.Parent = screenGui
makeCorner(miniChip, 7)

-- Основное компактное окно
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 196, 0, 204)
frame.Position = UDim2.new(0.03, 0, 0.30, 0)
frame.BackgroundColor3 = Color3.fromRGB(18, 20, 26)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true
frame.Parent = screenGui
makeCorner(frame, 9)

local header = Instance.new("Frame")
header.Size = UDim2.new(1, 0, 0, 26)
header.BackgroundColor3 = Color3.fromRGB(26, 30, 38)
header.BorderSizePixel = 0
header.Parent = frame
makeCorner(header, 9)

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -54, 1, 0)
title.Position = UDim2.new(0, 8, 0, 0)
title.BackgroundTransparency = 1
title.Text = "MM2 // LEEK HUB"
title.TextColor3 = Color3.fromRGB(235, 240, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 11
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = header

local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 20, 0, 18)
minBtn.Position = UDim2.new(1, -46, 0.5, -9)
minBtn.BackgroundColor3 = Color3.fromRGB(45, 50, 62)
minBtn.Text = "—"
minBtn.TextColor3 = Color3.fromRGB(235, 235, 235)
minBtn.Font = Enum.Font.GothamBold
minBtn.TextSize = 11
minBtn.Parent = header
makeCorner(minBtn, 5)

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 20, 0, 18)
closeBtn.Position = UDim2.new(1, -23, 0.5, -9)
closeBtn.BackgroundColor3 = Color3.fromRGB(120, 42, 50)
closeBtn.Text = "×"
closeBtn.TextColor3 = Color3.fromRGB(255, 235, 235)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 12
closeBtn.Parent = header
makeCorner(closeBtn, 5)

local function createToggle(order, labelText, key, onToggle)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(1, -14, 0, 28)
	btn.Position = UDim2.new(0, 7, 0, 32 + (order - 1) * 33)
	btn.BackgroundColor3 = Color3.fromRGB(32, 36, 45)
	btn.Text = labelText .. ": OFF"
	btn.TextColor3 = Color3.fromRGB(220, 225, 235)
	btn.Font = Enum.Font.GothamMedium
	btn.TextSize = 11
	btn.Parent = frame
	makeCorner(btn, 6)

	btn.MouseButton1Click:Connect(function()
		state[key] = not state[key]
		local active = state[key]
		btn.Text = labelText .. (active and ": ON" or ": OFF")
		btn.BackgroundColor3 = active and Color3.fromRGB(42, 95, 65) or Color3.fromRGB(32, 36, 45)
		if onToggle then
			onToggle(active)
		end
	end)
end

local function hasTool(plr, toolName)
	local bp = plr:FindFirstChild("Backpack")
	local ch = plr.Character
	return (bp and bp:FindFirstChild(toolName)) or (ch and ch:FindFirstChild(toolName))
end

local function clearRoleESP()
	for _, plr in ipairs(Players:GetPlayers()) do
		if plr.Character then
			local hl = plr.Character:FindFirstChild("LeekRoleHL")
			if hl then hl:Destroy() end
		end
	end
end

local function clearGunESP()
	for _, obj in ipairs(Workspace:GetDescendants()) do
		if obj.Name == "LeekGunHL" then
			obj:Destroy()
		end
	end
end

createToggle(1, "ROLE ESP", "roleEsp", function(active)
	if not active then clearRoleESP() end
end)

createToggle(2, "GUN DROP ESP", "gunEsp", function(active)
	if not active then clearGunESP() end
end)

createToggle(3, "SPEED 24", "speedBoost", function(active)
	if not active then
		local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
		if hum then hum.WalkSpeed = 16 end
	end
end)

createToggle(4, "INF JUMP", "infJump")
createToggle(5, "NOCLIP", "noclip")

minBtn.MouseButton1Click:Connect(function()
	miniChip.Position = frame.Position
	frame.Visible = false
	miniChip.Visible = true
end)

miniChip.MouseButton1Click:Connect(function()
	frame.Position = miniChip.Position
	miniChip.Visible = false
	frame.Visible = true
end)

closeBtn.MouseButton1Click:Connect(function()
	clearRoleESP()
	clearGunESP()
	screenGui:Destroy()
end)

UserInputService.JumpRequest:Connect(function()
	if state.infJump then
		local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
		if hum then
			hum:ChangeState(Enum.HumanoidStateType.Jumping)
		end
	end
end)

RunService.Stepped:Connect(function()
	if state.noclip and player.Character then
		for _, part in ipairs(player.Character:GetDescendants()) do
			if part:IsA("BasePart") then
				part.CanCollide = false
			end
		end
	end
end)

RunService.RenderStepped:Connect(function()
	if state.roleEsp then
		for _, plr in ipairs(Players:GetPlayers()) do
			if plr ~= player and plr.Character then
				local hl = plr.Character:FindFirstChild("LeekRoleHL")
				if not hl then
					hl = Instance.new("Highlight")
					hl.Name = "LeekRoleHL"
					hl.FillTransparency = 0.55
					hl.OutlineTransparency = 0
					hl.Parent = plr.Character
				end
				if hasTool(plr, "Knife") then
					hl.FillColor = Color3.fromRGB(255, 60, 60)
				elseif hasTool(plr, "Gun") then
					hl.FillColor = Color3.fromRGB(65, 145, 255)
				else
					hl.FillColor = Color3.fromRGB(85, 220, 115)
				end
			end
		end
	end

	if state.gunEsp then
		local gunDrop = Workspace:FindFirstChild("GunDrop", true)
		if gunDrop and not gunDrop:FindFirstChild("LeekGunHL") then
			local ghl = Instance.new("Highlight")
			ghl.Name = "LeekGunHL"
			ghl.FillColor = Color3.fromRGB(255, 215, 45)
			ghl.FillTransparency = 0.3
			ghl.OutlineTransparency = 0
			ghl.Parent = gunDrop
		end
	end

	if state.speedBoost then
		local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
		if hum then
			hum.WalkSpeed = state.walkSpeed
		end
	end
end)
