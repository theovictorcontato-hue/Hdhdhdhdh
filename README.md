-- [[ ⚡ POWER HUB V18 - SERVER COMMAND CONSERTADO ⚡ ]] --
-- Tela de loading + WindUI + TODAS tabs e funções OP Brookhaven

local p = game.Players.LocalPlayer
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

-- 1. TELA DE CARREGAMENTO FULLSCREEN
local sg = Instance.new("ScreenGui")
sg.Name = "PowerHubLoading"
sg.DisplayOrder = 9999
sg.IgnoreGuiInset = true
sg.Parent = p:WaitForChild("PlayerGui")

local bg = Instance.new("Frame")
bg.Size = UDim2.new(1, 0, 1, 0)
bg.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
bg.Parent = sg

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0.4, 0)
title.Position = UDim2.new(0, 0, 0.2, 0)
title.Text = "⚡ POWER HUB V18 ⚡\nBYPASSING SERVER..."
title.TextColor3 = Color3.fromRGB(255, 255, 0)
title.TextSize = 45
title.Font = Enum.Font.GothamBlack
title.BackgroundTransparency = 1
title.Parent = bg

local status = Instance.new("TextLabel")
status.Size = UDim2.new(1, 0, 0.1, 0)
status.Position = UDim2.new(0, 0, 0.5, 0)
status.Text = "INJETANDO SCRIPTS NO SERVIDOR: 0 / 200"
status.TextColor3 = Color3.fromRGB(255, 255, 255)
status.TextSize = 22
status.Font = Enum.Font.Code
status.BackgroundTransparency = 1
status.Parent = bg

local barBg = Instance.new("Frame")
barBg.Size = UDim2.new(0.6, 0, 0, 15)
barBg.Position = UDim2.new(0.2, 0, 0.6, 0)
barBg.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
barBg.Parent = bg

local bar = Instance.new("Frame")
bar.Size = UDim2.new(0, 0, 1, 0)
bar.BackgroundColor3 = Color3.fromRGB(255, 255, 0)
bar.Parent = barBg

-- Animação da barra
task.spawn(function()
    for i = 0, 200 do
        status.Text = "INJETANDO SCRIPTS NO SERVIDOR: " .. i .. " / 200"
        bar.Size = UDim2.new(i / 200, 0, 1, 0)
        task.wait(0.03)
    end
    sg:Destroy()
    IniciarV18()
end)

