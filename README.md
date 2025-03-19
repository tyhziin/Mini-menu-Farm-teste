PRIMEIRO SCRIPT 

-- Script para mostrar o inventário equipado de jogadores em Mini City RP

-- Configurações
local corDoRetangulo = Color3.fromRGB(0, 128, 255) -- Cor do retângulo (azul)
local transparenciaDoRetangulo = 0.5 -- Transparência do retângulo (0 a 1)
local corDoTexto = Color3.White -- Cor do texto
local tamanhoDoTexto = 16 -- Tamanho do texto
local tempoDeAtualizacao = 1 -- Tempo (em segundos) entre as atualizações do inventário

-- Funções auxiliares
local function criarRetangulo(jogador)
    local gui = Instance.new("ScreenGui")
    gui.Name = "InventarioGUI"
    gui.Parent = jogador.PlayerGui

    local frame = Instance.new("Frame")
    frame.Name = "InventarioFrame"
    frame.Size = UDim2.new(0, 200, 0, 150) -- Tamanho do retângulo
    frame.Position = UDim2.new(0.5, -100, 0.1, 0) -- Posição centralizada no topo da tela
    frame.BackgroundColor3 = corDoRetangulo
    frame.BackgroundTransparency = transparenciaDoRetangulo
    frame.BorderSizePixel = 0
    frame.Parent = gui

    local texto = Instance.new("TextLabel")
    texto.Name = "InventarioTexto"
    texto.Size = UDim2.new(1, 0, 1, 0)
    texto.Position = UDim2.new(0, 0, 0, 0)
    texto.BackgroundColor3 = Color3.new(1, 1, 1)
    texto.BackgroundTransparency = 1
    texto.TextColor3 = corDoTexto
    texto.TextSize = tamanhoDoTexto
    texto.Font = Enum.Font.SourceSansBold
    texto.TextXAlignment = Enum.TextXAlignment.Left
    texto.TextYAlignment = Enum.TextYAlignment.Top
    texto.Text = "" -- O texto será atualizado posteriormente
    texto.Parent = frame

    return gui, frame, texto
end


local function atualizarInventario(jogador, texto)
    -- Aqui você deve obter os itens equipados do jogador.  A LÓGICA DE COMO FAZER ISSO DEPENDE TOTALMENTE DO SEU JOGO.
    --  **IMPORTANTE**:  Substitua esta seção com o código específico do seu jogo para acessar o inventário.

    -- **EXEMPLO GENÉRICO (ADAPTE PARA O SEU JOGO)**
    local inventarioEquipado = {}

    -- ***SUBSTITUA TODO ESTE BLOCO COM A LÓGICA DO SEU JOGO***
    if jogador:FindFirstChild("Equipamentos") then
      local equipamentos = jogador:FindFirstChild("Equipamentos"):GetChildren()
      for _, item in ipairs(equipamentos) do
        if item:IsA("StringValue") then  -- ou qualquer outro tipo de objeto que represente um item
          table.insert(inventarioEquipado, item.Value)
        end
      end
    end
    -- ***FIM DO BLOCO QUE VOCÊ PRECISA SUBSTITUIR***


    -- Formatar o texto do inventário
    local textoInventario = ""
    for i, item in ipairs(inventarioEquipado) do
        textoInventario = textoInventario .. item .. "\n"
    end

    -- Atualizar o texto na tela
    texto.Text = textoInventario
end


local function inicializarJogador(jogador)
    local gui, frame, texto = criarRetangulo(jogador)
    atualizarInventario(jogador, texto) -- Atualizar o inventário imediatamente

    -- Loop para atualizar o inventário periodicamente
    while wait(tempoDeAtualizacao) do
        if jogador and jogador.Parent then  -- Verificar se o jogador ainda está no jogo
            if gui and gui.Parent then --Verifica se a gui ainda existe
                atualizarInventario(jogador, texto)
            else
                break --Sai do loop se a GUI não existe mais
            end
        else
            break -- Sair do loop se o jogador não está mais no jogo
        end
    end

    -- Limpeza (opcional, mas recomendado)
    if gui then
        gui:Destroy()
    end
end



-- Evento para quando um jogador entra no jogo
game.Players.PlayerAdded:Connect(function(jogador)
    inicializarJogador(jogador)
end)

-- Lógica para jogadores que já estão no jogo quando o script é executado
for _, jogador in ipairs(game.Players:GetPlayers()) do
    inicializarJogador(jogador)
end




SEGUNDO SCRIPT 

-- Script para mostrar o inventário equipado de jogadores em Mini City RP

-- Configurações
local corDoRetangulo = Color3.fromRGB(0, 128, 255) -- Cor do retângulo (azul)
local transparenciaDoRetangulo = 0.5 -- Transparência do retângulo (0 a 1)
local corDoTexto = Color3.White -- Cor do texto
local tamanhoDoTexto = 16 -- Tamanho do texto
local tempoDeAtualizacao = 1 -- Tempo (em segundos) entre as atualizações do inventário

