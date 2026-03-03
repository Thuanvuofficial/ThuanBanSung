--[[
    FULL AIMBOT + ESP MENU FOR SNIPER ARENA
    Tác giả: Tự viết
    Tính năng:
    - Aimbot (Head/Body)
    - ESP (Box, Name, Health, Distance)
    - Triggerbot
    - No Recoil / No Spread (cơ bản)
    - Speed / Jump Boost
    - Giao diện đẹp, dễ dùng
]]

-- Khởi tạo dịch vụ
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Biến cấu hình toàn cục
local Settings = {
    Aimbot = {
        Enabled = false,
        TargetPart = "Head",           -- "Head" hoặc "HumanoidRootPart"
        MaxDistance = 500,
        FOV = 180,                      -- góc nhìn 180 độ = nhắm mọi hướng
        Lock = false,                    -- khóa mục tiêu (sẽ bám theo)
    },
    ESP = {
        Enabled = false,
        Box = true,
        Name = true,
        Health = true,
        Distance = true,
        TeamCheck = false,               -- chỉ hiện thị kẻ thù
    },
    Triggerbot = {
        Enabled = false,
        Delay = 0.1,                      -- thời gian chờ trước khi bắn
    },
    Misc = {
        NoRecoil = false,
        NoSpread = false,
        Speed = 16,                       -- tốc độ mặc định
        JumpPower = 50,
        SpeedEnabled = false,
        JumpEnabled = false,
    },
}

-- Biến lưu trữ mục tiêu hiện tại
local CurrentTarget = nil
local CurrentTargetPart = nil

-- Tạo GUI chính
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SniperArenaMenu"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Tạo Frame chính (có thể kéo thả)
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 400, 0, 500)
MainFrame.Position = UDim2.new(0.5, -200, 0.5, -250) -- căn giữa
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.BackgroundTransparency = 0.1
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

-- Làm bo góc
local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 10)
UICorner.Parent = MainFrame

-- Tiêu đề
local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(1, 0, 0, 40)
TitleLabel.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
TitleLabel.Text = "SNIPER ARENA - AIMBOT + ESP"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextSize = 20
TitleLabel.Parent = MainFrame
TitleLabel.ZIndex = 2
local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 10)
TitleCorner.Parent = TitleLabel

-- Nút đóng (X)
local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -35, 0, 5)
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.new(1, 1, 1)
CloseButton.Font = Enum.Font.GothamBold
CloseButton.TextSize = 20
CloseButton.Parent = TitleLabel
CloseButton.ZIndex = 3
local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 5)
CloseCorner.Parent = CloseButton
CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)

-- Tạo các tab
local TabButtonsFrame = Instance.new("Frame")
TabButtonsFrame.Size = UDim2.new(1, 0, 0, 40)
TabButtonsFrame.Position = UDim2.new(0, 0, 0, 40)
TabButtonsFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TabButtonsFrame.Parent = MainFrame

local AimbotTabButton = Instance.new("TextButton")
AimbotTabButton.Size = UDim2.new(0.33, -2, 1, 0)
AimbotTabButton.Position = UDim2.new(0, 0, 0, 0)
AimbotTabButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
AimbotTabButton.Text = "Aimbot"
AimbotTabButton.TextColor3 = Color3.new(1, 1, 1)
AimbotTabButton.Font = Enum.Font.GothamBold
AimbotTabButton.TextSize = 16
AimbotTabButton.Parent = TabButtonsFrame

local ESPTabButton = Instance.new("TextButton")
ESPTabButton.Size = UDim2.new(0.33, -2, 1, 0)
ESPTabButton.Position = UDim2.new(0.33, 2, 0, 0)
ESPTabButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
ESPTabButton.Text = "ESP"
ESPTabButton.TextColor3 = Color3.new(1, 1, 1)
ESPTabButton.Font = Enum.Font.GothamBold
ESPTabButton.TextSize = 16
ESPTabButton.Parent = TabButtonsFrame

local MiscTabButton = Instance.new("TextButton")
MiscTabButton.Size = UDim2.new(0.34, -2, 1, 0)
MiscTabButton.Position = UDim2.new(0.66, 4, 0, 0)
MiscTabButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
MiscTabButton.Text = "Misc"
MiscTabButton.TextColor3 = Color3.new(1, 1, 1)
MiscTabButton.Font = Enum.Font.GothamBold
MiscTabButton.TextSize = 16
MiscTabButton.Parent = TabButtonsFrame

