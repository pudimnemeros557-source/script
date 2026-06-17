--[[
    Blox Fruits - Auto Farm Simples e Leve
    Compatibilidade: Fluxus, Hydrogen, Delta, Arceus X
]]

-- Configurações Iniciais
local Player = game.Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local VirtualUser = game:GetService("VirtualUser")

-- Função para simular o clique (evita o AFK e ataca)
local function autoClick()
    VirtualUser:CaptureController()
    VirtualUser:ClickButton1(Vector2.new(9e9, 9e9))
end

-- Função para verificar se o NPC é válido para ataque
local function isTargetValid(npc)
    return npc:FindFirstChild("Humanoid") and npc.Humanoid.Health > 0 and npc:FindFirstChild("HumanoidRootPart")
end

-- Loop Principal do Auto Farm
_G.AutoFarm = true -- Variável global para controle do script

task.spawn(function()
    while _G.AutoFarm do
        task.wait(0.1) -- Intervalo para não sobrecarregar o executor

        -- Tenta encontrar o NPC mais próximo
        local closestNpc = nil
        local shortestDistance = math.huge

        for _, npc in pairs(game:GetService("Workspace").Enemies:GetChildren()) do
            if isTargetValid(npc) then
                local distance = (npc.HumanoidRootPart.Position - Character.HumanoidRootPart.Position).Magnitude
                if distance < shortestDistance then
                    shortestDistance = distance
                    closestNpc = npc
                end
            end
        end

        -- Se houver um NPC próximo, executa a ação
        if closestNpc then
            -- 1. Equipar ferramenta (Soco/Espada/Fruta) se não houver nada nas mãos
            if not Character:FindFirstChildOfClass("Tool") then
                local tool = Player.Backpack:FindFirstChildOfClass("Tool")
                if tool then
                    Player.Character.Humanoid:EquipTool(tool)
                end
            end

            -- 2. Teleporte suave e Ataque
            -- Nota: Muitos executores preferem TweenService, mas para scripts leves,
            -- definir o CFrame com um pequeno offset em cima do NPC é o padrão funcional.
            Character.HumanoidRootPart.CFrame = closestNpc.HumanoidRootPart.CFrame * CFrame.new(0, 5, 0)
            
            -- Ataca o NPC
            autoClick()
        end
    end
end)

-- Mensagem de Log no Console do Executor
print("Script Carregado: Auto Farm Blox Fruits Ativado.")
