-- Configurações
local corDeFundo = Color3.new(0, 0, 0)
local tamanhoQuadrado = UDim2.new(0.2, 0, 0.3, 0)
local distanciaJogador = 5
local tempoAtualizacao = 0.5
local limiteItens = 3 -- Limite de itens equipados a exibir

--[[
IMPORTANTE: Este script DEVE ser executado em um Script NORMAL dentro do ServerScriptService.
Um Script LOCAL (dentro do PlayerGui) NÃO funciona para acessar dados de outros jogadores.

Este script combina:
1.  Detecção de itens equipados via "Inventory" (com BoolValue "Equipped")
2.  Detecção de roupas (Hat/Shirt/Pants/Accessory) usando Tags nos acessórios
3.  Uso de RemoteEvent para identificar o LocalPlayer corretamente.

O script assume que:
- Itens de inventário "equipados" estão na pasta "Inventory" com BoolValue "Equipped" = true.
- Roupas (chapéus, camisas, calças) usam Tags (ShirtTag, PantsTag, HatTag, AccessoryTag) dentro da Handle do acessório.

]]

--[[
FUNÇÕES AUXILIARES
]]

local function criarLabel(layout, texto)
    local label = Instance.new("TextLabel")
    label.Text = texto
    label.TextColor3 = Color3.new(1, 1, 1)
    label.BackgroundTransparency = 1
    label.Size = UDim2.new(1, 0, 0, 20)
    label.Font = Enum.Font.SourceSansPro
    label.TextScaled = true
    label.Parent = layout.Parent
end

local function verificarDistancia(jogadorLocal, jogadorAlvo)
    if not jogadorLocal or not jogadorLocal.Character or not jogadorAlvo or not jogadorAlvo.Character then
        return false
    end

    local posicaoLocal = jogadorLocal.Character:GetPivot().Position
    local posicaoAlvo = jogadorAlvo.Character:GetPivot().Position
    local distancia = (posicaoLocal - posicaoAlvo).Magnitude

    return distancia <= distanciaJogador
end

--[[
CRIAÇÃO DA INTERFACE
]]

local function criarInterface(jogador)
    local quadro = Instance.new("BillboardGui")
    quadro.Name = "ItensEquipadosGUI"
    quadro.Extents = Vector3.new(5, 5, 5)
    quadro.AlwaysOnTop = true
    quadro.StudsOffset = Vector3.new(0, 3, 0)
    quadro.Enabled = false

    local frame = Instance.new("Frame")
    frame.Size = tamanhoQuadrado
    frame.BackgroundTransparency = 0.5
    frame.BackgroundColor3 = corDeFundo
    frame.BorderSizePixel = 0
    frame.Parent = quadro

    local layout = Instance.new("UIListLayout")
    layout.Parent = frame
    layout.FillDirection = Enum.FillDirection.Vertical
    layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    layout.VerticalAlignment = Enum.VerticalAlignment.Top
    layout.Padding = UDim.new(0, 5)

    quadro.Parent = jogador.Character:WaitForChild("Head")

    return quadro, frame, layout
end

--[[
ATUALIZAÇÃO DA INTERFACE
]]

local function atualizarInterface(jogador, quadro, layout)
    -- Limpar labels existentes
    for _, child in ipairs(layout.Parent:GetChildren()) do
        if child:IsA("TextLabel") then
            child:Destroy()
        end
    end

    local itensExibidos = 0

    -- 1. ITENS DO INVENTÁRIO:
    local inventory = jogador:FindFirstChild("Inventory")
    if inventory then
        for _, item in ipairs(inventory:GetChildren()) do
            if itensExibidos >= limiteItens then break end -- Limite

            if item:IsA("BasePart") or item:IsA("Model") then
                local equipado = item:FindFirstChild("Equipped")
                if equipado and equipado:IsA("BoolValue") and equipado.Value == true then
                    criarLabel(layout, item.Name)
                    itensExibidos = itensExibidos + 1
                end
            end
        end
    end

    -- 2. ROUPAS (TAGS NOS ACESSÓRIOS):
    for _, acessorio in ipairs(jogador.Character:GetChildren()) do
        if itensExibidos >= limiteItens then break end -- Limite

        if acessorio:IsA("Accessory") then
            if acessorio.Handle:FindFirstChild("ShirtTag") then
                criarLabel(layout, acessorio.Name)
                itensExibidos = itensExibidos + 1
            elseif acessorio.Handle:FindFirstChild("PantsTag") then
                criarLabel(layout, acessorio.Name)
                itensExibidos = itensExibidos + 1
            elseif acessorio.Handle:FindFirstChild("HatTag") then
                criarLabel(layout, acessorio.Name)
                itensExibidos = itensExibidos + 1
            elseif acessorio.Handle:FindFirstChild("AccessoryTag") then
                criarLabel(layout, acessorio.Name)
                itensExibidos = itensExibidos + 1
            end
        elseif acessorio:IsA("Shirt") or acessorio:IsA("Pants") then
            criarLabel(layout, acessorio.Name)
            itensExibidos = itensExibidos + 1
        end
    end
