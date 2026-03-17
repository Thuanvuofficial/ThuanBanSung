local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- 1. Tạo Cửa Sổ Menu
local Window = Rayfield:CreateWindow({
   Name = "Blox Fruits Pro Menu",
   LoadingTitle = "Đang tải Script...",
   LoadingSubtitle = "by Gemini AI",
   ConfigurationSaving = {
      Enabled = true,
      FolderName = "GeminiConfig", 
      FileName = "BloxFruits"
   }
})

-- 2. Tạo Tab Auto Farm
local MainTab = Window:CreateTab("Auto Farm", 4483362458) -- ID icon

-- 3. Biến điều khiển trạng thái
_G.AutoFarm = false

-- 4. Hàm xử lý Logic Auto Farm (Mẫu đơn giản)
function StartAutoFarm()
    task.spawn(function()
        while _G.AutoFarm do
            task.wait(0.1)
            -- Tìm quái (Ví dụ tìm quái gần nhất)
            pcall(function()
                for _, v in pairs(game.Workspace.Enemies:GetChildren()) do
                    if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
                        -- Bay đến quái vật (CFrame)
                        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = v.HumanoidRootPart.CFrame * CFrame.new(0, 5, 0)
                        
                        -- Tự động Click đánh
                        local VirtualUser = game:GetService("VirtualUser")
                        VirtualUser:CaptureController()
                        VirtualUser:ClickButton1(Vector2.new(0,0))
                        
                        if not _G.AutoFarm then break end
                    end
                end
            end)
        end
    end)
end

-- 5. Tạo Nút Bật/Tắt (Toggle) trong Menu
local Toggle = MainTab:CreateToggle({
   Name = "Bật Auto Farm",
   CurrentValue = false,
   Flag = "ToggleFarm", -- ID của nút này trong file lưu config
   Callback = function(Value)
      _G.AutoFarm = Value
      if Value then
          Rayfield:Notify({
             Title = "Thông báo",
             Content = "Đã Bật Auto Farm!",
             Duration = 3,
             Image = 4483362458,
          })
          StartAutoFarm()
      else
          Rayfield:Notify({
             Title = "Thông báo",
             Content = "Đã Tắt Auto Farm!",
             Duration = 3,
             Image = 4483362458,
          })
      end
   end,
})

-- Thông báo khi load xong
Rayfield:Notify({
   Title = "Thành công",
   Content = "Menu đã sẵn sàng!",
   Duration = 5,
   Image = 4483362458,
})
er.Humanoid.WalkSpeed = val
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