-- Frame chứa nội dung các tab
local ContentFrame = Instance.new("ScrollingFrame")
ContentFrame.Size = UDim2.new(1, 0, 1, -80)
ContentFrame.Position = UDim2.new(0, 0, 0, 80)
ContentFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
ContentFrame.BorderSizePixel = 0
ContentFrame.ScrollBarThickness = 5
ContentFrame.CanvasSize = UDim2.new(0, 0, 1.5, 0) -- tạm thời, sẽ điều chỉnh sau
ContentFrame.Parent = MainFrame

-- Hàm tạo toggle (nút bật/tắt)
local function CreateToggle(parent, text, default, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -20, 0, 35)
    frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    frame.Parent = parent
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 5)
    corner.Parent = frame

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.7, -5, 1, 0)
    label.Position = UDim2.new(0, 10, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = text
    label.TextColor3 = Color3.new(1, 1, 1)
    label.Font = Enum.Font.Gotham
    label.TextSize = 16
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame

    local toggleBtn = Instance.new("TextButton")
    toggleBtn.Size = UDim2.new(0, 50, 0, 25)
    toggleBtn.Position = UDim2.new(1, -60, 0.5, -12.5)
    toggleBtn.BackgroundColor3 = default and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
    toggleBtn.Text = default and "ON" or "OFF"
    toggleBtn.TextColor3 = Color3.new(1, 1, 1)
    toggleBtn.Font = Enum.Font.GothamBold
    toggleBtn.TextSize = 14
    toggleBtn.Parent = frame
    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 5)
    btnCorner.Parent = toggleBtn

    local state = default
    toggleBtn.MouseButton1Click:Connect(function()
        state = not state
        toggleBtn.BackgroundColor3 = state and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
        toggleBtn.Text = state and "ON" or "OFF"
        callback(state)
    end)

    return frame
end

-- Hàm tạo dropdown (chọn lựa)
local function CreateDropdown(parent, text, options, default, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -20, 0, 35)
    frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    frame.Parent = parent
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 5)
    corner.Parent = frame

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.5, -5, 1, 0)
    label.Position = UDim2.new(0, 10, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = text
    label.TextColor3 = Color3.new(1, 1, 1)
    label.Font = Enum.Font.Gotham
    label.TextSize = 16
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame

    local dropdown = Instance.new("TextButton")
    dropdown.Size = UDim2.new(0, 100, 0, 25)
    dropdown.Position = UDim2.new(1, -110, 0.5, -12.5)
    dropdown.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    dropdown.Text = default
    dropdown.TextColor3 = Color3.new(1, 1, 1)
    dropdown.Font = Enum.Font.Gotham
    dropdown.TextSize = 14
    dropdown.Parent = frame
    local dropCorner = Instance.new("UICorner")
    dropCorner.CornerRadius = UDim.new(0, 5)
    dropCorner.Parent = dropdown

    local current = default
    dropdown.MouseButton1Click:Connect(function()
        -- chuyển qua lại giữa các options
        local idx = 1
        for i, opt in ipairs(options) do
            if opt == current then
                idx = i + 1
                break
            end
        end
        if idx > #options then idx = 1 end
        current = options[idx]
        dropdown.Text = current
        callback(current)
    end)

    return frame
end

-- Hàm tạo slider (điều chỉnh giá trị)
local function CreateSlider(parent, text, min, max, default, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -20, 0, 45)
    frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    frame.Parent = parent
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 5)
    corner.Parent = frame

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.5, -5, 0.5, 0)
    label.Position = UDim2.new(0, 10, 0, 2)
    label.BackgroundTransparency = 1
    label.Text = text
    label.TextColor3 = Color3.new(1, 1, 1)
    label.Font = Enum.Font.Gotham
    label.TextSize = 16
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame

    local valueLabel = Instance.new("TextLabel")
    valueLabel.Size = UDim2.new(0, 50, 0.5, 0)
    valueLabel.Position = UDim2.new(1, -60, 0, 2)
    valueLabel.BackgroundTransparency = 1
    valueLabel.Text = tostring(default)
    valueLabel.TextColor3 = Color3.fromRGB(0, 255, 255)
    valueLabel.Font = Enum.Font.GothamBold
    valueLabel.TextSize = 16
    valueLabel.Parent = frame

    local slider = Instance.new("TextButton")
    slider.Size = UDim2.new(0.9, -20, 0, 10)
    slider.Position = UDim2.new(0, 10, 0, 27)
    slider.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    slider.Text = ""
    slider.Parent = frame
    local sliderCorner = Instance.new("UICorner")
    sliderCorner.CornerRadius = UDim.new(0, 5)
    sliderCorner.Parent = slider

    local fill = Instance.new("Frame")
    fill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
    fill.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
    fill.BorderSizePixel = 0
    fill.Parent = slider
    local fillCorner = Instance.new("UICorner")
    fillCorner.CornerRadius = UDim.new(0, 5)
    fillCorner.Parent = fill

    local dragging = false
    slider.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
        end
    end)
    slider.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)

    RunService.RenderStepped:Connect(function()
        if dragging then
            local mousePos = UserInputService:GetMouseLocation()
            local absPos = slider.AbsolutePosition
            local absSize = slider.AbsoluteSize
            local relativeX = math.clamp(mousePos.X - absPos.X, 0, absSize.X)
            local percent = relativeX / absSize.X
            local value = math.floor((min + percent * (max - min)) * 10) / 10
            value = math.clamp(value, min, max)
            fill.Size = UDim2.new(percent, 0, 1, 0)
            valueLabel.Text = tostring(value)
            callback(value)
        end
    end)

    return frame
