-- Inicialização do painel
local aimbotEnabled = false
local player = game.Players.LocalPlayer

local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local TextButton = Instance.new("TextButton")

ScreenGui.Parent = player:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

Frame.Parent = ScreenGui
Frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
Frame.Position = UDim2.new(0.02, 0, 0.1, 0)
Frame.Size = UDim2.new(0, 200, 0, 100)

TextButton.Parent = Frame
TextButton.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
TextButton.Position = UDim2.new(0, 0, 0.5, 0)
TextButton.Size = UDim2.new(1, 0, 0.5, 0)
TextButton.Font = Enum.Font.SourceSans
TextButton.Text = "Aimbot: OFF"
TextButton.TextColor3 = Color3.fromRGB(255, 255, 255)
TextButton.TextScaled = true

-- Função para alternar aimbot
TextButton.MouseButton1Click:Connect(function()
    aimbotEnabled = not aimbotEnabled
    TextButton.Text = aimbotEnabled and "Aimbot: ON" or "Aimbot: OFF"
end)

-- Função aimbot
local function aimAt(target)
    if aimbotEnabled and target then
        local mouse = player:GetMouse()
        mouse.TargetFilter = target
        mouse.Hit = target.CFrame
    end
end

-- Função para puxar os ataques para o player
local function pullAttacks(target)
    if aimbotEnabled and target then
        for _, tool in pairs(player.Backpack:GetChildren()) do
            if tool:IsA("Tool") then
                tool.GripPos = (target.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Unit
            end
        end
    end
end

-- Loop para buscar o alvo mais próximo
game:GetService("RunService").RenderStepped:Connect(function()
    if aimbotEnabled then
        local closestTarget = nil
        local closestDistance = math.huge
        
        for _, v in pairs(game.Workspace:GetChildren()) do
            if v:IsA("Model") and v:FindFirstChild("Humanoid") and v:FindFirstChild("HumanoidRootPart") then
                local distance = (v.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
                if distance < closestDistance then
                    closestDistance = distance
                    closestTarget = v
                end
            end
        end
        
        aimAt(closestTarget)
        pullAttacks(closestTarget)
    end
end)
