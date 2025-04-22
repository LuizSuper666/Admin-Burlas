-- Definir as variáveis iniciais
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()

-- Criar a interface (GUI)
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player.PlayerGui

local frame = Instance.new("Frame")
frame.Parent = screenGui
frame.Size = UDim2.new(0, 400, 0, 350)
frame.Position = UDim2.new(0.5, -200, 0.5, -175)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BackgroundTransparency = 0.5
frame.BorderSizePixel = 0
frame.AnchorPoint = Vector2.new(0.5, 0.5)

-- Caixa de texto para digitar o Nick
local nickInput = Instance.new("TextBox")
nickInput.Parent = frame
nickInput.Size = UDim2.new(0, 300, 0, 40)
nickInput.Position = UDim2.new(0.5, -150, 0, 20)
nickInput.PlaceholderText = "Digite o Nick do Jogador"
nickInput.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
nickInput.TextColor3 = Color3.fromRGB(255, 255, 255)
nickInput.BorderRadius = UDim.new(0, 10)

-- Caixa de texto para digitar o Comando
local commandInput = Instance.new("TextBox")
commandInput.Parent = frame
commandInput.Size = UDim2.new(0, 300, 0, 40)
commandInput.Position = UDim2.new(0.5, -150, 0, 80)
commandInput.PlaceholderText = "Digite o Comando"
commandInput.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
commandInput.TextColor3 = Color3.fromRGB(255, 255, 255)
commandInput.BorderRadius = UDim.new(0, 10)

-- Texto para mostrar o último comando usado
local lastCommandText = Instance.new("TextLabel")
lastCommandText.Parent = frame
lastCommandText.Size = UDim2.new(0, 300, 0, 40)
lastCommandText.Position = UDim2.new(0.5, -150, 0, 250)
lastCommandText.Text = "Último Comando: Nenhum"
lastCommandText.TextColor3 = Color3.fromRGB(255, 255, 255)
lastCommandText.BackgroundTransparency = 1

-- Botões de Comando
local buttonAcionar = Instance.new("TextButton")
buttonAcionar.Parent = frame
buttonAcionar.Size = UDim2.new(0, 120, 0, 40)
buttonAcionar.Position = UDim2.new(0.5, -150, 0, 130)
buttonAcionar.Text = "Acionar"
buttonAcionar.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
buttonAcionar.BorderRadius = UDim.new(0, 10)

local buttonRepetir = Instance.new("TextButton")
buttonRepetir.Parent = frame
buttonRepetir.Size = UDim2.new(0, 120, 0, 40)
buttonRepetir.Position = UDim2.new(0.5, 30, 0, 130)
buttonRepetir.Text = "Repetir Infinitamente"
buttonRepetir.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
buttonRepetir.BorderRadius = UDim.new(0, 10)

local buttonDefesa = Instance.new("TextButton")
buttonDefesa.Parent = frame
buttonDefesa.Size = UDim2.new(0, 120, 0, 40)
buttonDefesa.Position = UDim2.new(0.5, -150, 0, 180)
buttonDefesa.Text = "Defesa Absoluta"
buttonDefesa.BackgroundColor3 = Color3.fromRGB(255, 255, 0)
buttonDefesa.BorderRadius = UDim.new(0, 10)

-- Novo Botão para o Comando 'useless'
local buttonUseless = Instance.new("TextButton")
buttonUseless.Parent = frame
buttonUseless.Size = UDim2.new(0, 120, 0, 40)
buttonUseless.Position = UDim2.new(0.5, 30, 0, 180)
buttonUseless.Text = "Useless"
buttonUseless.BackgroundColor3 = Color3.fromRGB(128, 0, 128)
buttonUseless.BorderRadius = UDim.new(0, 10)

-- Variáveis de Controle
local repeating = false
local defesaAtiva = false
local uselessActive = false

