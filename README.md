-- Mobile Admin Hub - c00lgui Style
-- Apenas para testes locais/autorizados!

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local Mouse = LocalPlayer:GetMouse()

local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "MobileAdminHub"
gui.ResetOnSpawn = false

-- Base do painel
local panel = Instance.new("Frame", gui)
panel.Size = UDim2.new(0, 300, 0, 500)
panel.Position = UDim2.new(0, 10, 0.2, 0)
panel.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
panel.BorderSizePixel = 0
panel.Active = true
panel.Draggable = true

-- Título
local title = Instance.new("TextLabel", panel)
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "👑 Mobile Admin Hub"
title.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
title.TextColor3 = Color3.fromRGB(0, 255, 255)
title.Font = Enum.Font.Code
title.TextSize = 22

-- Botão criador
local function CreateMobileButton(text, yOffset, callback)
	local btn = Instance.new("TextButton", panel)
	btn.Size = UDim2.new(0, 280, 0, 40)
	btn.Position = UDim2.new(0, 10, 0, yOffset)
	btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.Text = text
	btn.Font = Enum.Font.SourceSansBold
	btn.TextSize = 20
	btn.MouseButton1Click:Connect(callback)
end

-- 1. Fly Touch
local flying = false
local function ToggleFly()
	flying = not flying
	local char = LocalPlayer.Character
	if not char or not char:FindFirstChild("HumanoidRootPart") then return end
	local hrp = char.HumanoidRootPart

	local bg = Instance.new("BodyGyro", hrp)
	bg.P = 9e4
	bg.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
	bg.CFrame = hrp.CFrame

	local bv = Instance.new("BodyVelocity", hrp)
	bv.Velocity = Vector3.zero
	bv.MaxForce = Vector3.new(9e9, 9e9, 9e9)

	spawn(function()
		while flying and char and hrp and bv do
			bv.Velocity = Vector3.new(0, 50, 0)
			wait()
		end
		bg:Destroy()
		bv:Destroy()
	end)
end

-- 2. Auto Clicker
local autoClick = false
local function ToggleAutoClick()
	autoClick = not autoClick
	spawn(function()
		while autoClick do
			mouse1click()
			wait(0.05)
		end
	end)
end

-- 3. ESP
local espOn = false
local function ToggleESP()
	espOn = not espOn
	for _, p in ipairs(Players:GetPlayers()) do
		if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
			if espOn then
				local esp = Instance.new("BillboardGui", p.Character.Head)
				esp.Name = "ESP"
				esp.Size = UDim2.new(0, 100, 0, 20)
				esp.AlwaysOnTop = true
				local label = Instance.new("TextLabel", esp)
				label.Text = p.Name
				label.Size = UDim2.new(1, 0, 1, 0)
				label.BackgroundTransparency = 1
				label.TextColor3 = Color3.new(1, 0, 0)
			else
				local existing = p.Character.Head:FindFirstChild("ESP")
				if existing then
					existing:Destroy()
				end
			end
		end
	end
end

-- 4. Spin
local spinning = false
local function ToggleSpin()
	spinning = not spinning
	spawn(function()
		while spinning do
			if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
				LocalPlayer.Character.HumanoidRootPart.CFrame *= CFrame.Angles(0, math.rad(30), 0)
			end
			wait(0.03)
		end
	end)
end

-- 5. Escudo infinito
local function InfiniteShield()
	if LocalPlayer.Character then
		Instance.new("ForceField", LocalPlayer.Character)
	end
end

-- 6. Velocidade
local function SpeedBoost()
	if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
		LocalPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = 80
	end
end

-- 7. Super Pulo
local function HighJump()
	if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
		LocalPlayer.Character:FindFirstChildOfClass("Humanoid").JumpPower = 150
	end
end

-- 8. Aimbot simples
local aimbot = false
local function ToggleAimbot()
	aimbot = not aimbot
	RunService.RenderStepped:Connect(function()
		if not aimbot then return end
		local target = nil
		local dist = math.huge
		for _, p in ipairs(Players:GetPlayers()) do
			if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
				local mag = (p.Character.Head.Position - workspace.CurrentCamera.CFrame.Position).magnitude
				if mag < dist then
					dist = mag
					target = p
				end
			end
		end
		if target then
			workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, target.Character.Head.Position)
		end
	end)
end

-- 9. Knife (grab)
local function GrabKnife()
	local tool = Instance.new("Tool")
	tool.Name = "Knife"
	tool.RequiresHandle = true
	local part = Instance.new("Part")
	part.Name = "Handle"
	part.Size = Vector3.new(1,4,1)
	part.BrickColor = BrickColor.new("Bright red")
	part.Parent = tool
	tool.Parent = LocalPlayer.Backpack
end

-- 10. Teleport to Tap
local function TeleportToTouch()
	local tapped = false
	local conn
	conn = UIS.TouchTap:Connect(function(pos, gpe)
		if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
			local ray = workspace:Raycast(workspace.CurrentCamera.CFrame.Position, workspace.CurrentCamera.CFrame.LookVector * 1000)
			if ray then
				LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(ray.Position + Vector3.new(0, 5, 0))
			end
		end
		conn:Disconnect()
	end)
end

-- Criar os botões
CreateMobileButton("🚀 Fly", 50, ToggleFly)
CreateMobileButton("🖱️ AutoClick", 100, ToggleAutoClick)
CreateMobileButton("👁️ ESP", 150, ToggleESP)
CreateMobileButton("🔄 Spin", 200, ToggleSpin)
CreateMobileButton("🛡️ Escudo", 250, InfiniteShield)
CreateMobileButton("🏃 Velocidade", 300, SpeedBoost)
CreateMobileButton("🦘 Pulo Alto", 350, HighJump)
CreateMobileButton("🎯 Aimbot", 400, ToggleAimbot)
CreateMobileButton("🔪 Pegar Faca", 450, GrabKnife)
CreateMobileButton("🌀 TP Toque", 500, TeleportToTouch)
