-- [[ ⚡ POWER HUB V11 - SERVER DESTROYER ⚡ ]] --
local p = game.Players.LocalPlayer
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local RE = game:GetService("ReplicatedStorage"):FindFirstChild("RE", true)

-- 1. IDENTIDADE POWER HUB
if p.Character and p.Character:FindFirstChild("Humanoid") then
    p.Character.Humanoid.DisplayName = "⚡Power hub⚡"
end

-- FUNÇÃO DE LISTA
local function GetPlayerList()
    local lista = {}
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= p then table.insert(lista, player.Name) end
    end
    return lista
end

-- 2. INTERFACE (WIND UI)
local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()
local Window = WindUI:CreateWindow({
    Title = "⚡Power hub⚡ V11",
    Icon = "zap",
    Author = "By Power Hub",
    Folder = "PowerHubV11"
})

local NomeAlvo = ""
local Tab1 = Window:Tab({Title = "Troll Principal", Icon = "skull"})
local TabOp = Window:Tab({Title = "OP Functions", Icon = "unlocked"})

-- TEXTO DE INSTRUÇÃO
Tab1:Paragraph({
    Title = "COMO USAR:",
    Content = "Selecione o alvo. O BRING agora gira a pessoa em volta de você em alta velocidade. O KILL VOID leva ela para o abismo."
})

local Dropdown = Tab1:Dropdown({
    Title = "Selecionar Vítima",
    Options = GetPlayerList(),
    Callback = function(t) NomeAlvo = t end
})

Tab1:Button({Title = "🔄 Atualizar Lista", Callback = function() Dropdown:Refresh(GetPlayerList()) end})

-- KILL VOID REFORMULADO (LEVA ONDE VOCÊ ESTAVA)
Tab1:Button({Title = "☠️ KILL VOID (Sofá)", Callback = function()
    local v = Players:FindFirstChild(NomeAlvo)
    if v and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
        local posOriginal = p.Character.HumanoidRootPart.CFrame
        _G.TrollAtivo = true
        
        -- Cria a força de descida
        local bv = Instance.new("BodyVelocity", p.Character.HumanoidRootPart)
        bv.MaxForce = Vector3.new(9e9, 9e9, 9e9)
        bv.Velocity = Vector3.new(0, -500, 0) -- Descida rápida

        local t = tick()
        repeat
            p.Character.HumanoidRootPart.CFrame = v.Character.HumanoidRootPart.CFrame
            p.Character.HumanoidRootPart.Velocity = Vector3.new(0, -800, 0)
            task.wait()
        until v.Character.HumanoidRootPart.Position.Y < -50 or (tick() - t > 5)
        
        bv:Destroy()
        _G.TrollAtivo = false
        p.Character.HumanoidRootPart.CFrame = posOriginal -- Volta pra onde você ativou
    end
end})

-- BRING TURBO (GIRA RÁPIDO NA PESSOA)
Tab1:Button({Title = "🧲 BRING TURBO (Giro)", Callback = function()
    local v = Players:FindFirstChild(NomeAlvo)
    if v and v.Character then
        _G.TrollAtivo = true
        local t = tick()
        while tick() - t < 4 do
            local angle = tick() * 25 -- Velocidade do giro
            local offset = Vector3.new(math.cos(angle) * 7, 0, math.sin(angle) * 7)
            v.Character.HumanoidRootPart.CFrame = p.Character.HumanoidRootPart.CFrame + offset
            v.Character.HumanoidRootPart.Velocity = Vector3.new(0, 50, 0) -- Levanta ela um pouco
            task.wait()
        end
        _G.TrollAtivo = false
    end
end})

-- TAB OP FUNCTIONS (REPLICAÇÃO SERVER-SIDE)
TabOp:Button({Title = "🔥 Server Fling (Giro da Morte)", Callback = function()
    _G.TrollAtivo = true
    local bav = Instance.new("BodyAngularVelocity", p.Character.HumanoidRootPart)
    bav.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
    bav.AngularVelocity = Vector3.new(0, 99999, 0)
    task.wait(5)
    bav:Destroy()
    _G.TrollAtivo = false
end})

TabOp:Button({Title = "🚗 Car Rain (Lote/Carro Bug)", Callback = function()
    if RE then
        for i = 1, 10 do
            RE:FireServer("1Property0Emergency1", i, "VehicleSpeed", 200)
            task.wait(0.1)
        end
    end
end})

TabOp:Button({Title = "🌑 Blackout (Visual)", Callback = function()
    local sky = Instance.new("Sky", game.Lighting)
    sky.SkyboxBk = "rbxassetid://0"
    sky.SkyboxDn = "rbxassetid://0"
    sky.SkyboxFt = "rbxassetid://0"
    sky.SkyboxLf = "rbxassetid://0"
    sky.SkyboxRt = "rbxassetid://0"
    sky.SkyboxUp = "rbxassetid://0"
    game.Lighting.ClockTime = 0
end})

-- CONFIG
local Tab2 = Window:Tab({Title = "Config", Icon = "settings"})
Tab2:Slider({Title = "Velocidade", Min = 16, Max = 400, Default = 16, Callback = function(v) p.Character.Humanoid.WalkSpeed = v end})
Tab2:Toggle({Title = "Noclip", Callback = function(s) _G.noclip = s end})

-- LOOP DE FÍSICA
RunService.Stepped:Connect(function()
    if _G.TrollAtivo and p.Character then
        for _, v in pairs(p.Character:GetDescendants()) do
            if v:IsA("BasePart") then v.CanCollide = false end
        end
    end
    if _G.noclip and p.Character then
        for _, v in pairs(p.Character:GetDescendants()) do
            if v:IsA("BasePart") then v.CanCollide = false end
        end
    end
end)
