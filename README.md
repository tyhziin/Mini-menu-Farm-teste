-- Script para Mini City RP (PC) - Pegar ID

-- Configurações
local delayAberturaMenu = 5 -- Tempo em segundos para abrir o menu após a execução
local larguraMenu = 200
local alturaMenu = 100
local corFundoMenu = { r = 0, g = 0, b = 0, a = 200 } -- Cor preta semi-transparente
local corTextoMenu = { r = 255, g = 255, b = 255, a = 255 } -- Cor branca

-- Variáveis Globais
local menuVisivel = false
local janelaArrastando = false
local janelaX = 100
local janelaY = 100
local itemID = nil

-- Função para desenhar o menu
local function DesenharMenu()
  if menuVisivel then
    GUI.DrawRectangle(janelaX, janelaY, larguraMenu, alturaMenu, corFundoMenu)
    GUI.DrawText("Pegar ID", janelaX + 10, janelaY + 10, corTextoMenu)

    -- Mostrar o ID do item (se disponível)
    if itemID then
      GUI.DrawText("ID: " .. tostring(itemID), janelaX + 10, janelaY + 40, corTextoMenu)
    else
      GUI.DrawText("Segure um item...", janelaX + 10, janelaY + 40, corTextoMenu)
    end

    -- Botão para fechar (opcional)
    --GUI.DrawText("[Fechar]", janelaX + larguraMenu - 60, janelaY + alturaMenu - 20, corTextoMenu)
  end
end

-- Função para verificar se o mouse está sobre o botão (opcional)
--local function MouseSobreBotaoFechar(mouseX, mouseY)
--  return mouseX > janelaX + larguraMenu - 60 and mouseX < janelaX + larguraMenu - 10 and
--         mouseY > janelaY + alturaMenu - 20 and mouseY < janelaY + alturaMenu - 10
--end


-- Função para lidar com cliques do mouse
local function OnMouseClick(mouseX, mouseY, button)
  if menuVisivel then
    -- Lógica de arrastar janela
    if button == 1 and mouseX > janelaX and mouseX < janelaX + larguraMenu and mouseY > janelaY and mouseY < janelaY + 20 then
      janelaArrastando = true
      arrastoOffsetX = mouseX - janelaX
      arrastoOffsetY = mouseY - janelaY
    end

    -- Lógica do botão de fechar (opcional)
    --if MouseSobreBotaoFechar(mouseX, mouseY) then
    --  menuVisivel = false
    --end
  end
end

-- Função para lidar com o movimento do mouse
local function OnMouseMove(mouseX, mouseY)
  if janelaArrastando then
    janelaX = mouseX - arrastoOffsetX
    janelaY = mouseY - arrastoOffsetY
  end
end

-- Função para lidar com o fim do clique do mouse
local function OnMouseRelease(mouseX, mouseY, button)
  if button == 1 then
    janelaArrastando = false
  end
end


-- Função para obter o ID do item na mão (adaptar para Mini City RP)
local function ObterIDItemNaMao()
  -- *** AQUI VOCÊ PRECISARÁ ADAPTAR PARA A API DO MINI CITY RP ***
  -- Exemplo genérico (provavelmente NÃO FUNCIONARÁ sem adaptação):
  -- local item = API.GetItemNaMao(Player.GetLocalPlayer())
  -- if item then
  --   return item.ID
  -- else
  --   return nil
  -- end
  -- Pesquise na documentação do Mini City RP ou pergunte aos desenvolvedores como obter o item que o jogador está segurando.
  -- Este é o ponto crucial da adaptação.
  -- Se não houver uma função direta para obter o ID, pode ser necessário rastrear o inventário e detectar quando o item ativo muda.

  -- Placeholder para testar (substitua com a lógica real do Mini City RP)
  -- ATENÇÃO: ISSO É APENAS PARA TESTE E NÃO FUNCIONARÁ NO JOGO REAL SEM ADAPTAÇÃO
  local itemNaMao = Player.GetCarryingItem() -- Tentativa de obter um item carregado
  if itemNaMao then
    return itemNaMao.ItemID -- Tenta acessar um campo ItemID
  else
    return nil
  end

end


-- Função principal do script
local function Main()
  -- Abre o menu após o delay
  Timer.Create(delayAberturaMenu, false, function()
    menuVisivel = true
  end)

  -- Loop principal (executado a cada frame)
  while true do
    -- Atualiza o ID do item na mão
    itemID = ObterIDItemNaMao()

    -- Desenha o menu
    DesenharMenu()

    -- Aguarda o próximo frame
    coroutine.yield()
  end
end

-- Eventos de mouse
Events.OnMouseClick.Add(OnMouseClick)
Events.OnMouseMove.Add(OnMouseMove)
Events.OnMouseRelease.Add(OnMouseRelease)

-- Inicia o script
Main()
