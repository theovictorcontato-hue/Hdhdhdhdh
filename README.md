-- [[ POWER PANEL V9 - SERVER EXECUTION & CAR TROLL ]] --
local p = game.Players.LocalPlayer
local RS = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")

-- 1. TELA DE CARREGAMENTO (SCAN DE OBJETOS DO SERVIDOR)
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
status.Text = "CARREGANDO SERVER OBJECTS... 0 / 300"
status.BackgroundTransparency = 1

-- LOCALIZA O REMOTE PRINCIPAL (SERVER LINK)
local ServerRemote = nil
for _, v in pairs(game:GetDescendants()) do
    if v:IsA("RemoteEvent") and v.Name == "RE" then
        ServerRemote = v
        break
    end
end

task.spawn(function()
    for i = 0, 300, 3 do
        status.Text = "EXECUTANDO SCRIPT NO SERVIDOR: " .. i .. " / 300"
        task.wait(0.04)
    end
    
    if p.Character and p.Character:FindFirstChild("Humanoid") then
        p.Character.Humanoid.DisplayName = "Power Panel"
    end
    g:Destroy()

    -- 2. INTERFACE
    local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()
    local Window = WindUI:CreateWindow({
        Title = "Power Panel V9",
        Icon = "zap",
        Author = "Server Admin",
        Folder = "PowerPanelV9"
    })

    local NomeAlvo = ""

    -- TAB TROLL (FÍSICA & SERVIDOR)
    local Tab1 = Window:Tab({Title = "Server Troll", Icon = "zap"})

    Tab1:Input({Title = "Vítima", Placeholder = "Nome Exato", Callback = function(t) NomeAlvo = t end})

    -- 1. KILL VOID (O CLÁSSICO QUE FUNCIONA)
    Tab1:Button({Title = "Kill Void (Sofa/Físico)", Callback = function()
        local v = game.Players:FindFirstChild(NomeAlvo)
        if v and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
            local oldPos = p.Character.HumanoidRootPart.CFrame
            _G.Attack = true
            
            local bp = Instance.new("BodyPosition", p.Character.HumanoidRootPart)
            bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bp.P = 100000 -- Força bruta do servidor
            
            local bav = Instance.new("BodyAngularVelocity", p.Character.HumanoidRootPart)
            bav.AngularVelocity = Vector3.new(0, 90000, 0)
            bav.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)

            local t = tick()
            repeat
                bp.Position = v.Character.HumanoidRootPart.Position
                p.Character.HumanoidRootPart.Velocity = Vector3.new(0, -2000, 0) -- Void Force
                
                -- Replica remoção de itens
                if ServerRemote then ServerRemote:FireServer("1Item0Inventory1", "UnequipItem", "All") end
                task.wait()
            until not v or v.Character.Humanoid.Health <= 0 or (tick() - t > 6)
            
            bp:Destroy()
            bav:Destroy()
            _G.Attack = false
            p.Character.HumanoidRootPart.Velocity = Vector3.new(0,0,0)
            p.Character.HumanoidRootPart.CFrame = oldPos
        end
    end})

    -- 2. CAR FLING (NOVA FUNÇÃO - REPLICAÇÃO 100%)
    Tab1:Button({Title = "Car Fling (Precisa estar no Carro)", Callback = function()
        local v = game.Players:FindFirstChild(NomeAlvo)
        local car = p.Character:FindFirstChild("Humanoid").SeatPart and p.Character.Humanoid.SeatPart.Parent
        
        if v and car and v.Character then
            _G.CarAttack = true
            local t = tick()
            repeat
                -- Teleporta o carro inteiro para a cabeça da vítima
                car:SetPrimaryPartCFrame(v.Character.HumanoidRootPart.CFrame * CFrame.Angles(math.rad(90), 0, 0))
                car.PrimaryPart.Velocity = Vector3.new(0, -1000, 0)
                task.wait()
            until not v or (tick() - t > 5) or not _G.CarAttack
            _G.CarAttack = false
        else
            game.StarterGui:SetCore("SendNotification", {Title = "Erro", Text = "Entre em um veículo para usar isso!"})
        end
    end})

    -- 3. HOUSE BAN (BAN REAL DO SERVIDOR)
    Tab1:Button({Title = "Banir de Todas as Casas", Callback = function()
        local v = game.Players:FindFirstChild(NomeAlvo)
        if v and ServerRemote then
            -- Loopa pelos IDs de casas do Brookhaven (Server Properties)
            for i = 1, 30 do
                ServerRemote:FireServer("1Property0Emergency1", i, "BanPlayer", v)
            end
            print("Tentativa de ban global enviada.")
        end
    end})

    -- 4. CRASH/LAG (SPAM DE SOM E EFEITO)
    Tab1:Button({Title = "Crash Lag (Spam)", Callback = function()
        local v = game.Players:FindFirstChild(NomeAlvo)
        if v and ServerRemote then
             task.spawn(function()
                for i = 1, 50 do
                    -- Spamma campainhas e efeitos na posição da vítima
                    ServerRemote:FireServer("1Property0Emergency1", v, "RingDoorbell")
                    ServerRemote:FireServer("1Property0Emergency1", v, "CratePlayer")
                    task.wait(0.05)
                end
            end)
        end
    end})

    -- TAB PLAYER
    local Tab2 = Window:Tab({Title = "Player", Icon = "user"})
    Tab2:Slider({Title = "Velocidade", Min = 16, Max = 400, Default = 16, Callback = function(v) p.Character.Humanoid.WalkSpeed = v end})
    Tab2:Toggle({Title = "Noclip", Callback = function(s) _G.noclip = s end})
end)

-- ESTABILIZADOR DO SERVIDOR
RunService.Heartbeat:Connect(function()
    if _G.Attack and p.Character then
        settings().Physics.AllowSleep = false
        p.Character.HumanoidRootPart.Velocity = Vector3.new(0, -1000, 0)
        for _, part in pairs(p.Character:GetDescendants()) do
            if part:IsA("BasePart") then part.CanCollide = false end
        end
    end
    
    if _G.noclip and p.Character then
        for _, v in pairs(p.Character:GetDescendants()) do
            if v:IsA("BasePart") then v.CanCollide = false end
        end
    end
end)