-- Funções auxiliares
local function criarRetangulo(jogador)
    local gui = Instance.new("ScreenGui")
    gui.Name = "InventarioGUI"
    gui.Parent = jogador.PlayerGui

    local frame = Instance.new("Frame")
    frame.Name = "InventarioFrame"
    frame.Size = UDim2.new(0, 200, 0, 150) -- Tamanho do retângulo
    frame.Position = UDim2.new(0.5, -100, 0.1, 0) -- Posição centralizada no topo da tela
    frame.BackgroundColor3 = corDoRetangulo
    frame.BackgroundTransparency = transparenciaDoRetangulo
    frame.BorderSizePixel = 0
    frame.Parent = gui

    local texto = Instance.new("TextLabel")
    texto.Name = "InventarioTexto"
    texto.Size = UDim2.new(1, 0, 1, 0)
    texto.Position = UDim2.new(0, 0, 0, 0)
    texto.BackgroundColor3 = Color3.new(1, 1, 1)
    texto.BackgroundTransparency = 1
    texto.TextColor3 = corDoTexto
    texto.TextSize = tamanhoDoTexto
    texto.Font = Enum.Font.SourceSansBold
    texto.TextXAlignment = Enum.TextXAlignment.Left
    texto.TextYAlignment = Enum.TextYAlignment.Top
    texto.Text = "" -- O texto será atualizado posteriormente
    texto.Parent = frame

    return gui, frame, texto
end


local function atualizarInventario(jogador, texto)
    -- Aqui você deve obter os itens equipados do jogador.  A LÓGICA DE COMO FAZER ISSO DEPENDE TOTALMENTE DO SEU JOGO.
    --  **IMPORTANTE**:  Substitua esta seção com o código específico do seu jogo para acessar o inventário.

    -- **EXEMPLO GENÉRICO (ADAPTE PARA O SEU JOGO)**
    local inventarioEquipado = {}

    -- ***SUBSTITUA TODO ESTE BLOCO COM A LÓGICA DO SEU JOGO***
    if jogador:FindFirstChild("Equipamentos") then
      local equipamentos = jogador:FindFirstChild("Equipamentos"):GetChildren()
      for _, item in ipairs(equipamentos) do
        if item:IsA("StringValue") then  -- ou qualquer outro tipo de objeto que represente um item
          table.insert(inventarioEquipado, item.Value)
        end
      end
    end
    -- ***FIM DO BLOCO QUE VOCÊ PRECISA SUBSTITUIR***


    -- Formatar o texto do inventário
    local textoInventario = ""
    for i, item in ipairs(inventarioEquipado) do
        textoInventario = textoInventario .. item .. "\n"
    end

    -- Atualizar o texto na tela
    texto.Text = textoInventario
end


local function inicializarJogador(jogador)
    local gui, frame, texto = criarRetangulo(jogador)
    atualizarInventario(jogador, texto) -- Atualizar o inventário imediatamente

    -- Loop para atualizar o inventário periodicamente
    while wait(tempoDeAtualizacao) do
        if jogador and jogador.Parent then  -- Verificar se o jogador ainda está no jogo
            if gui and gui.Parent then --Verifica se a gui ainda existe
                atualizarInventario(jogador, texto)
            else
                break --Sai do loop se a GUI não existe mais
            end
        else
            break -- Sair do loop se o jogador não está mais no jogo
        end
    end

    -- Limpeza (opcional, mas recomendado)
    if gui then
        gui:Destroy()
    end
end



-- Evento para quando um jogador entra no jogo
game.Players.PlayerAdded:Connect(function(jogador)
    inicializarJogador(jogador)
end)

-- Lógica para jogadores que já estão no jogo quando o script é executado
for _, jogador in ipairs(game.Players:GetPlayers()) do
    inicializarJogador(jogador)
end


-- **EXPLICAÇÃO DO CÓDIGO:**

-- 1. **Configurações:** Define as cores, transparência, tamanho do texto e o tempo de atualização.  Ajuste esses valores conforme necessário.

-- 2. **`criarRetangulo(jogador)`:**
--   - Cria um `ScreenGui` para o jogador.
--   - Cria um `Frame` (o retângulo) dentro do `ScreenGui`.
--   - Cria um `TextLabel` dentro do `Frame` para exibir os itens.
--   - Define as propriedades visuais (cor, tamanho, posição, etc.).

-- 3. **`atualizarInventario(jogador, texto)`:**
--   - **IMPORTANTE:** Esta é a função que você precisa modificar para funcionar com o seu jogo.  Atualmente, ela tem um exemplo genérico que **NÃO VAI FUNCIONAR** sem modificação.
--   - **VOCÊ PRECISA SUBSTITUIR O BLOCO COMENTADO COM `***SUBSTITUA TODO ESTE BLOCO...`** pela lógica correta para acessar os itens equipados do jogador no seu jogo.  Isso pode envolver acessar objetos no `Character`, acessar um serviço de inventário, ou qualquer outro método específico do seu jogo.
--   - Formata os itens em uma string para exibição no `TextLabel`.

-- 4. **`inicializarJogador(jogador)`:**
--   - Chama `criarRetangulo` para criar a interface para o jogador.
--   - Chama `atualizarInventario` para exibir o inventário inicial.
--   - Inicia um loop `while` que:
--     - Espera `tempoDeAtualizacao` segundos.
--     - Chama `atualizarInventario` para atualizar a exibição do inventário.
--     - Verifica se o jogador ainda está no jogo e se a GUI ainda existe antes de continuar. Isso evita erros.
--   - Destrói a GUI quando o loop termina (geralmente quando o jogador sai). Isso é importante para evitar vazamentos de memória.

-- 5. **Eventos `PlayerAdded` e Inicialização Inicial:**
--   - O evento `game.Players.PlayerAdded` garante que a interface seja criada para novos jogadores que entram no jogo.
--   - O loop `for _, jogador in ipairs(game.Players:GetPlayers()) do` inicializa a interface para jogadores que já estão no jogo quando o script é iniciado
