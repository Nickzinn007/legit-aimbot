local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")

-- ===================== SCRIPT PRINCIPAL =====================
function loadMainScript()
    _G.HeadSize = 8
    _G.Disabled = true

    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "CabeçaoHUD"
    screenGui.ResetOnSpawn = false
    screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

    local mainFrame = Instance.new("Frame")
    mainFrame.Size = UDim2.new(0, 300, 0, 230)
    mainFrame.Position = UDim2.new(0.5, -150, 0.5, -115)
    mainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
    mainFrame.BorderSizePixel = 0
    mainFrame.Parent = screenGui

    local uiCorner = Instance.new("UICorner")
    uiCorner.CornerRadius = UDim.new(0, 18)
    uiCorner.Parent = mainFrame

    local uiStroke = Instance.new("UIStroke")
    uiStroke.Color = Color3.fromRGB(255, 40, 40)
    uiStroke.Thickness = 4
    uiStroke.Transparency = 0.3
    uiStroke.Parent = mainFrame

    local uiGradient = Instance.new("UIGradient")
    uiGradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, Color3.fromRGB(35, 35, 45)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(18, 18, 22))
    }
    uiGradient.Rotation = 45
    uiGradient.Parent = mainFrame

    local titleLabel = Instance.new("TextLabel")
    titleLabel.Size = UDim2.new(1, 0, 0, 38)
    titleLabel.Position = UDim2.new(0, 0, 0, 5)
    titleLabel.BackgroundTransparency = 1
    titleLabel.Text = "MK CABEÇAO MENU"
    titleLabel.TextColor3 = Color3.fromRGB(255, 70, 70)
    titleLabel.TextScaled = true
    titleLabel.Font = Enum.Font.Gotham
    titleLabel.Parent = mainFrame

    -- 🎮 DRAG
    local dragging = false
    local dragInput
    local dragStart
    local startPos

    mainFrame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = mainFrame.Position
        end
    end)

    mainFrame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if dragging and input == dragInput then
            local delta = input.Position - dragStart
            mainFrame.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end)

    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)

    -- 🔘 TOGGLE
    local toggleBtn = Instance.new("TextButton")
    toggleBtn.Size = UDim2.new(0.85, 0, 0, 45)
    toggleBtn.Position = UDim2.new(0.075, 0, 0, 48)
    toggleBtn.BackgroundColor3 = Color3.fromRGB(0, 220, 80)
    toggleBtn.Text = "ATIVADO"
    toggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    toggleBtn.TextScaled = true
    toggleBtn.Font = Enum.Font.Gotham
    toggleBtn.Parent = mainFrame

    local toggleCorner = Instance.new("UICorner", toggleBtn)
    toggleCorner.CornerRadius = UDim.new(0, 12)

    toggleBtn.MouseButton1Click:Connect(function()
        _G.Disabled = not _G.Disabled
        toggleBtn.Text = _G.Disabled and "ATIVADO" or "DESATIVADO"
        toggleBtn.BackgroundColor3 = _G.Disabled and Color3.fromRGB(0, 220, 80) or Color3.fromRGB(220, 40, 40)
    end)

    -- 📏 SIZE
    local sizeDisplay = Instance.new("TextLabel")
    sizeDisplay.Size = UDim2.new(0.85, 0, 0, 24)
    sizeDisplay.Position = UDim2.new(0.075, 0, 0, 110)
    sizeDisplay.BackgroundTransparency = 1
    sizeDisplay.Text = tostring(_G.HeadSize)
    sizeDisplay.TextColor3 = Color3.fromRGB(255, 220, 60)
    sizeDisplay.TextScaled = true
    sizeDisplay.Font = Enum.Font.Gotham
    sizeDisplay.Parent = mainFrame

    local minusBtn = Instance.new("TextButton")
    minusBtn.Size = UDim2.new(0.22, 0, 0, 40)
    minusBtn.Position = UDim2.new(0.075, 0, 0, 135)
    minusBtn.Text = "-"
    minusBtn.Parent = mainFrame

    local plusBtn = Instance.new("TextButton")
    plusBtn.Size = UDim2.new(0.22, 0, 0, 40)
    plusBtn.Position = UDim2.new(0.70, 0, 0, 135)
    plusBtn.Text = "+"
    plusBtn.Parent = mainFrame

    minusBtn.MouseButton1Click:Connect(function()
        _G.HeadSize = math.max(1, _G.HeadSize - 1)
        sizeDisplay.Text = tostring(_G.HeadSize)
    end)

    plusBtn.MouseButton1Click:Connect(function()
        _G.HeadSize += 1
        sizeDisplay.Text = tostring(_G.HeadSize)
    end)

    -- 💥 LOOP
    game:GetService("RunService").RenderStepped:Connect(function()
        for _, v in pairs(Players:GetPlayers()) do
            if v ~= LocalPlayer then
                pcall(function()
                    if v.Character and v.Character:FindFirstChild("Head") then
                        if _G.Disabled then
                            v.Character.Head.Size = Vector3.new(_G.HeadSize, _G.HeadSize, _G.HeadSize)
                        else
                            v.Character.Head.Size = Vector3.new(1,1,1)
                        end
                    end
                end)
            end
        end
    end)
end

-- 🚀 executa direto
loadMainScript()