end

--[[
GERENCIAMENTO DOS JOGADORES (ENTRADA/SAÍDA) E LOOP PRINCIPAL
]]

local LocalPlayers = {} -- Tabela para armazenar quais jogadores são LocalPlayers (por cliente)
local interfaces = {}    -- Tabela para armazenar as interfaces dos jogadores

-- RemoteEvent para setar o LocalPlayer (lado servidor)
game.ReplicatedStorage.SetLocalPlayer.OnServerEvent:Connect(function(player)
    LocalPlayers[player] = true -- Marca este jogador como LocalPlayer
end)

local function atualizarInterfaces()
    for _, jogadorAlvo in ipairs(game.Players:GetPlayers()) do
        if LocalPlayers[jogadorAlvo] == true then -- É LocalPlayer? NÃO MOSTRAR
            if interfaces[jogadorAlvo] then
                local quadro, _, _ = unpack(interfaces[jogadorAlvo])
                quadro.Enabled = false
            end
        else -- NÃO é LocalPlayer? MOSTRAR (se estiver perto)
            if not interfaces[jogadorAlvo] then
                interfaces[jogadorAlvo] = criarInterface(jogadorAlvo)
            end

            local quadro, frame, layout = unpack(interfaces[jogadorAlvo])

            -- Encontrar o LocalPlayer mais próximo (complexo, pois pode ter vários)
            local jogadorLocal = nil
            local menorDistancia = math.huge

            for player, _ in pairs(LocalPlayers) do --Itera sobre os jogadores locais.
                if player and player.Character and jogadorAlvo and jogadorAlvo.Character then
                    local distancia = (player.Character:GetPivot().Position - jogadorAlvo.Character:GetPivot().Position).Magnitude
                    if distancia < menorDistancia then
                        menorDistancia = distancia
                        jogadorLocal = player
                    end
                end
            end

            if jogadorLocal and verificarDistancia(jogadorLocal, jogadorAlvo) then
                atualizarInterface(jogadorAlvo, quadro, layout)
                quadro.Enabled = true
            else
                quadro.Enabled = false
            end
        end
    end
end

local function jogadorEntrou(jogador)
    interfaces[jogador] = criarInterface(jogador)

    jogador.CharacterAppearanceLoaded:Connect(function()
        task.wait(1)
        atualizarInterfaces()
    end)

    -- Criar a pasta "Inventory" se não existir
    if not jogador:FindFirstChild("Inventory") then
        local inventory = Instance.new("Folder")
        inventory.Name = "Inventory"
        inventory.Parent = jogador
    end
end

local function jogadorSaiu(jogador)
    if interfaces[jogador] then
        local quadro, _, _ = unpack(interfaces[jogador])
        quadro:Destroy()
        interfaces[jogador] = nil
    end
    LocalPlayers[jogador] = nil -- Remover o jogador da tabela de LocalPlayers
end

local function inicializar()
    -- Conectar eventos de entrada/saída de jogadores
    game.Players.PlayerAdded:Connect(jogadorEntrou)
    game.Players.PlayerRemoving:Connect(jogadorSaiu)

    -- Inicializar interfaces para jogadores existentes
    for _, jogador in ipairs(game.Players:GetPlayers()) do
        jogadorEntrou(jogador)
    end

    -- Loop principal de atualização
    while true do
        task.wait(tempoAtualizacao)
        atualizarInterfaces()
    end
end

--[[
INÍCIO DO SCRIPT
]]

inicializar()