end

-- Xây dựng từng tab
local function BuildAimbotTab(parent)
    local layout = Instance.new("UIListLayout")
    layout.Padding = UDim.new(0, 5)
    layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Parent = parent

    CreateToggle(parent, "Enable Aimbot", Settings.Aimbot.Enabled, function(state)
        Settings.Aimbot.Enabled = state
    end)

    CreateDropdown(parent, "Target Part", {"Head", "Body"}, Settings.Aimbot.TargetPart, function(opt)
        Settings.Aimbot.TargetPart = (opt == "Head") and "Head" or "HumanoidRootPart"
    end)

    CreateSlider(parent, "Max Distance", 100, 1000, Settings.Aimbot.MaxDistance, function(val)
        Settings.Aimbot.MaxDistance = val
    end)

    CreateSlider(parent, "FOV Angle", 10, 180, Settings.Aimbot.FOV, function(val)
        Settings.Aimbot.FOV = val
    end)

    CreateToggle(parent, "Lock Target", Settings.Aimbot.Lock, function(state)
        Settings.Aimbot.Lock = state
    end)
end

local function BuildESPTab(parent)
    local layout = Instance.new("UIListLayout")
    layout.Padding = UDim.new(0, 5)
    layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Parent = parent

    CreateToggle(parent, "Enable ESP", Settings.ESP.Enabled, function(state)
        Settings.ESP.Enabled = state
        if not state then
            -- Xóa tất cả ESP hiện tại
            for _, v in ipairs(parent:GetChildren()) do
                if v:IsA("Folder") and v.Name == "ESP_Folder" then
                    v:Destroy()
                end
            end
        end
    end)

    CreateToggle(parent, "Show Box", Settings.ESP.Box, function(state)
        Settings.ESP.Box = state
    end)

    CreateToggle(parent, "Show Name", Settings.ESP.Name, function(state)
        Settings.ESP.Name = state
    end)

    CreateToggle(parent, "Show Health", Settings.ESP.Health, function(state)
        Settings.ESP.Health = state
    end)

    CreateToggle(parent, "Show Distance", Settings.ESP.Distance, function(state)
        Settings.ESP.Distance = state
    end)

    CreateToggle(parent, "Team Check", Settings.ESP.TeamCheck, function(state)
        Settings.ESP.TeamCheck = state
    end)
end

local function BuildMiscTab(parent)
    local layout = Instance.new("UIListLayout")
    layout.Padding = UDim.new(0, 5)
    layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Parent = parent

    CreateToggle(parent, "No Recoil", Settings.Misc.NoRecoil, function(state)
        Settings.Misc.NoRecoil = state
    end)

    CreateToggle(parent, "No Spread", Settings.Misc.NoSpread, function(state)
        Settings.Misc.NoSpread = state
    end)

    CreateToggle(parent, "Speed Hack", Settings.Misc.SpeedEnabled, function(state)
        Settings.Misc.SpeedEnabled = state
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            LocalPlayer.Character.Humanoid.WalkSpeed = state and Settings.Misc.Speed or 16
        end
    end)

    CreateSlider(parent, "Speed Value", 16, 200, Settings.Misc.Speed, function(val)
        Settings.Misc.Speed = val
        if Settings.Misc.SpeedEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            LocalPlayer.Character.Humanoid.WalkSpeed = val
        end
    end)

    CreateToggle(parent, "Jump Hack", Settings.Misc.JumpEnabled, function(state)
        Settings.Misc.JumpEnabled = state
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            LocalPlayer.Character.Humanoid.JumpPower = state and Settings.Misc.JumpPower or 50
        end
    end)

    CreateSlider(parent, "Jump Power", 50, 200, Settings.Misc.JumpPower, function(val)
        Settings.Misc.JumpPower = val
        if Settings.Misc.JumpEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            LocalPlayer.Character.Humanoid.JumpPower = val
        end
    end)

    CreateToggle(parent, "Triggerbot", Settings.Triggerbot.Enabled, function(state)
        Settings.Triggerbot.Enabled = state
    end)

    CreateSlider(parent, "Trigger Delay", 0, 0.5, Settings.Triggerbot.Delay, function(val)
        Settings.Triggerbot.Delay = val
    end)
