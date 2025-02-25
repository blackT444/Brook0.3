local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer

-- Criar a RemoteEvent se não existir
local KickEvent = Instance.new("RemoteEvent")
KickEvent.Name = "KickPlayer"
KickEvent.Parent = ReplicatedStorage

-- Criar a GUI principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Criar botão Play
local PlayButton = Instance.new("TextButton")
PlayButton.Size = UDim2.new(0, 200, 0, 50)
PlayButton.Position = UDim2.new(0.5, -100, 0.4, 0) -- Posição do botão Play
PlayButton.Text = "Play"
PlayButton.BackgroundTransparency = 1 -- Deixa o fundo invisível
PlayButton.TextColor3 = Color3.new(1, 1, 1) -- Texto branco
PlayButton.Parent = ScreenGui

-- Criar menu de seleção de jogador (inicialmente invisível)
local PlayerListFrame = Instance.new("ScrollingFrame")
PlayerListFrame.Size = UDim2.new(0, 200, 0, 150)
PlayerListFrame.Position = UDim2.new(0.5, -100, 0.75, 0) -- Centraliza na parte inferior
PlayerListFrame.Visible = false
PlayerListFrame.BackgroundTransparency = 1 -- Deixa o fundo invisível
PlayerListFrame.ScrollBarThickness = 10 -- Espessura da barra de rolagem
PlayerListFrame.Parent = ScreenGui

-- Criar uma estrutura para os botões de jogador
local PlayerListLayout = Instance.new("UIListLayout")
PlayerListLayout.Parent = PlayerListFrame

-- Criar botão para Remover Jogador (inicialmente invisível, mas com texto visível)
local RemoveButton = Instance.new("TextButton")
RemoveButton.Size = UDim2.new(0, 200, 0, 50)
RemoveButton.Position = UDim2.new(0.5, -100, 0.65, 0)
RemoveButton.Text = "Remover Jogador"
RemoveButton.BackgroundTransparency = 1 -- Deixa o fundo invisível
RemoveButton.TextColor3 = Color3.new(1, 1, 1) -- Texto branco
RemoveButton.Visible = false
RemoveButton.Parent = ScreenGui

-- Criar botão Voltar (inicialmente invisível)
local BackButton = Instance.new("TextButton")
BackButton.Size = UDim2.new(0, 200, 0, 50)
BackButton.Position = UDim2.new(0.5, -100, 0.85, 0) -- Posição do botão Voltar
BackButton.Text = "Voltar"
BackButton.BackgroundTransparency = 1 -- Deixa o fundo invisível
BackButton.TextColor3 = Color3.new(1, 1, 1) -- Texto branco
BackButton.Visible = false
BackButton.Parent = ScreenGui

local SelectedPlayer = nil -- Armazena o jogador escolhido

-- Quando clicar no Play, mostrar lista de jogadores
PlayButton.MouseButton1Click:Connect(function()
    PlayerListFrame.Visible = true
    PlayButton.Visible = false
    RemoveButton.Visible = false
    BackButton.Visible = false

    -- Limpar lista de jogadores anterior
    for _, child in pairs(PlayerListFrame:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end

    -- Criar botões para cada jogador
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local PlayerButton = Instance.new("TextButton")
            PlayerButton.Size = UDim2.new(1, 0, 0, 30)
            PlayerButton.Text = player.Name
            PlayerButton.BackgroundTransparency = 1 -- Deixa o fundo invisível
            PlayerButton.TextColor3 = Color3.new(1, 1, 1) -- Texto branco
            PlayerButton.Parent = PlayerListFrame

            -- Selecionar jogador e mostrar botão Remover e Voltar
            PlayerButton.MouseButton1Click:Connect(function()
                SelectedPlayer = player
                RemoveButton.Visible = true
                BackButton.Visible = true
            end)
        end
    end
end)

-- Quando clicar no botão Remover, simular perda de conexão
RemoveButton.MouseButton1Click:Connect(function()
    if SelectedPlayer then
        KickEvent:FireServer(SelectedPlayer) -- Chamar o evento para remover o jogador
        RemoveButton.Visible = false
        PlayerListFrame.Visible = false
        PlayButton.Visible = true
    end
end)

-- Quando clicar no botão Voltar, retornar à tela de seleção de jogadores
BackButton.MouseButton1Click:Connect(function()
    PlayerListFrame.Visible = false
    PlayButton.Visible = true
    RemoveButton.Visible = false
    BackButton.Visible = false
    SelectedPlayer = nil
end)

-- Código do servidor para simular perda de conexão
KickEvent.OnServerEvent:Connect(function(player, targetPlayer)
    if targetPlayer and targetPlayer:IsA("Player") then
        targetPlayer:Kick("A conexão foi perdida. Você saiu da experiência.")
    end
end)
