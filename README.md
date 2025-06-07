--[[ 
Goti Hub V1 - Script Open Source Básico para Blox Fruits
Funções: 
- Auto Farm NPC
- Auto Coleta de Frutas no chão
- Auto Kill Boss
--]]

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

-- Configurações
local autoFarmEnabled = false
local autoCollectFruitEnabled = false
local autoKillBossEnabled = false

-- Lista de NPCs para farm (exemplo, pode mudar para NPCs que quiser)
local farmNPCs = {
    "Bandit",
    "Pirate",
    "Zombie",
}

-- Função para encontrar NPC por nome
local function findNPC(name)
    for _, npc in pairs(workspace.Enemies:GetChildren()) do
        if npc.Name == name and npc:FindFirstChild("Humanoid") and npc.Humanoid.Health > 0 then
            return npc
        end
    end
    return nil
end

-- Função para auto farm
local function autoFarm()
    while autoFarmEnabled do
        for _, npcName in pairs(farmNPCs) do
            local npc = findNPC(npcName)
            if npc then
                -- Teleporta até o NPC
                humanoidRootPart.CFrame = npc.HumanoidRootPart.CFrame + Vector3.new(0, 5, 0)
                wait(0.5)
                
                -- Ataca o NPC (usa o Remote correto do jogo para atacar)
                ReplicatedStorage.Remotes.CombatAttack:InvokeServer()
                wait(1)
            end
        end
        wait(1)
    end
end

-- Função para auto coletar frutas no chão
local function autoCollectFruit()
    while autoCollectFruitEnabled do
        for _, fruit in pairs(workspace:GetChildren()) do
            if fruit.Name == "Fruit" and fruit:IsA("Tool") then
                local dist = (fruit.Handle.Position - humanoidRootPart.Position).Magnitude
                if dist < 30 then
                    -- Teleporta até a fruta
                    humanoidRootPart.CFrame = fruit.Handle.CFrame + Vector3.new(0, 5, 0)
                    wait(0.5)
                    -- Pega a fruta (interação automática)
                    firetouchinterest(humanoidRootPart, fruit.Handle, 0)
                    wait(0.1)
                    firetouchinterest(humanoidRootPart, fruit.Handle, 1)
                    wait(1)
                end
            end
        end
        wait(2)
    end
end

-- Função para auto matar boss (exemplo: "MagmaLord")
local function autoKillBoss()
    while autoKillBossEnabled do
        local boss = findNPC("MagmaLord")
        if boss then
            humanoidRootPart.CFrame = boss.HumanoidRootPart.CFrame + Vector3.new(0, 5, 0)
            wait(0.5)
            ReplicatedStorage.Remotes.CombatAttack:InvokeServer()
            wait(1)
        else
            print("Boss não encontrado.")
            wait(5)
        end
    end
end

-- Ativadores via comandos simples no chat (exemplo)
player.Chatted:Connect(function(msg)
    if msg == "!autofarm" then
        autoFarmEnabled = not autoFarmEnabled
        if autoFarmEnabled then
            print("Auto Farm ativado")
            spawn(autoFarm)
        else
            print("Auto Farm desativado")
        end
    elseif msg == "!autofruit" then
        autoCollectFruitEnabled = not autoCollectFruitEnabled
        if autoCollectFruitEnabled then
            print("Auto Coleta de Frutas ativado")
            spawn(autoCollectFruit)
        else
            print("Auto Coleta de Frutas desativado")
        end
    elseif msg == "!autoboss" then
        autoKillBossEnabled = not autoKillBossEnabled
        if autoKillBossEnabled then
            print("Auto Kill Boss ativado")
            spawn(autoKillBoss)
        else
            print("Auto Kill Boss desativado")
        end
    end
end)

print("Goti Hub V1 carregado! Use comandos: !autofarm, !autofruit, !autoboss")
