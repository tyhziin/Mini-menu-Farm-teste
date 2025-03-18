--// Configurações //--
local menuKey = Enum.KeyCode.K -- Tecla para abrir/fechar o menu
local pegarIdKey = Enum.KeyCode.F1 -- Tecla para "pegar ID"
local menuWidth = 200
local menuHeight = 300
local itemDisplayTime = 5 -- Tempo em segundos que o ID do item fica visível

--// Serviços //--
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage") -- Para eventos remotos, se necessário

--// Variáveis //--
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local isMenuOpen = false
local pegarIdAtivo = false
local itemIdLabel
local itemDisplayTimerConnection
local menuFrame
local pegarIdButton

--// Funções //--

-- Função para criar a janela do menu
local function criarMenu()
    menuFrame = Instance.new("Frame")
    menuFrame.Size = UDim2.new(0, menuWidth, 0, menuHeight)
    menuFrame.Position = UDim2.new(0.5, -menuWidth / 2, 0.5, -menuHeight / 2)
    menuFrame.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
    menuFrame.BorderSizePixel = 0
    menuFrame.Active = true
    menuFrame.Draggable = true
    menuFrame.Visible = false -- Inicialmente invisível
    menuFrame.Parent = player.PlayerGui

    local titleLabel = Instance.new("TextLabel")
    titleLabel.Size = UDim2.new(1, 0, 0, 30)
    titleLabel.Position = UDim2.new(0, 0, 0, 0)
    titleLabel.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
    titleLabel.TextColor3 = Color3.new(1, 1, 1)
    titleLabel.Text = "Menu de Ferramentas"
    titleLabel.Font = Enum.Font.SourceSansBold
    titleLabel.TextScaled = true
    titleLabel.Parent = menuFrame

    pegarIdButton = Instance.new("TextButton")
    pegarIdButton.Size = UDim2.new(0.9, 0, 0, 40)
    pegarIdButton.Position = UDim2.new(0.05, 0, 0, 40)
    pegarIdButton.BackgroundColor3 = Color3.new(0.4, 0.4, 0.4)
    pegarIdButton.TextColor3 = Color3.new(1, 1, 1)
    pegarIdButton.Text = "Pegar ID (F1)"
    pegarIdButton.Font = Enum.Font.SourceSans
    pegarIdButton.TextScaled = true
    pegarIdButton.Parent = menuFrame

    pegarIdButton.MouseButton1Click:Connect(function()
        pegarIdAtivo = not pegarIdAtivo
        if pegarIdAtivo then
            pegarIdButton.Text = "Pegar ID: ATIVO"
            mostrarMensagem("Pegar ID Ativado. Pegue um item.")
        else
            pegarIdButton.Text = "Pegar ID (F1)"
            mostrarMensagem("Pegar ID Desativado.")
        end
    end)

    itemIdLabel = Instance.new("TextLabel")
    itemIdLabel.Size = UDim2.new(1, 0, 0, 30)
    itemIdLabel.Position = UDim2.new(0, 0, 0, 90)
    itemIdLabel.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
    itemIdLabel.TextColor3 = Color3.new(1, 1, 0)
    itemIdLabel.Text = ""
    itemIdLabel.Font = Enum.Font.SourceSans
    itemIdLabel.TextScaled = true
    itemIdLabel.Parent = menuFrame
    itemIdLabel.Visible = false
end


-- Função para mostrar o ID do item
local function mostrarItemId(itemId)
    itemIdLabel.Text = "ID do Item: " .. tostring(itemId)
    itemIdLabel.Visible = true

    if itemDisplayTimerConnection then
        itemDisplayTimerConnection:Disconnect()
    end

    itemDisplayTimerConnection = RunService.Heartbeat:Wait(itemDisplayTime)
    itemIdLabel.Visible = false
end

-- Função para mostrar mensagens na tela
local function mostrarMensagem(mensagem)
    local mensagemLabel = Instance.new("TextLabel")
    mensagemLabel.Size = UDim2.new(0, 200, 0, 30)
    mensagemLabel.Position = UDim2.new(0.5, -100, 0.2, 0)
    mensagemLabel.BackgroundColor3 = Color3.new(0, 0, 0, 0.7)
    mensagemLabel.TextColor3 = Color3.new(1, 1, 1)
    mensagemLabel.Text = mensagem
    mensagemLabel.Font = Enum.Font.SourceSans
    mensagemLabel.TextScaled = true
    mensagemLabel.Parent = player.PlayerGui
    mensagemLabel.ZIndex = 10

    game:GetService("TweenService"):Create(mensagemLabel, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Position = UDim2.new(0.5, -100, 0.1, 0)}):Play()

    wait(3)

    game:GetService("TweenService"):Create(mensagemLabel, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {Position = UDim2.new(0.5, -100, 0, 0)}):Play()

    wait(0.5)
    mensagemLabel:Destroy()
end

--// Conexões de Eventos //--

-- Função para verificar se o player pegou um item
local function onItemEquipped(tool)
    if pegarIdAtivo then
        local itemId = tool.Name -- Use o nome da ferramenta como ID (pode ajustar)
        mostrarItemId(itemId)
    end
end

-- Conectar ao evento de equipar uma ferramenta
player.CharacterAdded:Connect(function(char)
    character = char
    humanoid = char:WaitForChild("Humanoid")

    humanoid.Equipped:Connect(onItemEquipped)
end)

if character and humanoid then
    humanoid.Equipped:Connect(onItemEquipped)
end

-- Evento para abrir/fechar o menu com a tecla
UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if gameProcessedEvent then return end -- Ignora se o jogo já está processando o evento (ex: digitando no chat)

    if input.KeyCode == menuKey then
        isMenuOpen = not isMenuOpen
        menuFrame.Visible = isMenuOpen
        if isMenuOpen then
            mostrarMensagem("Menu ABERTO (K)")
        else
            mostrarMensagem("Menu FECHADO (K)")
        end
    elseif input.KeyCode == pegarIdKey then
        pegarIdAtivo = not pegarIdAtivo
        if pegarIdAtivo then
            pegarIdButton.Text = "Pegar ID: ATIVO"
            mostrarMensagem("Pegar ID Ativado. Pegue um item.")
        else
            pegarIdButton.Text = "Pegar ID (F1)"
            mostrarMensagem("Pegar ID Desativado.")
        end
    end
end)


--// Inicialização //--
criarMenu()
mostrarMensagem("Script Carregado. Pressione K para abrir o menu.")
