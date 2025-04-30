if game.CoreGui:FindFirstChild("StaticCrosshairGUI") then
    game.CoreGui.StaticCrosshairGUI:Destroy()
end
if game.CoreGui:FindFirstChild("StaticCrosshair") then
    game.CoreGui.StaticCrosshair:Destroy()
end

local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local screenGui = Instance.new("ScreenGui", game.CoreGui)
screenGui.Name = "StaticCrosshairGUI"
screenGui.ResetOnSpawn = false

local mainFrame = Instance.new("Frame", screenGui)
mainFrame.Size = UDim2.new(0, 250, 0, 100)
mainFrame.Position = UDim2.new(0.5, -125, 0.1, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BorderSizePixel = 0
mainFrame.Visible = true

local title = Instance.new("TextLabel", mainFrame)
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.Text = "RonX Crosshair GUI"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 18

local sliderBar = Instance.new("Frame", mainFrame)
sliderBar.Size = UDim2.new(0, 200, 0, 4)
sliderBar.Position = UDim2.new(0, 25, 0, 50)
sliderBar.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
sliderBar.BorderSizePixel = 0

local knob = Instance.new("Frame", sliderBar)
knob.Size = UDim2.new(0, 10, 0, 16)
knob.Position = UDim2.new(0.5, -5, 0, -6)
knob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
knob.BorderSizePixel = 0

local offsetLabel = Instance.new("TextLabel", mainFrame)
offsetLabel.Size = UDim2.new(1, 0, 0, 20)
offsetLabel.Position = UDim2.new(0, 0, 1, -20)
offsetLabel.BackgroundTransparency = 1
offsetLabel.TextColor3 = Color3.new(1, 1, 1)
offsetLabel.Font = Enum.Font.SourceSans
offsetLabel.TextSize = 14
offsetLabel.Text = "Offset Y: 0"

local crosshairGui = Instance.new("ScreenGui", game.CoreGui)
crosshairGui.Name = "StaticCrosshair"

local enabled = true
local heightOffset = 0
local thickness = 2
local length = 10
local dragging = false

local function createLine(name)
    local line = Instance.new("Frame")
    line.Name = name
    line.Size = UDim2.new(0, 0, 0, 0)
    line.Position = UDim2.new(0, 0, 0, 0)
    line.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    line.BorderSizePixel = 0
    line.Visible = true
    line.Parent = crosshairGui
    return line
end

local top = createLine("Top")
local bottom = createLine("Bottom")
local left = createLine("Left")
local right = createLine("Right")

local function drawCrosshair()
    local cam = workspace.CurrentCamera
    if not cam then return end

    local screenSize = cam.ViewportSize
    local centerX = screenSize.X / 2
    local centerY = screenSize.Y / 2 + heightOffset

    local visible = enabled

    top.Visible = visible
    bottom.Visible = visible
    left.Visible = visible
    right.Visible = visible

    top.Size = UDim2.new(0, thickness, 0, length)
    bottom.Size = UDim2.new(0, thickness, 0, length)
    left.Size = UDim2.new(0, length, 0, thickness)
    right.Size = UDim2.new(0, length, 0, thickness)

    top.Position = UDim2.new(0, centerX - thickness/2, 0, centerY - length - 2)
    bottom.Position = UDim2.new(0, centerX - thickness/2, 0, centerY + 2)
    left.Position = UDim2.new(0, centerX - length - 2, 0, centerY - thickness/2)
    right.Position = UDim2.new(0, centerX + 2, 0, centerY - thickness/2)
end

local function updateSlider(inputX)
    local relativeX = math.clamp(inputX - sliderBar.AbsolutePosition.X, 0, sliderBar.AbsoluteSize.X)
    knob.Position = UDim2.new(0, relativeX - 5, 0, -6)
    heightOffset = math.floor((relativeX / sliderBar.AbsoluteSize.X) * 200) - 100
    offsetLabel.Text = "Offset Y: " .. heightOffset
    drawCrosshair()
end

knob.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        updateSlider(input.Position.X)
    end
end)

UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.V then
        enabled = not enabled
        drawCrosshair()
    elseif input.KeyCode == Enum.KeyCode.P then
        mainFrame.Visible = not mainFrame.Visible
    end
end)

RunService.RenderStepped:Connect(drawCrosshair)
