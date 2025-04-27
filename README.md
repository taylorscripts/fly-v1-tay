-- Script de voo com GUI para Roblox (Brookhaven RP)

local userInputService = game:GetService("UserInputService")
local players = game:GetService("Players")
local localPlayer = players.LocalPlayer
local character = localPlayer.Character or localPlayer.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local flying = false
local speed = 50 -- Velocidade do voo
local flyHeight = 20 -- Altura do voo

-- Variáveis para controlar o voo
local bodyVelocity
local bodyGyro

-- Função para ativar o voo
local function startFly()
    if not flying then
        flying = true
        -- Adiciona um BodyVelocity para controlar o movimento
        bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = Vector3.new(100000, 100000, 100000)
        bodyVelocity.Velocity = Vector3.new(0, speed, 0)
        bodyVelocity.Parent = character.HumanoidRootPart
        
        -- Adiciona um BodyGyro para estabilizar o personagem no ar
        bodyGyro = Instance.new("BodyGyro")
        bodyGyro.MaxTorque = Vector3.new(100000, 100000, 100000)
        bodyGyro.CFrame = character.HumanoidRootPart.CFrame
        bodyGyro.Parent = character.HumanoidRootPart

        -- Ajustar a altura do voo
        character.HumanoidRootPart.CFrame = character.HumanoidRootPart.CFrame + Vector3.new(0, flyHeight, 0)
        
        -- Atualiza a GUI para Fly On (verde)
        flyButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0) -- Cor verde
        flyButton.Text = "Fly On"
    end
end

-- Função para desativar o voo
local function stopFly()
    if flying then
        flying = false
        if bodyVelocity then
            bodyVelocity:Destroy()
        end
        if bodyGyro then
            bodyGyro:Destroy()
        end
        
        -- Atualiza a GUI para Fly Off (vermelho)
        flyButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0) -- Cor vermelha
        flyButton.Text = "Fly Off"
    end
end

-- Controle de voo com a tecla "F"
userInputService.InputBegan:Connect(function(input, gameProcessed)
    if input.KeyCode == Enum.KeyCode.F and not gameProcessed then
        if flying then
            stopFly()
        else
            startFly()
        end
    end
end)

-- Atualizando a direção durante o voo (manter o voo controlado)
game:GetService("RunService").Heartbeat:Connect(function()
    if flying then
        -- Movimentação básica para controlar o voo
        local moveDirection = Vector3.new(0, 0, 0)
        if userInputService:IsKeyDown(Enum.KeyCode.W) then
            moveDirection = moveDirection + character.HumanoidRootPart.CFrame.LookVector
        end
        if userInputService:IsKeyDown(Enum.KeyCode.S) then
            moveDirection = moveDirection - character.HumanoidRootPart.CFrame.LookVector
        end
        if userInputService:IsKeyDown(Enum.KeyCode.A) then
            moveDirection = moveDirection - character.HumanoidRootPart.CFrame.RightVector
        end
        if userInputService:IsKeyDown(Enum.KeyCode.D) then
            moveDirection = moveDirection + character.HumanoidRootPart.CFrame.RightVector
        end
        if userInputService:IsKeyDown(Enum.KeyCode.Space) then
            moveDirection = moveDirection + Vector3.new(0, 1, 0)
        end
        if userInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
            moveDirection = moveDirection - Vector3.new(0, 1, 0)
        end
        
        bodyVelocity.Velocity = moveDirection * speed
    end
end)

-- Criando a GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.CoreGui
ScreenGui.Name = "FlyGui"

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 200, 0, 100)
frame.Position = UDim2.new(0, 10, 0, 10)
frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
frame.Parent = ScreenGui

local flyButton = Instance.new("TextButton")
flyButton.Size = UDim2.new(1, 0, 1, 0)
flyButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0) -- Cor inicial vermelha
flyButton.Text = "Fly Off"
flyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
flyButton.Parent = frame

-- Função para alternar o voo ao clicar no botão
flyButton.MouseButton1Click:Connect(function()
    if flying then
        stopFly()
    else
        startFly()
    end
end)

-- Notificação de script iniciado
game:GetService("StarterGui"):SetCore("SendNotification", {
    Title = "Fly Script Ativado",
    Text = "Pressione F ou clique no botão para ativar/desativar o voo.",
    Duration = 5
})
