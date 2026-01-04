--------------------------------------------------
-- ZERO HUB | BASE COMPLETA
--------------------------------------------------

-- SERVICES
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TeleportService = game:GetService("TeleportService")
local player = Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")

--------------------------------------------------
-- KEY SYSTEM (MODELO)
--------------------------------------------------
local VALID_KEY = "ZERO-2026"

do
	local keyGui = Instance.new("ScreenGui", player.PlayerGui)
	local box = Instance.new("TextBox", keyGui)
	box.Size = UDim2.new(0,280,0,45)
	box.Position = UDim2.new(0.5,-140,0.5,-22)
	box.PlaceholderText = "Digite a KEY"
	box.Text = ""
	box.TextSize = 16
	box.BackgroundColor3 = Color3.fromRGB(20,20,20)
	box.TextColor3 = Color3.new(1,1,1)

	box.FocusLost:Connect(function()
		if box.Text == VALID_KEY then
			keyGui:Destroy()
		else
			player:Kick("Key inválida")
		end
	end)
end

--------------------------------------------------
-- VARIÁVEIS
--------------------------------------------------
local Flags = {
	AutoFarm = false,
	BringMob = false,
	AutoBoss = false,
	AutoStats = false,
	AutoHaki = false,
	AutoFruit = false,
	MirageDetect = false,
	SeaDetect = false,
	WhiteScreen = false
}

--------------------------------------------------
-- GUI
--------------------------------------------------
local gui = Instance.new("ScreenGui", player.PlayerGui)
gui.ResetOnSpawn = false

-- BOTÃO FLUTUANTE
local float = Instance.new("TextButton", gui)
float.Size = UDim2.new(0,52,0,52)
float.Position = UDim2.new(0,15,0.5,-26)
float.Text = "Z"
float.Font = Enum.Font.GothamBold
float.TextSize = 20
float.TextColor3 = Color3.new(1,1,1)
float.BackgroundColor3 = Color3.fromRGB(0,120,255)
float.Active = true
float.Draggable = true
Instance.new("UICorner", float).CornerRadius = UDim.new(1,0)

-- HUB
local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0,520,0,340)
main.Position = UDim2.new(0.5,-260,0.5,-170)
main.BackgroundColor3 = Color3.fromRGB(18,18,18)
main.Visible = false
main.Active = true
main.Draggable = true
Instance.new("UICorner", main).CornerRadius = UDim.new(0,10)

-- SIDEBAR
local side = Instance.new("Frame", main)
side.Size = UDim2.new(0,130,1,0)
side.BackgroundColor3 = Color3.fromRGB(14,14,14)

-- CONTENT
local content = Instance.new("Frame", main)
content.Position = UDim2.new(0,130,0,0)
content.Size = UDim2.new(1,-130,1,0)
content.BackgroundTransparency = 1

--------------------------------------------------
-- SISTEMA DE ABAS
--------------------------------------------------
local tabs = {}

local function CreateTab(name)
	local b = Instance.new("TextButton", side)
	b.Size = UDim2.new(1,0,0,40)
	b.Text = name
	b.Font = Enum.Font.Gotham
	b.TextSize = 14
	b.TextColor3 = Color3.new(1,1,1)
	b.BackgroundColor3 = Color3.fromRGB(30,30,30)

	local f = Instance.new("Frame", content)
	f.Size = UDim2.new(1,0,1,0)
	f.Visible = false
	f.BackgroundTransparency = 1

	b.MouseButton1Click:Connect(function()
		for _,v in pairs(tabs) do v.Visible = false end
		f.Visible = true
	end)

	tabs[name] = f
	return f
end

