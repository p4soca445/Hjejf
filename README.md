-- Aguarda o jogo carregar completamente
repeat wait() until game:IsLoaded()

-- Definir o jogador e o personagem
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

-- Variáveis de transformação
local isHero = false
local speed = 100  -- Velocidade de voo ou corrida do herói
local jumpPower = 150  -- Força de pulo do herói

-- Criar botão de transformação na tela
local ScreenGui = Instance.new("ScreenGui")
local TransformButton = Instance.new("TextButton")

ScreenGui.Parent = game.CoreGui
TransformButton.Parent = ScreenGui
TransformButton.Size = UDim2.new(0, 200, 0, 50)
TransformButton.Position = UDim2.new(0.4, 0, 0.85, 0)  -- Posição do botão
TransformButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
TransformButton.TextSize = 20
TransformButton.Text = "Transformar em Herói"
TransformButton.TextColor3 = Color3.fromRGB(255, 255, 255)

-- Função de transformação
local function transformHero()
    if isHero then
        -- Desfaz a transformação
        isHero = false
        humanoid.WalkSpeed = 16  -- Velocidade normal
        humanoid.JumpPower = 50  -- Força de pulo normal
        humanoidRootPart.Color = Color3.fromRGB(255, 255, 255)  -- Cor normal

        -- Desativar o voo, se necessário
        if character:FindFirstChild("BodyVelocity") then
            character.BodyVelocity:Destroy()
        end

        TransformButton.Text = "Transformar em Herói"
        TransformButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)

    else
        -- Ativa a transformação
        isHero = true
        humanoid.WalkSpeed = speed  -- Aumenta a velocidade
        humanoid.JumpPower = jumpPower  -- Aumenta o pulo
        humanoidRootPart.Color = Color3.fromRGB(0, 0, 255)  -- Muda a cor para representar transformação

        -- Adiciona efeitos de voo
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = Vector3.new(500000, 500000, 500000)
        bodyVelocity.Velocity = Vector3.new(0, 50, 0)  -- Dirigir o personagem para cima (efeito de voo)
        bodyVelocity.Parent = humanoidRootPart

        -- Alterar o texto e cor do botão
        TransformButton.Text = "Destransformar"
        TransformButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
    end
end

-- Conectar o botão para ativar/desativar a transformação
TransformButton.MouseButton1Click:Connect(transformHero)

-- Função para controle do voo
game:GetService("RunService").RenderStepped:Connect(function()
    if isHero then
        -- O personagem vai voar para a direção que o jogador está olhando
        local cameraDirection = workspace.CurrentCamera.CFrame.LookVector
        humanoidRootPart.CFrame = humanoidRootPart.CFrame + cameraDirection * 0.5  -- Movimento do voo
    end
end)

-- Mensagem para informar ao jogador
game.StarterGui:SetCore("SendNotification", {
    Title = "Transformação Ativada!";
    Text = "Clique no botão para se tornar um herói!";
    Duration = 5;
})
