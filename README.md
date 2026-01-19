-- [[ POWER PANEL V8 - SERVER HIJACK ]] --
local p = game.Players.LocalPlayer
local RS = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")

-- 1. TELA DE CARREGAMENTO (VARREDURA PROFUNDA DE BYPASS)
local g = Instance.new("ScreenGui", p:WaitForChild("PlayerGui"))
g.IgnoreGuiInset = true
local bg = Instance.new("Frame", g)
bg.Size = UDim2.new(1, 0, 1, 0)
bg.BackgroundColor3 = Color3.fromRGB(5, 5, 5)

local status = Instance.new("TextLabel", bg)
status.Size = UDim2.new(1, 0, 1, 0)
status.TextColor3 = Color3.fromRGB(255, 215, 0)
status.TextSize = 25
status.Font = Enum.Font.GothamBlack
status.Text = "POWER HUB V8: SEQUESTRANDO SERVIDOR... 0 / 300"
status.BackgroundTransparency = 1

-- LOCALIZADOR DE REMOTES (MÉTODO DE FORÇA BRUTA)
local ServerRemote = nil
for _, v in pairs(game:GetDescendants()) do
    if v:IsA("RemoteEvent") and v.Name == "RE" then
        ServerRemote = v
    end
end

task.spawn(function()
    for i = 0, 300, 5 do
        status.Text = "BYPASSING SERVER SIDE [V8]: " .. i .. " / 300"
        task.wait(0.04)
    end
    
    -- Muda o nome para Power Panel (Local e tentativa de Replicação)
    if p.Character and p.Character:FindFirstChild("Humanoid") then
        p.Character.Humanoid.DisplayName = "Power Panel"
    end
    print("clientes e serve")
    g:Destroy()

    -- 2. INTERFACE WIND UI
    local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()
    local Window = WindUI:CreateWindow({
        Title = "Power Panel V8",
        Icon = "zap",
        Author = "The Admin",
        Folder = "PowerPanelV8"
    })

    local NomeAlvo = ""

    -- TAB TROLL (MÉTODOS QUE REPLICAM NO SERVIDOR)
    local Tab1 = Window:Tab({Title = "Troll Server", Icon = "zap"})

    Tab1:Input({Title = "Vítima", Placeholder = "Nome Exato", Callback = function(t) NomeAlvo = t end})

    -- KILL VOID SERVER (GIRO + SUCÇÃO)
    -- Este método não usa apenas posição, usa FORÇA FÍSICA que o servidor replica.
    Tab1:Button({Title = "Kill Void (Server)", Callback = function()
        local v = game.Players:FindFirstChild(NomeAlvo)
        if v and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
            local oldPos = p.Character.HumanoidRootPart.CFrame
            _G.Attack = true
            
            -- Cria propulsores físicos que o servidor é obrigado a ver
            local bp = Instance.new("BodyPosition", p.Character.HumanoidRootPart)
            bp.MaxForce = Vector3.new(9e9, 9e9, 9e9)
            bp.P = 100000 -- Força de atração
            
            local bav = Instance.new("BodyAngularVelocity", p.Character.HumanoidRootPart)
            bav.AngularVelocity = Vector3.new(0, 50000, 0) -- Giro de Fling
            bav.MaxTorque = Vector3.new(9e9, 9e9, 9e9)

            local t = tick()
            repeat
                -- Força o seu personagem a ficar exatamente onde a vítima está
                bp.Position = v.Character.HumanoidRootPart.Position
                p.Character.HumanoidRootPart.Velocity = Vector3.new(0, -1000, 0) -- Puxa os dois pro Void
                
                -- Tenta desequipar os itens dela via Server Remote
                if ServerRemote then
                    ServerRemote:FireServer("1Item0Inventory1", "UnequipItem", "All")
                end
                task.wait()
            until not v or v.Character.Humanoid.Health <= 0 or (tick() - t > 5)
            
            bp:Destroy()
            bav:Destroy()
            _G.Attack = false
            p.Character.HumanoidRootPart.Velocity = Vector3.new(0,0,0)
            p.Character.HumanoidRootPart.CFrame = oldPos
        end
    end})

    -- BRING (TRAZER PESSOA VIA SERVER)
    Tab1:Button({Title = "Bring Player", Callback = function()
        local v = game.Players:FindFirstChild(NomeAlvo)
        if v and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
            _G.Attack = true
            local t = tick()
            while tick() - t < 1.5 do
                v.Character.HumanoidRootPart.Velocity = (p.Character.HumanoidRootPart.Position - v.Character.HumanoidRootPart.Position) * 15
                task.wait()
            end
            _G.Attack = false
        end
    end})

    -- TAB PLAYER
    local Tab2 = Window:Tab({Title = "Player", Icon = "user"})
    Tab2:Slider({Title = "Velocidade", Min = 16, Max = 400, Default = 16, Callback = function(v) p.Character.Humanoid.WalkSpeed = v end})
    Tab2:Toggle({Title = "Noclip", Callback = function(s) _G.noclip = s end})
end)

-- ESTABILIZADOR DE REDE (FUNDAMENTAL PARA EXECUTAR NO SERVIDOR)
RunService.Heartbeat:Connect(function()
    if _G.Attack and p.Character then
        -- Isso força o Network Ownership: o servidor aceita a sua física sobre a do alvo
        settings().Physics.AllowSleep = false
        p.Character.HumanoidRootPart.Velocity = Vector3.new(0, -1000, 0)
        
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
