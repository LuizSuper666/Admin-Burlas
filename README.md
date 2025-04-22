-- Variáveis de Controle
local repeating = false
local defesaAtiva = false
local uselessMode = false
local targetPlayer = nil  -- Jogador alvo selecionado

-- Função para ajustar a interface para celular (tamanho da tela e interação por toque)
local function ajustarUIParaCelular()
    local screenSize = game:GetService("Workspace").CurrentCamera.ViewportSize
    local buttonSize = UDim2.new(0, screenSize.X / 4, 0, 50) -- Ajuste o tamanho dos botões para celular

    -- Ajuste os botões para tamanhos mais apropriados para celular
    buttonAcionar.Size = buttonSize
    buttonRepetir.Size = buttonSize
    buttonDefesa.Size = buttonSize
    buttonUseless.Size = buttonSize

    -- Ajuste o tamanho dos campos de entrada de texto
    nickInput.Size = UDim2.new(0, screenSize.X - 40, 0, 50)
    commandInput.Size = UDim2.new(0, screenSize.X - 40, 0, 50)
end

-- Chama a função para ajustar a UI ao iniciar
ajustarUIParaCelular()

-- Função para Acionar Comando
buttonAcionar.MouseButton1Click:Connect(function()
    local playerName = nickInput.Text
    local command = commandInput.Text
    local executorName = player.Name -- Nome do jogador que está executando o comando

    -- Verifica se o modo "Useless" está ativado na pessoa escolhida
    if uselessMode and targetPlayer and targetPlayer.Name == playerName then
        lastCommandText.Text = "O jogador está no modo Useless! Comandos desativados para ele."
        return
    end

    -- Verifica se o nome do jogador existe
    local selectedPlayer = game.Players:FindFirstChild(playerName)
    if selectedPlayer then
        -- Mostra qual comando foi acionado
        lastCommandText.Text = "Último Comando: " .. command .. " de " .. executorName .. " em " .. selectedPlayer.Name
        -- Ação para o comando
        if command:lower() == "explode" then
            -- Explode o jogador
            local explosion = Instance.new("Explosion")
            explosion.Position = selectedPlayer.Character.HumanoidRootPart.Position
            explosion.Parent = game.Workspace
        elseif command:lower() == "kick" then
            -- Expulsa o jogador
            selectedPlayer:Kick("Você foi expulso pelo administrador!")
        elseif command:lower() == "freeze" then
            -- Congela o jogador
            if selectedPlayer.Character then
                selectedPlayer.Character.HumanoidRootPart.Anchored = true
            end
        elseif command:lower() == "unfreeze" then
            -- Descongela o jogador
            if selectedPlayer.Character then
                selectedPlayer.Character.HumanoidRootPart.Anchored = false
            end
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
            -- Verifica se o modo "Useless" está ativado na pessoa escolhida
            if uselessMode and targetPlayer then
                lastCommandText.Text = "O jogador está no modo Useless! Comandos desativados para ele."
                break
            end
            
            -- Execute o comando repetidamente (sem delay)
            local playerName = nickInput.Text
            local command = commandInput.Text
            local selectedPlayer = game.Players:FindFirstChild(playerName)
            local executorName = player.Name -- Nome do jogador que está executando o comando
            if selectedPlayer then
                -- Mostra qual comando foi acionado
                lastCommandText.Text = "Último Comando: " .. command .. " de " .. executorName .. " em " .. selectedPlayer.Name
                -- Ação para o comando
                if command:lower() == "explode" then
                    -- Explode o jogador repetidamente
                    local explosion = Instance.new("Explosion")
                    explosion.Position = selectedPlayer.Character.HumanoidRootPart.Position
                    explosion.Parent = game.Workspace
                elseif command:lower() == "kick" then
                    -- Expulsa o jogador repetidamente
                    selectedPlayer:Kick("Você foi expulso pelo administrador!")
                elseif command:lower() == "freeze" then
                    -- Congela o jogador repetidamente
                    if selectedPlayer.Character then
                        selectedPlayer.Character.HumanoidRootPart.Anchored = true
                    end
                elseif command:lower() == "unfreeze" then
                    -- Descongela o jogador repetidamente
                    if selectedPlayer.Character then
                        selectedPlayer.Character.HumanoidRootPart.Anchored = false
                    end
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
        lastCommandText.Text = "Defesa Absoluta Desativada!"
    end
end)

-- Função para Ativar o Modo Useless
buttonUseless.MouseButton1Click:Connect(function()
    uselessMode = not uselessMode
    local playerName = nickInput.Text
    targetPlayer = game.Players:FindFirstChild(playerName)

    if targetPlayer then
        if uselessMode then
            -- Desativa as interações do jogador
            targetPlayer.Character.HumanoidRootPart.Anchored = true
            targetPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = 0
            targetPlayer.Character:FindFirstChildOfClass("Humanoid").JumpHeight = 0

            -- Impede o jogador de pegar itens
            targetPlayer.Character:FindFirstChildOfClass("Humanoid").CanPickupItems = false

            -- Impede a morte ou reinício
            targetPlayer.Character:FindFirstChildOfClass("Humanoid").Died:Connect(function()
                -- Reage à morte de forma que não permita a reinicialização ou mortes
                targetPlayer.Character:BreakJoints()
            end)

            lastCommandText.Text = playerName .. " está no modo Useless. Ele não pode usar comandos."
        else
            -- Restaura as interações do jogador
            targetPlayer.Character.HumanoidRootPart.Anchored = false
            targetPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = 16
            targetPlayer.Character:FindFirstChildOfClass("Humanoid").JumpHeight = 50

            -- Restaura a capacidade de pegar itens
            targetPlayer.Character:FindFirstChildOfClass("Humanoid").CanPickupItems = true

            lastCommandText.Text = playerName .. " está livre para jogar novamente."
        end
    else
        lastCommandText.Text = "Jogador não encontrado!"
    end
end)
