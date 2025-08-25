-- LocalScript (StarterGui)

local player = game.Players.LocalPlayer

-- Variáveis do chão
local velocidadeChao = 10
local alturaMaxima = 500
local alturaMinima = -100
local chao = nil
local subindo = false
local descendo = false

-- Função para criar GUI
local function criarGUI()
    -- Remove GUI antiga se existir
    local screenGui = player:WaitForChild("PlayerGui"):FindFirstChild("ChaoGUI")
    if screenGui then screenGui:Destroy() end

    screenGui = Instance.new("ScreenGui")
    screenGui.Name = "ChaoGUI"
    screenGui.Parent = player:WaitForChild("PlayerGui")

    -- Botão redondo
    local botaoRedondo = Instance.new("TextButton")
    botaoRedondo.Size = UDim2.new(0, 60, 0, 60)
    botaoRedondo.Position = UDim2.new(0.1, 0, 0.8, 0)
    botaoRedondo.BackgroundColor3 = Color3.fromRGB(255,0,0)
    botaoRedondo.Text = ""
    botaoRedondo.BorderSizePixel = 0
    botaoRedondo.Parent = screenGui

    local uicorner = Instance.new("UICorner")
    uicorner.CornerRadius = UDim.new(0.5,0)
    uicorner.Parent = botaoRedondo

    botaoRedondo.Active = true
    botaoRedondo.Draggable = true

    -- Frame dos botões do chão (arrastável)
    local frameBotoes = Instance.new("Frame")
    frameBotoes.Size = UDim2.new(0, 200, 0, 130)
    frameBotoes.Position = UDim2.new(0.5, -100, 0.75, 0)
    frameBotoes.BackgroundTransparency = 1
    frameBotoes.Active = true
    frameBotoes.Draggable = true
    frameBotoes.Visible = false
    frameBotoes.Parent = screenGui

    local function criarBotao(nome, texto, pos, corFundo, corTexto)
        local botao = Instance.new("TextButton")
        botao.Name = nome
        botao.Size = UDim2.new(1,0,0.4,0)
        botao.Position = pos
        botao.Text = texto
        botao.BackgroundColor3 = corFundo or Color3.fromRGB(255,100,100)
        botao.TextColor3 = corTexto or Color3.fromRGB(255,255,255)
        botao.Font = Enum.Font.FredokaOne
        botao.TextScaled = true
        botao.BorderSizePixel = 0
        botao.Parent = frameBotoes
        return botao
    end

    local botaoToggle = criarBotao("BotaoToggle","Ativar Chão",UDim2.new(0,0,0,0), Color3.fromRGB(40,180,255))
    local botaoParar = criarBotao("BotaoParar","Parar Chão",UDim2.new(0,0,0.55,0), Color3.fromRGB(255,120,40))

    local labelAutor = Instance.new("TextLabel")
    labelAutor.Size = UDim2.new(1,0,0.1,0)
    labelAutor.Position = UDim2.new(0,0,0.9,0)
    labelAutor.TextColor3 = Color3.fromRGB(255,255,255)
    labelAutor.BackgroundTransparency = 1
    labelAutor.Text = "by Darkzin"
    labelAutor.Font = Enum.Font.FredokaOne
    labelAutor.TextScaled = true
    labelAutor.Parent = frameBotoes

    -- Mostrar/ocultar frame ao clicar no botão redondo
    botaoRedondo.MouseButton1Click:Connect(function()
        frameBotoes.Visible = not frameBotoes.Visible
    end)

    -- Botão chão
    botaoToggle.MouseButton1Click:Connect(function()
        if not chao then
            chao = Instance.new("Part")
            chao.Size = Vector3.new(2048,2,2048)
            chao.Anchored = true
            chao.Position = Vector3.new(0, player.Character:WaitForChild("HumanoidRootPart").Position.Y - 10, 0)
            chao.Color = Color3.fromRGB(100,255,100)
            chao.Name = "ChaoGigante"
            chao.Parent = workspace
        end

        if not subindo and not descendo then
            subindo = true
            descendo = false
            botaoToggle.Text = "Descer Chão"
        elseif subindo then
            subindo = false
            descendo = true
            botaoToggle.Text = "Subir Chão"
        elseif descendo then
            subindo = true
            descendo = false
            botaoToggle.Text = "Descer Chão"
        end
    end)

    botaoParar.MouseButton1Click:Connect(function()
        subindo = false
        descendo = false
        botaoToggle.Text = "Continuar"
    end)

    -- Mudar cor do botão redondo automaticamente
    task.spawn(function()
        while botaoRedondo.Parent do
            task.wait(0.5)
            botaoRedondo.BackgroundColor3 = Color3.fromRGB(
                math.random(50,255),
                math.random(50,255),
                math.random(50,255)
            )
        end
    end)
end

-- Loop do chão
task.spawn(function()
    while true do
        task.wait()
        if chao and chao.Parent then
            local dt = task.wait()
            local yAtual = chao.Position.Y
            if subindo and yAtual < alturaMaxima then
                chao.CFrame = chao.CFrame * CFrame.new(0, velocidadeChao * dt, 0)
            elseif descendo and yAtual > alturaMinima then
                chao.CFrame = chao.CFrame * CFrame.new(0, -velocidadeChao * dt, 0)
            end
        end
    end
end)

-- Criar GUI inicial
criarGUI()

-- Recriar GUI após respawn
player.CharacterAdded:Connect(function()
    task.wait(0.1)
    criarGUI()
end)