end

-- Tạo nội dung cho từng tab và chức năng chuyển tab
local AimbotContent = Instance.new("Frame")
AimbotContent.Size = UDim2.new(1, -10, 1, -10)
AimbotContent.Position = UDim2.new(0, 5, 0, 5)
AimbotContent.BackgroundTransparency = 1
AimbotContent.Visible = true
AimbotContent.Parent = ContentFrame
BuildAimbotTab(AimbotContent)

local ESPContent = Instance.new("Frame")
ESPContent.Size = UDim2.new(1, -10, 1, -10)
ESPContent.Position = UDim2.new(0, 5, 0, 5)
ESPContent.BackgroundTransparency = 1
ESPContent.Visible = false
ESPContent.Parent = ContentFrame
BuildESPTab(ESPContent)

local MiscContent = Instance.new("Frame")
MiscContent.Size = UDim2.new(1, -10, 1, -10)
MiscContent.Position = UDim2.new(0, 5, 0, 5)
MiscContent.BackgroundTransparency = 1
MiscContent.Visible = false
MiscContent.Parent = ContentFrame
BuildMiscTab(MiscContent)

-- Xử lý chuyển tab
AimbotTabButton.MouseButton1Click:Connect(function()
    AimbotContent.Visible = true
    ESPContent.Visible = false
    MiscContent.Visible = false
    AimbotTabButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    ESPTabButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    MiscTabButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
end)

ESPTabButton.MouseButton1Click:Connect(function()
    AimbotContent.Visible = false
    ESPContent.Visible = true
    MiscContent.Visible = false
    AimbotTabButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    ESPTabButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    MiscTabButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
end)

MiscTabButton.MouseButton1Click:Connect(function()
    AimbotContent.Visible = false
    ESPContent.Visible = false
    MiscContent.Visible = true
    AimbotTabButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    ESPTabButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    MiscTabButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
end)

-- Cập nhật CanvasSize của ScrollingFrame dựa trên nội dung
local function UpdateCanvasSize(frame)
    local layout = frame:FindFirstChildOfClass("UIListLayout")
    if layout then
        local totalHeight = 0
        for _, child in ipairs(frame:GetChildren()) do
            if child:IsA("Frame") then
                totalHeight = totalHeight + child.AbsoluteSize.Y + layout.Padding.Offset
            end
        end
        frame.Parent.CanvasSize = UDim2.new(0, 0, 0, totalHeight + 10)
    end
end

-- Gọi UpdateCanvasSize sau khi xây dựng
task.wait(0.1)
UpdateCanvasSize(AimbotContent)
UpdateCanvasSize(ESPContent)
UpdateCanvasSize(MiscContent)

-- === CÁC CHỨC NĂNG CHÍNH === --

-- Hàm tìm kẻ địch gần nhất trong tầm nhìn
local function GetClosestTarget()
    local closestDistance = math.huge
    local closestTarget = nil
    local closestPart = nil
    local cameraPos = Camera.CFrame.Position
    local cameraDir = Camera.CFrame.LookVector

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
            -- Team check
            if Settings.ESP.TeamCheck and player.Team == LocalPlayer.Team then
                continue
            end

            local character = player.Character
            local targetPart = character:FindFirstChild(Settings.Aimbot.TargetPart) or character:FindFirstChild("HumanoidRootPart")
            if not targetPart then continue end

            local partPos = targetPart.Position
            local distance = (cameraPos - partPos).Magnitude
            if distance > Settings.Aimbot.MaxDistance then continue end

            -- Kiểm tra góc nhìn (FOV)
            local dirToTarget = (partPos - cameraPos).Unit
            local dot = cameraDir:Dot(dirToTarget)
            local angle = math.deg(math.acos(dot))
            if angle > Settings.Aimbot.FOV then continue end

            -- Kiểm tra xem có vật cản không (raycast)
            local raycastParams = RaycastParams.new()
            raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
            raycastParams.FilterDescendantsInstances = {LocalPlayer.Character, character}
            local rayResult = workspace:Raycast(cameraPos, (partPos - cameraPos).Unit * dis
