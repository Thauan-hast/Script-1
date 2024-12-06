-- Script para Race Clicker no Roblox
-- Este script permite configurar a aceleração, multiplicar os ganhos e comprar todos os game passes

local player = game.Players.LocalPlayer
local clickSpeed = 0.1 -- Intervalo entre cliques em segundos
local accelerationMultiplier = 5 -- Multiplicador de aceleração
local gainMultiplier = 2 -- Multiplicador de ganhos
local autoClickEnabled = false -- Controle para auto click
local multipliersEnabled = false -- Controle para multiplicadores

-- Função para simular um clique
local function simulateClick()
    if autoClickEnabled then
        game:GetService("ReplicatedStorage").Events.Click:FireServer()
    end
end

-- Função para aplicar multiplicadores
local function applyMultipliers()
    if multipliersEnabled then
        local stats = player:FindFirstChild("leaderstats")
        if stats then
            local speed = stats:FindFirstChild("Speed")
            local gains = stats:FindFirstChild("Gains")
            if speed and gains then
                speed.Value = speed.Value * accelerationMultiplier
                gains.Value = gains.Value * gainMultiplier
            end
        end
    end
end

-- Função para verificar se o jogador já possui um game pass
local function hasGamePass(player, passId)
    local success, hasPass = pcall(function()
        return game:GetService("MarketplaceService"):UserOwnsGamePassAsync(player.UserId, passId)
    end)
    return success and hasPass
end

-- Função para comprar todos os game passes
local function buyAllGamePasses()
    local gamePasses = {12345678, 23456789, 34567890} -- IDs dos game passes
    for _, passId in ipairs(gamePasses) do
        if not hasGamePass(player, passId) then
            game:GetService("MarketplaceService"):PromptPurchase(player, passId)
        end
    end
end

-- Criar interface gráfica
local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
local frame = Instance.new("Frame", screenGui)
frame.Size = UDim2.new(0, 200, 0, 100)
frame.Position = UDim2.new(0.5, -100, 0.5, -50)
frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)

local autoClickButton = Instance.new("TextButton", frame)
autoClickButton.Size = UDim2.new(0, 180, 0, 40)
autoClickButton.Position = UDim2.new(0, 10, 0, 10)
autoClickButton.Text = "Ativar Auto Click"
autoClickButton.MouseButton1Click:Connect(function()
    autoClickEnabled = not autoClickEnabled
    autoClickButton.Text = autoClickEnabled and "Desativar Auto Click" or "Ativar Auto Click"
end)

local multipliersButton = Instance.new("TextButton", frame)
multipliersButton.Size = UDim2.new(0, 180, 0, 40)
multipliersButton.Position = UDim2.new(0, 10, 0, 50)
multipliersButton.Text = "Ativar Multiplicadores"
multipliersButton.MouseButton1Click:Connect(function()
    multipliersEnabled = not multipliersEnabled
    multipliersButton.Text = multipliersEnabled and "Desativar Multiplicadores" or "Ativar Multiplicadores"
end)

-- Loop para clicar automaticamente e aplicar multiplicadores
while true do
    simulateClick()
    applyMultipliers()
    wait(clickSpeed)
end

-- Comprar todos os game passes ao iniciar o script
buyAllGamePasses()
