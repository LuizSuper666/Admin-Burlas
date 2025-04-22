-- Inicializa variáveis de controle
local repeating = false
local defesaAtiva = false
local uselessMode = false
local targetPlayer = nil  -- Jogador alvo selecionado

-- Função para verificar e pegar os elementos de interface
local function verificarElementosDeInterface()
    print("Verificando elementos de interface...")
    -- Tente encontrar os elementos da interface
    local buttonAcionar = script.Parent:FindFirstChild("ButtonAcionar")
    local buttonRepetir = script.Parent:FindFirstChild("ButtonRepetir")
    local buttonDefesa = script.Parent:FindFirstChild("ButtonDefesa")
    local buttonUseless = script.Parent:FindFirstChild("ButtonUseless")
    local nickInput = script.Parent:FindFirstChild("NickInput")
    local commandInput = script.Parent:FindFirstChild("CommandInput")
    local lastCommandText = script.Parent:FindFirstChild("LastCommandText")

    -- Verificar se os botões e campos existem
    if not buttonAcionar then print("Botão 'ButtonAcionar' não encontrado!") end
    if not buttonRepetir then print("Botão 'ButtonRepetir' não encontrado!") end
    if not buttonDefesa then print("Botão 'ButtonDefesa' não encontrado!") end
    if not buttonUseless then print("Botão 'ButtonUseless' não encontrado!") end
    if not nickInput then print("Campo de input 'NickInput' não encontrado!") end
    if not commandInput then print("Campo de input 'CommandInput' não encontrado!") end
    if not lastCommandText then print("Campo de texto 'LastCommandText' não encontrado!") end

    return buttonAcionar, buttonRepetir, buttonDefesa, buttonUseless, nickInput, commandInput, lastCommandText
end

-- Função para ajustar a interface para celular
local function ajustarUIParaCelular(buttonAcionar, buttonRepetir, buttonDefesa, buttonUseless, nickInput, commandInput)
    if buttonAcionar and buttonRepetir and buttonDefesa and buttonUseless then
        -- Ajuste o tamanho dos botões
        local screenSize = game:GetService("Workspace").CurrentCamera.ViewportSize
        local buttonSize = UDim2.new(0, screenSize.X / 4, 0, 50)  -- Ajuste o tamanho para celular

        -- Ajustar o tamanho dos botões para celulares
        buttonAcionar.Size = buttonSize
        buttonRepetir.Size = buttonSize
        buttonDefesa.Size = buttonSize
        buttonUseless.Size = buttonSize

        -- Ajustar tamanho dos campos de entrada de texto
        if nickInput and commandInput then
            nickInput.Size = UDim2.new(0, screenSize.X - 40, 0, 50)
            commandInput.Size = UDim2.new(0, screenSize.X - 40, 0, 50)
        end
    else
        print("Alguns botões ou campos de entrada não foram encontrados!")
    end
end

-- Função para lidar com o clique do botão Acionar
local function acionarComando(buttonAcionar, nickInput, commandInput, lastCommandText)
    if buttonAcionar then
        buttonAcionar.MouseButton1Click:Connect(function()
            local playerName = nickInput.Text
            local command = commandInput.Text

            -- Verificar se o comando e jogador são válidos
            local selectedPlayer = game.Players:FindFirstChild(playerName)
            if selectedPlayer then
                -- Mostrar qual comando foi acionado
                lastCommandText.Text = "Último Comando: " .. command .. " em " .. selectedPlayer.Name
                -- Lógica do comando
                if command:lower() == "explode" then
                    local explosion = Instance.new("Explosion")
                    explosion.Position = selectedPlayer.Character.HumanoidRootPart.Position
                    explosion.Parent = game.Workspace
                elseif command:lower() == "kick" then
                    selectedPlayer:Kick("Você foi expulso pelo administrador!")
                elseif command:lower() == "freeze" then
                    selectedPlayer.Character.HumanoidRootPart.Anchored = true
                elseif command:lower() == "unfreeze" then
                    selectedPlayer.Character.HumanoidRootPart.Anchored = false
                end
            else
                lastCommandText.Text = "Jogador não encontrado!"
            end
        end)
    else
        print("Botão 'ButtonAcionar' não encontrado para associar evento.")
    end
end

-- Função para lidar com o botão Repetir
local function repetirAcao(buttonRepetir)
    if buttonRepetir then
        buttonRepetir.MouseButton1Click:Connect(function()
            repeating = not repeating
            if repeating then
                print("Ação repetida ativada.")
            else
                print("Ação repetida desativada.")
            end
        end)
    else
        print("Botão 'ButtonRepetir' não encontrado para associar evento.")
    end
end

-- Função para lidar com a ativação da defesa
local function ativarDefesa(buttonDefesa)
    if buttonDefesa then
        buttonDefesa.MouseButton1Click:Connect(function()
            defesaAtiva = not defesaAtiva
            if defesaAtiva then
                print("Defesa ativada.")
            else
                print("Defesa desativada.")
            end
        end)
    else
        print("Botão 'ButtonDefesa' não encontrado para associar evento.")
    end
end

-- Função para lidar com o modo inutilizado
local function ativarModoUseless(buttonUseless)
    if buttonUseless then
        buttonUseless.MouseButton1Click:Connect(function()
            uselessMode = not uselessMode
            if uselessMode then
                print("Modo inutilizado ativado.")
            else
                print("Modo inutilizado desativado.")
            end
        end)
    else
        print("Botão 'ButtonUseless' não encontrado para associar evento.")
    end
end

-- Verificar e pegar os elementos da interface
local buttonAcionar, buttonRepetir, buttonDefesa, buttonUseless, nickInput, commandInput, lastCommandText = verificarElementosDeInterface()

-- Ajustar a interface para dispositivos móveis
ajustarUIParaCelular(buttonAcionar, buttonRepetir, buttonDefesa, buttonUseless, nickInput, commandInput)

-- Lidar com o comando de acionar
acionarComando(buttonAcionar, nickInput, commandInput, lastCommandText)

-- Lidar com o botão de repetir
repetirAcao(buttonRepetir)

-- Lidar com o botão de defesa
ativarDefesa(buttonDefesa)

-- Lidar com o botão de inutilizar
ativarModoUseless(buttonUseless)
