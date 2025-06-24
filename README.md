--[[
hunteXscriptV2 - Script completo para BloxFruits
GUI adaptada para mobile, com funcionalidades para Sea 1, Sea 2 e Sea 3
]]

--// Serviços
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")
local CoreGui = game:GetService("CoreGui")

local LocalPlayer = Players.LocalPlayer

--// Configuração inicial e armazenamento local
local config = {
    autoFarm = false,
    autoQuest = false,
    autoHaki = false,
    AutoFruit = false,
    ESP = false,
    Aimbot = false,
    currentSea = "Sea 1"
}

local filename = "bloxfruits_hub_config.json"
if isfile and writefile and readfile and isfile(filename) then
    local ok, saved = pcall(function()
        return HttpService:JSONDecode(readfile(filename))
    end)
    if ok and type(saved) == "table" then
        for k, v in pairs(saved) do
            config[k] = v
        end
    end
end

getfenv()._G.BFUserConfig = setmetatable({}, {
    __index = config,
    __newindex = function(_, key, val)
        config[key] = val
        if writefile then
            writefile(filename, HttpService:JSONEncode(config))
        end
    end
})
local enabled = getfenv()._G.BFUserConfig

--// Funções principais

function autoFarm()
    if not enabled.autoFarm then return end
    -- Aqui você pode adicionar o código real do auto farm
    -- Exemplo simples (placeholder)
    -- print("AutoFarm ativado")
end

function autoQuest()
    if not enabled.autoQuest then return end
    -- Código do auto quest aqui
    -- print("AutoQuest ativado")
end

function activateHaki()
    if not enabled.autoHaki then return end
    -- Código para ativar Haki
    -- print("AutoHaki ativado")
end