--------------------------------------------------
-- TOGGLE
--------------------------------------------------
local function Toggle(parent, text, flag)
	local btn = Instance.new("TextButton", parent)
	btn.Size = UDim2.new(1,-20,0,38)
	btn.Position = UDim2.new(0,10,0,10 + (#parent:GetChildren()*42))
	btn.BackgroundColor3 = Color3.fromRGB(35,35,35)
	btn.Text = text.." : OFF"
	btn.TextColor3 = Color3.new(1,1,1)
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 13
	Instance.new("UICorner", btn).CornerRadius = UDim.new(0,8)

	btn.MouseButton1Click:Connect(function()
		Flags[flag] = not Flags[flag]
		btn.Text = text.." : "..(Flags[flag] and "ON" or "OFF")
		btn.BackgroundColor3 = Flags[flag] and Color3.fromRGB(0,120,255) or Color3.fromRGB(35,35,35)
	end)
end

--------------------------------------------------
-- ABAS
--------------------------------------------------
local FarmTab = CreateTab("Farm")
local PlayerTab = CreateTab("Player")
local WorldTab = CreateTab("World")

tabs["Farm"].Visible = true

--------------------------------------------------
-- BOTÕES
--------------------------------------------------
Toggle(FarmTab,"Auto Farm", "AutoFarm")
Toggle(FarmTab,"Bring Mob", "BringMob")
Toggle(FarmTab,"Auto Boss", "AutoBoss")
Toggle(FarmTab,"Auto Fruit", "AutoFruit")

Toggle(PlayerTab,"Auto Stats", "AutoStats")
Toggle(PlayerTab,"Auto Haki", "AutoHaki")

Toggle(WorldTab,"Detect Mirage", "MirageDetect")
Toggle(WorldTab,"Detect Sea Event", "SeaDetect")
Toggle(WorldTab,"White Screen", "WhiteScreen")

--------------------------------------------------
-- LOOPS FUNCIONAIS
--------------------------------------------------

-- FARM / BOSS
task.spawn(function()
	while task.wait(0.4) do
		for _,mob in pairs(workspace.Enemies:GetChildren()) do
			if mob:FindFirstChild("HumanoidRootPart")
			and mob:FindFirstChild("Humanoid")
			and mob.Humanoid.Health > 0 then

				if Flags.AutoFarm and not mob.Name:find("Boss") then
					hrp.CFrame = mob.HumanoidRootPart.CFrame * CFrame.new(0,0,3)
				end

				if Flags.AutoBoss and mob.Name:find("Boss") then
					hrp.CFrame = mob.HumanoidRootPart.CFrame * CFrame.new(0,0,4)
				end

				if Flags.BringMob then
					mob.HumanoidRootPart.CFrame = hrp.CFrame * CFrame.new(0,0,-3)
				end
			end
		end
	end
end)

-- AUTO STATS
task.spawn(function()
	while task.wait(1) do
		if Flags.AutoStats then
			pcall(function()
				ReplicatedStorage.Remotes.CommF_:InvokeServer("AddPoint","Melee",1)
			end)
		end
	end
end)

-- AUTO HAKI
task.spawn(function()
	while task.wait(2) do
		if Flags.AutoHaki then
			pcall(function()
				ReplicatedStorage.Remotes.CommF_:InvokeServer("Buso")
			end)
		end
	end
end)

-- AUTO FRUIT
task.spawn(function()
	while task.wait(2) do
		if Flags.AutoFruit then
			for _,v in pairs(workspace:GetChildren()) do
				if v:IsA("Tool") and v.Name:find("Fruit") then
					hrp.CFrame = v.Handle.CFrame
				end
			end
		end
	end
end)

-- DETECT MIRAGE
task.spawn(function()
	while task.wait(5) do
		if Flags.MirageDetect and workspace:FindFirstChild("MirageIsland") then
			warn("🌙 MIRAGE DETECTADA!")
		end
	end
end)

-- DETECT SEA EVENT
task.spawn(function()
	while task.wait(4) do
		if Flags.SeaDetect then
			for _,v in pairs(workspace:GetChildren()) do
				if v.Name:lower():find("sea") then
					warn("🌊 SEA EVENT:", v.Name)
				end
			end
		end
	end
end)

-- WHITE SCREEN
task.spawn(function()
	while task.wait(1) do
		if Flags.WhiteScreen then
			game.Lighting.Brightness = 0
			game.Lighting.FogEnd = 9e9
		else
			game.Lighting.Brightness = 2
		end
	end
end)

--------------------------------------------------
-- ABRIR / FECHAR
--------------------------------------------------
float.MouseButton1Click:Connect(function()
	main.Visible = not main.Visible
end)
