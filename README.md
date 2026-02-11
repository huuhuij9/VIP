local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local CoreGui = game:GetService("CoreGui")
local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

local function CreateMenu()
    if CoreGui:FindFirstChild("HRZ_Ultimate_Menu") then
        CoreGui:FindFirstChild("HRZ_Ultimate_Menu"):Destroy()
    end

    getgenv().HBE_Enabled = false
    getgenv().ESP_Enabled = false
    getgenv().HitboxSize = 15 

    local sg = Instance.new("ScreenGui", CoreGui)
    sg.Name = "HRZ_Ultimate_Menu"
    sg.ResetOnSpawn = false

    local frame = Instance.new("Frame", sg)
    frame.Size = UDim2.new(0, 200, 0, 160)
    frame.Position = UDim2.new(0.5, -100, 0.3, 0)
    frame.BackgroundColor3 = Color3.fromRGB(45, 35, 60)
    frame.BorderSizePixel = 2
    frame.BorderColor3 = Color3.fromRGB(180, 150, 255)
    frame.Active = true
    frame.Draggable = true

    local title = Instance.new("TextLabel", frame)
    title.Size = UDim2.new(1, 0, 0, 35)
    title.Text = "HRZ SCRIPT"
    title.TextColor3 = Color3.new(1, 1, 1)
    title.BackgroundColor3 = Color3.fromRGB(120, 90, 180)
    title.Font = Enum.Font.SourceSansBold
    title.TextSize = 18

    local btnESP = Instance.new("TextButton", frame)
    btnESP.Size = UDim2.new(1, -20, 0, 40)
    btnESP.Position = UDim2.new(0, 10, 0, 45)
    btnESP.Text = "ESP: OFF"
    btnESP.BackgroundColor3 = Color3.fromRGB(80, 60, 120)
    btnESP.TextColor3 = Color3.new(1, 1, 1)

    local btnHBE = Instance.new("TextButton", frame)
    btnHBE.Size = UDim2.new(1, -20, 0, 40)
    btnHBE.Position = UDim2.new(0, 10, 0, 95)
    btnHBE.Text = "HBE: OFF"
    btnHBE.BackgroundColor3 = Color3.fromRGB(80, 60, 120)
    btnHBE.TextColor3 = Color3.new(1, 1, 1)

    btnESP.MouseButton1Click:Connect(function()
        getgenv().ESP_Enabled = not getgenv().ESP_Enabled
        btnESP.Text = getgenv().ESP_Enabled and "ESP: ON" or "ESP: OFF"
        btnESP.BackgroundColor3 = getgenv().ESP_Enabled and Color3.fromRGB(160, 100, 255) or Color3.fromRGB(80, 60, 120)
    end)

    btnHBE.MouseButton1Click:Connect(function()
        getgenv().HBE_Enabled = not getgenv().HBE_Enabled
        btnHBE.Text = getgenv().HBE_Enabled and "HBE: ON" or "HBE: OFF"
        btnHBE.BackgroundColor3 = getgenv().HBE_Enabled and Color3.fromRGB(160, 100, 255) or Color3.fromRGB(80, 60, 120)
    end)

    RunService.RenderStepped:Connect(function()
        title.TextColor3 = Color3.fromHSV((tick() * 0.5) % 1, 0.7, 1)
        
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= player and p.Character then
                local char = p.Character
                local root = char:FindFirstChild("HumanoidRootPart")
                
                -- Lógica do ESP (Highlight)
                local hl = char:FindFirstChild("HRZ_Highlight")
                if getgenv().ESP_Enabled then
                    if not hl then
                        hl = Instance.new("Highlight")
                        hl.Name = "HRZ_Highlight"
                        hl.Parent = char
                        hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                        hl.FillTransparency = 0.5
                        hl.OutlineTransparency = 0
                    end
                    hl.FillColor = Color3.fromRGB(180, 100, 255)
                else
                    if hl then hl:Destroy() end
                end
                
                -- Lógica da Hitbox
                if root then
                    local isVisible = false
                    if getgenv().HBE_Enabled then
                        local rayParams = RaycastParams.new()
                        rayParams.FilterType = Enum.RaycastFilterType.Exclude
                        rayParams.FilterDescendantsInstances = {player.Character, char}
                        
                        local origin = camera.CFrame.Position
                        local direction = (root.Position - origin)
                        local result = workspace:Raycast(origin, direction, rayParams)
                        
                        if not result then
                            isVisible = true
                        end
                    end

                    if getgenv().HBE_Enabled and isVisible then
                        root.Size = Vector3.new(getgenv().HitboxSize, getgenv().HitboxSize, getgenv().HitboxSize)
                        -- IMPORTANTE: Transparência 1 evita que o Highlight brilhe no quadrado
                        root.Transparency = 1 
                        root.CanCollide = false
                    else
                        root.Size = Vector3.new(2, 2, 1)
                        root.Transparency = 1
                        root.CanCollide = true
                    end
                end
            end
        end
    end)
end

pcall(CreateMenu)
