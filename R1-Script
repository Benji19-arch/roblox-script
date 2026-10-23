-- LocalScript pour votre experience Roblox Studio.
-- Emplacement : StarterPlayer > StarterPlayerScripts.
-- Clic droit : viser. Inser : menu. Distances en studs.
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Input = game:GetService("UserInputService")
local player = Players.LocalPlayer
local config = {
	ESP = true, Boxes = true, Names = true, Health = true,
	Distance = true, Aim = true, Circle = true, TeamCheck = true,
	Fov = 120, Smooth = 12, Range = 3000,
}
local records, connections = {}, {}
local aiming = false
local gui = Instance.new("ScreenGui")
gui.Name = "StudioESP"
gui.IgnoreGuiInset = true
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")
local function connect(event, callback)
	table.insert(connections, event:Connect(callback))
end
local function make(class, parent, properties)
	local instance = Instance.new(class)
	for key, value in properties do instance[key] = value end
	instance.Parent = parent
	return instance
end
local circle = make("Frame", gui, {
	AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5),
	BackgroundTransparency = 1,
})
make("UICorner", circle, {CornerRadius = UDim.new(1, 0)})
make("UIStroke", circle, {Color = Color3.new(1, 1, 1), Thickness = 1, Transparency = 0.3})
local panel = make("Frame", gui, {
	Position = UDim2.fromOffset(24, 80), Size = UDim2.fromOffset(250, 420),
	BackgroundColor3 = Color3.fromRGB(24, 26, 36), BorderSizePixel = 0,
})
make("UICorner", panel, {CornerRadius = UDim.new(0, 10)})
make("TextLabel", panel, {
	Position = UDim2.fromOffset(10, 8), Size = UDim2.new(1, -20, 0, 32),
	BackgroundTransparency = 1, Text = "STUDIO / ESP + VISEE",
	TextColor3 = Color3.new(1, 1, 1), Font = Enum.Font.GothamBold, TextSize = 15,
})
local row = 46
local function button()
	local item = make("TextButton", panel, {
		Position = UDim2.fromOffset(12, row), Size = UDim2.new(1, -24, 0, 27),
		BackgroundColor3 = Color3.fromRGB(39, 42, 56), BorderSizePixel = 0,
		TextColor3 = Color3.new(1, 1, 1), Font = Enum.Font.Gotham, TextSize = 12,
	})
	row += 31
	return item
end
for _, option in {
	{"ESP", "ESP"}, {"Boxes", "Cadres"}, {"Names", "Noms"}, {"Health", "Vie"},
	{"Distance", "Distance"}, {"Aim", "Aide a la visee"},
	{"Circle", "Cercle de visee"}, {"TeamCheck", "Ignorer les allies"},
} do
	local key, title = option[1], option[2]
	local item = button()
	local function refresh() item.Text = title .. " : " .. (config[key] and "ON" or "OFF") end
	connect(item.Activated, function() config[key] = not config[key]; refresh() end)
	refresh()
end
local fov = button()
fov.Text = "Rayon : 120 px"
connect(fov.Activated, function()
	config.Fov = config.Fov == 240 and 60 or config.Fov + 60
	fov.Text = "Rayon : " .. config.Fov .. " px"
end)
local smoothing = button()
smoothing.Text = "Lissage : 12"
connect(smoothing.Activated, function()
	config.Smooth = config.Smooth == 24 and 6 or config.Smooth * 2
	smoothing.Text = "Lissage : " .. config.Smooth
end)
make("TextLabel", panel, {
	Position = UDim2.fromOffset(10, 365), Size = UDim2.new(1, -20, 0, 45),
	BackgroundTransparency = 1, Text = "Clic droit : viser | Inser : menu\nPortee : 3000 studs",
	TextColor3 = Color3.new(1, 1, 1), Font = Enum.Font.Gotham, TextSize = 11,
})
local function remove(other)
	local record = records[other]
	if record then
		record.highlight:Destroy()
		record.box:Destroy()
		record.info:Destroy()
		records[other] = nil
	end
