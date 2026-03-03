--[[
    FULL AIMBOT + ESP MENU - SNIPER ARENA
    - Sử dụng CoreGui thay vì PlayerGui (tránh bị game xóa)
    - Thêm phím tắt RightCtrl để ẩn/hiện menu
    - Kiểm tra lỗi và log ra console
]]

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")  -- Dùng CoreGui an toàn hơn
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Kiểm tra nếu đã có GUI cũ thì xóa
local existingGui = CoreGui:FindFirstChild("SniperArenaMenu")
if existingGui then existingGui:Destroy() end

-- ===== CẤU HÌNH =====
local Settings = {
    Aimbot = {
        Enabled = false,
        TargetPart = "Head",           -- "Head" hoặc "HumanoidRootPart"
        MaxDistance = 500,
        FOV = 180,
        Lock = false,
    },
    ESP = {
        Enabled = false,
        Box = true,
        Name = true,
        Health = true,
        Distance = true,
        TeamCheck = false,
    },
    Triggerbot = {
        Enabled = false,
        Delay = 0.1,
    },
    Misc = {
        NoRecoil = false,
        NoSpread = false,
        Speed = 16,
        JumpPower = 50,
        SpeedEnabled = false,
        JumpEnabled = false,
    },
}

local CurrentTarget = nil
local CurrentTargetPart = nil

-- ===== TẠO GUI TRONG COREGUI =====
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SniperArenaMenu"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = CoreGui  -- Quan trọng: dùng CoreGui

-- Tạo Frame chính
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 400, 0, 500)
MainFrame.Position = UDim2.new(0.5, -200, 0.5, -250)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.BackgroundTransparency = 0.1
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

-- ... (phần còn lại của GUI giống như script cũ, nhưng nhớ thay đổi parent cho tất cả các thành phần là MainFrame, không dùng PlayerGui nữa) ...

-- Để tiết kiệm thời gian, bạn chỉ cần copy phần GUI từ script cũ và thay thế:
-- Tất cả các .Parent = something phải đảm bảo something là MainFrame hoặc ScreenGui.
-- Ở đây mình sẽ chỉ đưa ra phần thay đổi quan trọng:

-- Thay vì:
-- ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
-- thì đã sửa thành:
-- ScreenGui.Parent = CoreGui

-- Ngoài ra, để tránh bị game xóa, bạn có thể thêm một vòng lặp giữ GUI:
spawn(function()
    while ScreenGui and ScreenGui.Parent do
        -- Nếu GUI bị xóa, tạo lại
        if not ScreenGui:IsDescendantOf(CoreGui) then
            ScreenGui.Parent = CoreGui
        end
        task.wait(1)
    end
end)

-- Thêm chức năng ẩn/hiện menu bằng phím (RightControl)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.RightControl then
        MainFrame.Visible = not MainFrame.Visible
    end
end)

-- Thông báo khi script load
print("✅ Script Sniper Arena đã được tải! Nhấn RightCtrl để ẩn/hiện menu.")

-- ===== CÁC CHỨC NĂNG CHÍNH (giữ nguyên) =====
-- Copy toàn bộ phần xử lý Aimbot, ESP, Triggerbot từ script cũ và dán vào đây.
-- Đảm bảo không có lỗi cú pháp.

-- Lưu ý: Trong phần ESP, bạn cần thay đổi Parent của ESPFolder từ ScreenGui sang một thư mục con trong CoreGui.
local ESPFolder = Instance.new("Folder")
ESPFolder.Name = "ESP_Folder"
ESPFolder.Parent = ScreenGui  -- Vẫn dùng ScreenGui vì nó nằm trong CoreGui

-- ... (các hàm CreateESP, GetClosestTarget, v.v. giữ nguyên) ...
lider

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