function fruitSniper()
    if not enabled.AutoFruit then return end
    for _, item in pairs(Workspace:GetChildren()) do
        if item:IsA("Tool") and item:FindFirstChild("Handle") then
            local dist = (item.Handle.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
            if dist < 300 then
                firetouchinterest(item.Handle, LocalPlayer.Character.HumanoidRootPart, 0)
                wait(0.1)
                firetouchinterest(item.Handle, LocalPlayer.Character.HumanoidRootPart, 1)
            end
        end
    end
end

local function enableESP()
    if not enabled.ESP then
        for _, gui in pairs(Workspace:GetDescendants()) do
            if gui:IsA("BillboardGui") and gui.Name == "PlayerESP" then
                gui:Destroy()
            end
        end
        return
    end

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local exists = player.Character:FindFirstChild("PlayerESP")
            if not exists then
                local esp = Instance.new("BillboardGui", player.Character)
                esp.Name = "PlayerESP"
                esp.Size = UDim2.new(0, 200, 0, 50)
                esp.AlwaysOnTop = true
                esp.Adornee = player.Character.HumanoidRootPart
                local label = Instance.new("TextLabel", esp)
                label.Size = UDim2.new(1, 0, 1, 0)
                label.Text = player.Name
                label.TextColor3 = Color3.new(1, 1, 1)
                label.BackgroundTransparency = 1
                label.TextStrokeTransparency = 0.5
            end
        end
    end
end

function aimbot()
    if not enabled.Aimbot then return end
    local closestEnemy, shortestDist = nil, math.huge
    if not Workspace:FindFirstChild("Enemies") then return end
    for _, enemy in pairs(Workspace.Enemies:GetChildren()) do
        if enemy:FindFirstChild("HumanoidRootPart") and enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
            local dist = (enemy.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
            if dist < shortestDist then
                shortestDist = dist
                closestEnemy = enemy
            end
        end
    end
    if closestEnemy then
        Workspace.CurrentCamera.CFrame = CFrame.new(Workspace.CurrentCamera.CFrame.Position, closestEnemy.HumanoidRootPart.Position)
    end
end

--// Loop principal para executar as funções automaticamente
RunService.RenderStepped:Connect(function()
    pcall(function()
        autoQuest()
        autoFarm()
        activateHaki()
        aimbot()
        fruitSniper()
        enableESP()
    end)
end)

--// GUI para mobile

local TweenService = game:GetService("TweenService")

-- Frame principal
local frame = Instance.new("Frame")
frame.Name = "hunteXscriptV2Frame"
frame.Parent = CoreGui
frame.AnchorPoint = Vector2.new(0.5, 0)
frame.Position = UDim2.new(0.5, 0, 0.05, 0)
frame.Size = UDim2.new(0, 320, 0, 450)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0
frame.ZIndex = 999999

-- Título
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Text = "hunteXscriptV2"
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.BorderSizePixel = 0

-- Aba lateral vertical
local tabFrame = Instance.new("Frame", frame)
tabFrame.Size = UDim2.new(0, 100, 0, 400)
tabFrame.Position = UDim2.new(0, 0, 0, 40)
tabFrame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
tabFrame.BorderSizePixel = 0

-- Container dos botões na direita
local buttonContainer = Instance.new("Frame", frame)
buttonContainer.Size = UDim2.new(0, 210, 0, 400)
buttonContainer.Position = UDim2.new(0, 110, 0, 40)
buttonContainer.BackgroundTransparency = 1

-- Lista de abas
local seaTabs = {"Sea 1", "Sea 2", "Sea 3"}
local currentTab = "Sea 1"

-- Função para mostrar notificações rápidas
local notificationFrame = Instance.new("Frame", frame)
notificationFrame.Size = UDim2.new(1, -20, 0, 30)
notificationFrame.Position = UDim2.new(0, 10, 1, -40)
notificationFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
notificationFrame.BorderSizePixel = 0
notificationFrame.Visible = false

local notificationLabel = Instance.new("TextLabel", notificationFrame)
notificationLabel.Size = UDim2.new(1, 0, 1, 0)
notificationLabel.BackgroundTransparency = 1
notificationLabel.TextColor3 = Color3.new(1, 1, 1)
notificationLabel.TextScaled = true
notificationLabel.Font = Enum.Font.GothamBold

function notify(text, duration)
    notificationLabel.Text = text
    notificationFrame.Visible = true
    delay(duration or 3, function()
        notificationFrame.Visible = false
    end)
end

-- Criar botões da aba lateral vertical
for i, sea in pairs(seaTabs) do
    local tab = Instance.new("TextButton", tabFrame)
    tab.Size = UDim2.new(1, 0, 0, 40)
    tab.Position = UDim2.new(0, 0, 0, (i - 1) * 40)
    tab.Text = sea
    tab.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
    tab.TextColor3 = Color3.new(1, 1, 1)
    tab.Font = Enum.Font.GothamBold
    tab.TextScaled = true
    tab.BorderSizePixel = 0
    tab.AutoButtonColor = true
    tab.MouseButton1Click:Connect(function()
        currentTab = sea
        notify("Aba alterada para "..sea, 2)
        setupButtonsMobile()
    end)
end

-- Função para criar botões grandes para mobile
local yBtn = 10
local function addButtonMobile(name, callback)
    local btn = Instance.new("TextButton", buttonContainer)
    btn.Size = UDim2.new(1, -20, 0, 50)
    btn.Position = UDim2.new(0, 10, 0, yBtn)
    btn.BackgroundColor3 = Color3.fromRGB(65, 65, 65)
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.TextScaled = true
    btn.Font = Enum.Font.GothamBold
    btn.Text = name
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = true
    btn.MouseButton1Click:Connect(function()
        callback()
    end)
    yBtn = yBtn + 60
end

-- Setup dos botões conforme a aba selecionada
function setupButtonsMobile()
    yBtn = 10
    buttonContainer:ClearAllChildren()
    if currentTab == "Sea 1" then
        addButtonMobile("Toggle Auto Farm", function()
            enabled.autoFarm = not enabled.autoFarm
            notify("Auto Farm: "..tostring(enabled.autoFarm), 2)
        end)
        addButtonMobile("Toggle Auto Quest", function()
            enabled.autoQuest = not enabled.autoQuest
            notify("Auto Quest: "..tostring(enabled.autoQuest), 2)
        end)
        addButtonMobile("Toggle Auto Haki", function()
            enabled.autoHaki = not enabled.autoHaki
            notify("Auto Haki: "..tostring(enabled.autoHaki), 2)
        end)
    elseif currentTab == "Sea 2" then
        addButtonMobile("Toggle ESP", function()
            enabled.ESP = not enabled.ESP
            notify("ESP: "..tostring(enabled.ESP), 2)
        end)
        addButtonMobile("Toggle Aimbot", function()
            enabled.Aimbot = not enabled.Aimbot
            notify("Aimbot: "..tostring(enabled.Aimbot), 2)
        end)
    elseif currentTab == "Sea 3" then
        addButtonMobile("Toggle AutoFruit", function()
            enabled.AutoFruit = not enabled.AutoFruit
            notify("AutoFruit: "..tostring(enabled.AutoFruit), 2)
        end)
    end
end

-- Inicializar botões
setupButtonsMobile()