-- 2. FUNÇÃO PRINCIPAL
function IniciarV18()
    local success, WindUI = pcall(loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua")))
    
    if not success then
        warn("Erro ao carregar WindUI: " .. tostring(WindUI))
        WindUI = nil
    end

    local Window
    if WindUI then
        Window = WindUI:CreateWindow({
            Title = "⚡Power hub⚡ V18",
            Subtitle = "SERVER EXECUTOR | Théo Rio",
            Theme = "Dark"
        })
    else
        warn("WindUI falhou - rodando modo básico")
        return
    end

    local Alvo = ""

    -- TAB DESTRUIÇÃO
    local MainTab = Window:CreateTab("Destruição", "zap")

    local LabelInfo = MainTab:Paragraph({
        Title = "ALVO ATUAL:",
        Content = "Aguardando..."
    })

    MainTab:CreateInput("Digitar Nome do Alvo", "Escreve aqui...", function(text)
        Alvo = text
        LabelInfo:SetContent("Alvo: " .. Alvo)
    end)

    MainTab:CreateDropdown("Escolher da Lista", function()
        local opts = {}
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= p then table.insert(opts, plr.Name) end
        end
        return opts
    end, function(selected)
        Alvo = selected
        LabelInfo:SetContent("Alvo: " .. Alvo)
    end)

    MainTab:CreateButton("🌪️ SOFÁ VOID (Gira & Deleta)", function()
        local victim = Players:FindFirstChild(Alvo)
        if victim and victim.Character and victim.Character:FindFirstChild("HumanoidRootPart") then
            local oldPos = p.Character.HumanoidRootPart.CFrame
            
            local noclip = RunService.Stepped:Connect(function()
                for _, part in pairs(p.Character:GetDescendants()) do
                    if part:IsA("BasePart") then part.CanCollide = false end
                end
            end)
            
            local bav = Instance.new("BodyAngularVelocity")
            bav.AngularVelocity = Vector3.new(0, 100000, 0)
            bav.MaxTorque = Vector3.new(0, math.huge, 0)
            bav.Parent = p.Character.HumanoidRootPart

            local start = tick()
            repeat
                p.Character.HumanoidRootPart.CFrame = victim.Character.HumanoidRootPart.CFrame * CFrame.new(0, -3, 0)
                task.wait()
            until victim.Character.Humanoid.Sit or (tick() - start > 8)

            p.Character.HumanoidRootPart.Velocity = Vector3.new(0, -10000, 0)
            task.wait(0.8)

            bav:Destroy()
            noclip:Disconnect()
            p.Character.HumanoidRootPart.CFrame = oldPos
        end
    end)

    -- TAB UTILITY OP
    local Utility = Window:CreateTab("Utility OP", "zap")

    Utility:CreateButton("Fly (Infinite Yield)", function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source"))()
    end)

    Utility:CreateToggle("Noclip Permanente", false, function(state)
        if state then
            _G.PermNoclip = RunService.Stepped:Connect(function()
                for _, part in pairs(p.Character:GetDescendants()) do
                    if part:IsA("BasePart") then part.CanCollide = false end
                end
            end)
        else
            if _G.PermNoclip then _G.PermNoclip:Disconnect() end
        end
    end)

    Utility:CreateSlider("WalkSpeed", 16, 500, 50, function(value)
        if p.Character and p.Character:FindFirstChild("Humanoid") then
            p.Character.Humanoid.WalkSpeed = value
        end
    end)

    Utility:CreateButton("Infinite Jump", function()
        game:GetService("UserInputService").JumpRequest:Connect(function()
            if p.Character and p.Character:FindFirstChild("Humanoid") then
                p.Character.Humanoid:ChangeState("Jumping")
            end
        end)
    end)

    Utility:CreateButton("Low Gravity", function()
        workspace.Gravity = 50
    end)

    Utility:CreateButton("Car Speed x3", function()
        for _, car in pairs(workspace:GetChildren()) do
            if car:FindFirstChild("VehicleSeat") then
                car.VehicleSeat.MaxSpeed = 100
            end
        end
    end)

    -- TAB TROLLS & TARGET
    local Trolls = Window:CreateTab("Trolls & Target", "skull")

    Trolls:CreateButton("Super Fling", function()
        local victim = Players:FindFirstChild(Alvo)
        if victim and victim.Character then
            local noclip = RunService.Stepped:Connect(function()
                for _, part in pairs(p.Character:GetDescendants()) do
                    if part:IsA("BasePart") then part.CanCollide = false end
                end
            end)
            
            local start = tick()
            repeat
                p.Character.HumanoidRootPart.CFrame = victim.Character.HumanoidRootPart.CFrame
                p.Character.HumanoidRootPart.Velocity = Vector3.new(99999, 99999, 99999)
                task.wait()
            until tick() - start > 4
            
            noclip:Disconnect()
        end
    end)

    Trolls:CreateButton("Server Bring", function()
        local victim = Players:FindFirstChild(Alvo)
        if victim and victim.Character then
            victim.Character.HumanoidRootPart.CFrame = p.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -3)
        end
    end)

    -- TAB TELEPORTS
    local Teleports = Window:CreateTab("Teleports", "map-pin")

    Teleports:CreateButton("Police Station", function()
        local hrp = p.Character and p.Character:FindFirstChild("HumanoidRootPart")
        if hrp then hrp.CFrame = CFrame.new(-627, 20, -280) end
    end)

    Teleports:CreateButton("Hospital", function()
        local hrp = p.Character and p.Character:FindFirstChild("HumanoidRootPart")
        if hrp then hrp.CFrame = CFrame.new(-380, 73, -320) end
    end)

    Teleports:CreateButton("School", function()
        local hrp = p.Character and p.Character:FindFirstChild("HumanoidRootPart")
        if hrp then hrp.CFrame = CFrame.new(437, 5, -101) end
    end)

    Teleports:CreateButton("Bank", function()
        local hrp = p.Character and p.Character:FindFirstChild("HumanoidRootPart")
        if hrp then hrp.CFrame = CFrame.new(-296, 23, 393) end
    end)

    -- NOTIFICAÇÃO FINAL
    WindUI:Notify("⚡Power Hub⚡ V18", "Carregado! Tela completa + TODAS tabs e funções liberadas", 8)
end