end
local function add(other, character)
	remove(other)
	local color = Color3.fromRGB(255, 85, 100)
	local highlight = make("Highlight", gui, {
		Adornee = character, FillColor = color, OutlineColor = color,
		FillTransparency = 0.8, DepthMode = Enum.HighlightDepthMode.AlwaysOnTop,
	})
	local box = make("SelectionBox", gui, {
		Adornee = character, Color3 = color, LineThickness = 0.035, SurfaceTransparency = 1,
	})
	local info = make("BillboardGui", gui, {
		Size = UDim2.fromOffset(220, 58), StudsOffsetWorldSpace = Vector3.new(0, 3, 0),
		AlwaysOnTop = true,
	})
	local text = make("TextLabel", info, {
		Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1,
		TextColor3 = Color3.new(1, 1, 1), TextStrokeTransparency = 0.25,
		Font = Enum.Font.GothamBold, TextSize = 13,
	})
	local record = {character = character, highlight = highlight, box = box, info = info, text = text}
	records[other] = record
	return record
end
connect(Input.InputBegan, function(input, processed)
	if processed then return end
	if input.KeyCode == Enum.KeyCode.Insert then panel.Visible = not panel.Visible end
	if input.UserInputType == Enum.UserInputType.MouseButton2 then aiming = true end
end)
connect(Input.InputEnded, function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton2 then aiming = false end
end)
connect(Input.WindowFocusReleased, function() aiming = false end)
connect(Players.PlayerRemoving, remove)
local binding = "StudioESP_" .. game:GetService("HttpService"):GenerateGUID(false)
RunService:BindToRenderStep(binding, Enum.RenderPriority.Camera.Value + 1, function(dt)
	local camera = workspace.CurrentCamera
	if not camera then return end
	circle.Size = UDim2.fromOffset(config.Fov * 2, config.Fov * 2)
	circle.Visible = config.Circle and config.Aim
	local center = camera.ViewportSize / 2
	local character = player.Character
	local localRoot = character and character:FindFirstChild("HumanoidRootPart")
	local localHumanoid = character and character:FindFirstChildOfClass("Humanoid")
	local canAim = aiming and config.Aim and localHumanoid and localHumanoid.Health > 0
		and not Input:GetFocusedTextBox()
	local target, best = nil, config.Fov
	local params = RaycastParams.new()
	params.FilterType = Enum.RaycastFilterType.Exclude
	params.FilterDescendantsInstances = character and {character} or {}
	for _, other in Players:GetPlayers() do
		if other == player then continue end
		local enemy = other.Character
		if not enemy then remove(other); continue end
		local record = records[other]
		if not record or record.character ~= enemy then record = add(other, enemy) end
		local humanoid = enemy:FindFirstChildOfClass("Humanoid")
		local head = enemy:FindFirstChild("Head")
		local root = enemy:FindFirstChild("HumanoidRootPart")
		local allied = config.TeamCheck and not player.Neutral and not other.Neutral and player.Team == other.Team
		local distance = root and localRoot and (root.Position - localRoot.Position).Magnitude or math.huge
		local eligible = not allied and humanoid ~= nil and humanoid.Health > 0 and head ~= nil and distance <= config.Range
		local visible = config.ESP and eligible
		record.highlight.Enabled = visible
		record.box.Visible = visible and config.Boxes
		record.info.Enabled = visible and (config.Names or config.Health or config.Distance)
		record.info.Adornee = head
		if visible then
			local lines = {}
			if config.Names then table.insert(lines, other.DisplayName) end
			if config.Health then table.insert(lines, string.format("Vie : %.0f / %.0f", humanoid.Health, humanoid.MaxHealth)) end
			if config.Distance then table.insert(lines, string.format("%.0f studs", distance)) end
			record.text.Text = table.concat(lines, "\n")
		end
		if canAim and eligible then
			local point, onScreen = camera:WorldToViewportPoint(head.Position)
			local delta = (Vector2.new(point.X, point.Y) - center).Magnitude
			if onScreen and delta < best then
				local hit = workspace:Raycast(camera.CFrame.Position, head.Position - camera.CFrame.Position, params)
				if not hit or hit.Instance:IsDescendantOf(enemy) then target, best = head, delta end
			end
		end
	end
	if target then
		camera.CFrame = camera.CFrame:Lerp(CFrame.lookAt(camera.CFrame.Position, target.Position), 1 - math.exp(-config.Smooth * dt))
	end
end)
connect(script.Destroying, function()
	RunService:UnbindFromRenderStep(binding)
	for _, connection in connections do connection:Disconnect() end
	for other in records do remove(other) end
	gui:Destroy()
end)