-- Função para Acionar Comando
buttonAcionar.MouseButton1Click:Connect(function()
    local playerName = nickInput.Text
    local command = commandInput.Text
    local executorName = player.Name -- Nome do jogador que está executando o comando

    -- Verifica se o nome do jogador existe
    local targetPlayer = game.Players:FindFirstChild(playerName)
    if targetPlayer then
        -- Mostra qual comando foi acionado
        lastCommandText.Text = "Último Comando: " .. command .. " de " .. executorName .. " em " .. targetPlayer.Name
        -- Ação para o comando
        if command:lower() == "explode" then
            -- Explode o jogador
            local explosion = Instance.new("Explosion")
            explosion.Position = targetPlayer.Character.HumanoidRootPart.Position
            explosion.Parent = game.Workspace
        elseif command:lower() == "kick" then
            -- Expulsa o jogador
            targetPlayer:Kick("Você foi expulso pelo administrador!")
        elseif command:lower() == "freeze" then
            -- Congela o jogador
            if targetPlayer.Character then
                targetPlayer.Character.HumanoidRootPart.Anchored = true
            end
        elseif command:lower() == "unfreeze" then
            -- Descongela o jogador
            if targetPlayer.Character then
                targetPlayer.Character.HumanoidRootPart.Anchored = false
            end
        elseif command:lower() == "useless" then
            -- Ativa o comando "useless"
            uselessActive = true
            -- Bloqueia ações do jogador
            if targetPlayer.Character then
                targetPlayer.Character:FindFirstChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.Physics, false) -- Impede ações
                targetPlayer.Character:FindFirstChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.Dead, false) -- Impede morrer
            end
            lastCommandText.Text = targetPlayer.Name .. " foi bloqueado com o comando 'useless'."
        end
    else
        lastCommandText.Text = "Jogador não encontrado!"
    end
end)

-- Função para Repetir Comando Infinitamente
buttonRepetir.MouseButton1Click:Connect(function()
    repeating = not repeating
    if repeating then
        buttonRepetir.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        while repeating do
            -- Execute o comando repetidamente (sem delay)
            local playerName = nickInput.Text
            local command = commandInput.Text
            local targetPlayer = game.Players:FindFirstChild(playerName)
            local executorName = player.Name -- Nome do jogador que está executando o comando
            if targetPlayer then
                -- Mostra qual comando foi acionado
                lastCommandText.Text = "Último Comando: " .. command .. " de " .. executorName .. " em " .. targetPlayer.Name
                -- Ação para o comando
                if command:lower() == "explode" then
                    -- Explode o jogador repetidamente
                    local explosion = Instance.new("Explosion")
                    explosion.Position = targetPlayer.Character.HumanoidRootPart.Position
                    explosion.Parent = game.Workspace
                elseif command:lower() == "kick" then
                    -- Expulsa o jogador repetidamente
                    targetPlayer:Kick("Você foi expulso pelo administrador!")
                elseif command:lower() == "freeze" then
                    -- Congela o jogador repetidamente
                    if targetPlayer.Character then
                        targetPlayer.Character.HumanoidRootPart.Anchored = true
                    end
                elseif command:lower() == "unfreeze" then
                    -- Descongela o jogador repetidamente
                    if targetPlayer.Character then
                        targetPlayer.Character.HumanoidRootPart.Anchored = false
                    end
                elseif command:lower() == "useless" then
                    -- Impede o jogador de executar qualquer comando
                    uselessActive = true
                    -- Bloqueia ações do jogador
                    if targetPlayer.Character then
                        targetPlayer.Character:FindFirstChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.Physics, false)
                    end
                    lastCommandText.Text = targetPlayer.Name .. " foi bloqueado com o comando 'useless'."
                end
            end
            wait(1) -- Espera 1 segundo antes de repetir
        end
    else
        buttonRepetir.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
    end
end)

-- Função para Ativar Defesa Absoluta
buttonDefesa.MouseButton1Click:Connect(function()
    defesaAtiva = not defesaAtiva
    if defesaAtiva then
        buttonDefesa.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        -- Impede ações de outros jogadores em você
        local playerName = nickInput.Text
        if playerName == player.Name then
            -- Comandos de outros jogadores não irão funcionar em você
            -- Exemplo: "explode" ou "kick" não funcionará em você
            lastCommandText.Text = "Defesa Absoluta Ativada!"
        end
    else
        buttonDefesa.BackgroundColor3 = Color3.fromRGB(255, 255, 0)
    end
end)
