local Lib = loadstring(game:HttpGet("https://raw.githubusercontent.com/7yhx/kwargs_Ui_Library/main/source.lua"))()

local UI = Lib:Create{
   Theme = "Dark",
   Size = UDim2.new(0, 555, 0, 400)
}

local Main = UI:Tab{
   Name = "Main"
}

local Divider = Main:Divider{
   Name = "Main Interface"
}

local HealthBar = Main:CreateElement("Bar", {
   Name = "Vida",
   MaxValue = 100,
   Value = 100,
   Color = Color3.fromRGB(255, 0, 0),
})

local Crosshair = Main:CreateElement("Image", {
   Name = "Retículo",
   Image = "rbxassetid://<id_da_imagem_do_retículo>",
   Size = UDim2.new(0, 10, 0, 10),
   Position = UDim2.new(0.5, -5, 0.5, -5),
})

local AmmoCounter = Main:CreateElement("Text", {
   Name = "Munição",
   Text = "Munição: 30",
   Position = UDim2.new(1, -110, 1, -35),
   Color = Color3.fromRGB(255, 255, 255),
})

local QuitDivider = Main:Divider{
   Name = "Quit"
}

game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        local humanoid = character:WaitForChild("Humanoid")
        humanoid.HealthChanged:Connect(function(health)
            HealthBar:SetValue(health)
        end)
    end)
end)

game.ReplicatedStorage:WaitForChild("AmmoChanged").OnClientEvent:Connect(function(ammo)
    AmmoCounter:SetText("Munição: " .. ammo)
end)

local function marcarJogadores(jogador)
    local marcador = Instance.new("BillboardGui")
    marcador.Size = UDim2.new(1, 0, 1, 0)
    marcador.Adornee = jogador.Character.Head
    local label = Instance.new("TextLabel", marcador)
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = jogador.TeamColor == game.Players.LocalPlayer.TeamColor and "Aliado" or "Inimigo"
    label.TextColor3 = jogador.TeamColor == game.Players.LocalPlayer.TeamColor and Color3.new(0, 0, 1) or Color3.new(1, 0, 0)
    marcador.Parent = jogador.Character.Head
end

local function atualizarDistancia(jogador)
    local distancia = (jogador.Character.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).magnitude
    local marcadorDistancia = jogador.Character.Head:FindFirstChild("Distancia")
    if not marcadorDistancia then
        marcadorDistancia = Instance.new("BillboardGui", jogador.Character.Head)
        marcadorDistancia.Name = "Distancia"
        marcadorDistancia.Size = UDim2.new(1, 0, 1, 0)
        local label = Instance.new("TextLabel", marcadorDistancia)
        label.Size = UDim2.new(1, 0, 1, 0)
        label.BackgroundTransparency = 1
        label.TextColor3 = Color3.new(1, 1, 1)
    end
    marcadorDistancia.TextLabel.Text = string.format("Distância: %.2f", distancia)
end

local function criarBala()
    local bala = Instance.new("Part")
    bala.CanCollide = false
    bala.Touched:Connect(function(hit)
        if hit.Parent:FindFirstChild("Humanoid") then
            -- Lógica de dano aqui
        end
    end)
    return bala
end

game.Players.PlayerAdded:Connect(function(jogador)
    jogador.CharacterAdded:Connect(function(character)
        marcarJogadores(jogador)
        while wait(1) do
            atualizarDistancia(jogador)
        end
    end)
end)

local bala = criarBala()
bala.Position = game.Players.LocalPlayer.Character.HumanoidRootPart.Position
bala.Parent = workspace
