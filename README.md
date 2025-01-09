local redzlib = loadstring(game:HttpGet("https://raw.githubusercontent.com/REDzHUB/RedzLibV5/main/Source.Lua"))()

local Window = redzlib:MakeWindow({
  Title = "Test",
  SubTitle = "hello",
  SaveFolder = "Redz Config"
})

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Debris = game:GetService("Debris")

local localPlayer = Players.LocalPlayer
local gui = Instance.new("ScreenGui", localPlayer.PlayerGui)

function spawnBullet(player)
    local bullet = Instance.new("Part")
    bullet.Size = Vector3.new(0.2, 0.2, 0.2)
    bullet.Position = player.Character.Head.Position
    bullet.Velocity = player.Character.Head.CFrame.LookVector * 100
    bullet.Anchored = false
    bullet.CanCollide = false
    bullet.BrickColor = BrickColor.new("Bright Yellow")
    bullet.Parent = workspace
    Debris:AddItem(bullet, 5)
end

function updateDistances()
    for _, otherPlayer in pairs(Players:GetPlayers()) do
        if otherPlayer ~= localPlayer and otherPlayer.Character and otherPlayer.Character:FindFirstChild("HumanoidRootPart") then
            local distance = (otherPlayer.Character.HumanoidRootPart.Position - localPlayer.Character.HumanoidRootPart.Position).magnitude
            local label = gui:FindFirstChild(otherPlayer.Name)
            if not label then
                label = Instance.new("TextLabel", gui)
                label.Name = otherPlayer.Name
                label.Size = UDim2.new(0, 200, 0, 50)
                label.TextColor3 = Color3.new(1, 1, 1)
                label.BackgroundTransparency = 1
            end
            label.Text = otherPlayer.Name .. " - " .. math.floor(distance) .. "m"
        end
    end
end

function setPlayerColor(player, isAlly)
    local color = isAlly and Color3.new(0, 1, 0) or Color3.new(1, 0, 0)
    if player.Character and player.Character:FindFirstChild("Humanoid") then
        for _, part in pairs(player.Character:GetChildren()) do
            if part:IsA("BasePart") then
                part.Color = color
            end
        end
    end
end

Players.PlayerAdded:Connect(function(player)
    local isAlly = math.random(1, 2) == 1
    player.CharacterAdded:Connect(function()
        setPlayerColor(player, isAlly)
        player.Character.HumanoidRootPart.Touched:Connect(function()
            spawnBullet(player)
        end)
    end)
end)