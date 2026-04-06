local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
   Name = "恐鬼症",
   Icon = 0,
   LoadingTitle = "恐鬼症",
   LoadingSubtitle = "伊散缝合",
   Theme = "DarkBlue",

   DisableRayfieldPrompts = false,
   DisableBuildWarnings = false,

   KeySystem = true,
   KeySettings = {
      Title = "恐鬼症",
      Subtitle = "验证系统",
      Note = "卡密:yisan",
      FileName = "horror_fix",
      SaveKey = true,
      GrabKeyFromSite = false,
      Key = {"yisan"}
   }
})

-- 服务与常用变量
local Lighting = game:GetService("Lighting")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local WorkspaceService = game:GetService("Workspace")

-- 状态变量
local GhostLock = false -- 默认关闭
local WalkSpeedConnection = nil

-- 创建标签页
local Function = Window:CreateTab("功能", "book-check")

-- ====================
-- 证据部分
-- ====================
local Section = Function:CreateSection("证据")
local EMFCountLabel = Function:CreateParagraph({Title = "互动(电磁场读取)", Content = "出现次数: 0"})
local Thermometer = Function:CreateParagraph({Title = "冻结温度", Content = "获取中..."})
local Ouijabox = Function:CreateParagraph({Title = "精灵盒", Content = "需在鬼房使用道具"})

-- ====================
-- 玩家功能
-- ====================
local Section = Function:CreateSection("玩家")

-- 修复：穿门功能
-- 放在 "玩家" Section 下面
local NoclipLoop = nil
local NoclipToggle = Function:CreateToggle({
   Name = "穿墙(穿过一切)",
   CurrentValue = false,
   Flag = "NoclipAll",
   Callback = function(Value)
        if Value then
            -- 开启循环，每一帧都把角色碰撞关掉
            NoclipLoop = RunService.Stepped:Connect(function()
                if LocalPlayer.Character then
                    for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
                        if part:IsA("BasePart") and part.CanCollide == true then
                            part.CanCollide = false
                        end
                    end
                end
            end)
            Rayfield:Notify({Title = "穿墙", Content = "已开启，你可以穿过一切", Duration = 2})
        else
            -- 关闭循环
            if NoclipLoop then
                NoclipLoop:Disconnect()
                NoclipLoop = nil
            end
            Rayfield:Notify({Title = "穿墙", Content = "已关闭", Duration = 2})
        end
   end,
})
local Light = Function:CreateButton({
    Name = "夜视",
    Callback = function()
        Lighting.Brightness = 2
        Lighting.ClockTime = 14
        Lighting.FogEnd = 100000
        Lighting.GlobalShadows = false
        Lighting.OutdoorAmbient = Color3.fromRGB(128, 128, 128)
        if Lighting:FindFirstChild("Atmosphere") then
            Lighting.Atmosphere:Destroy()
        end
    end,
})

-- 修复：无限体力/速度
local SpeedPlayer = Function:CreateToggle({
   Name = "加速度",
   CurrentValue = false,
   Flag = "SpeedToggle",
   Callback = function(Value)
        if Value then
            -- 方法1: 尝试修改 Dead 值 (原脚本逻辑修复)
            if LocalPlayer.Character then
                local deadVal = LocalPlayer.Character:FindFirstChild("Dead")
                if deadVal then deadVal.Value = true end
            end
            
            -- 方法2: 强制锁定速度 (更通用的无限体力/速度表现)
            WalkSpeedConnection = RunService.RenderStepped:Connect(function()
                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
                    -- 这里的 16 是默认速度，如果想加速可以改成 20 或 25
                    if LocalPlayer.Character.Humanoid.WalkSpeed < 18 then 
                        LocalPlayer.Character.Humanoid.WalkSpeed = 18 
                    end
                end
            end)
            
            Rayfield:Notify({
               Title = "已开启",
               Content = "体力/速度已锁定",
               Duration = 3,
               Image = "check",
            })
        else
            -- 关闭时还原
            if WalkSpeedConnection then
                WalkSpeedConnection:Disconnect()
                WalkSpeedConnection = nil
            end
            
            if LocalPlayer.Character then
                local deadVal = LocalPlayer.Character:FindFirstChild("Dead")
                if deadVal then deadVal.Value = false end
                -- 重置速度
                if LocalPlayer.Character:FindFirstChild("Humanoid") then
                    LocalPlayer.Character.Humanoid.WalkSpeed = 16
                end
            end
        end
   end,
})

-- ====================
-- 透视部分 (ESP)
-- ====================
local Section = Function:CreateSection("透视")

-- 辅助函数：创建高亮
local function AddHighlight(parent, name, color)
    if not parent then return end
    local hl = parent:FindFirstChild(name)
    if not hl then
        hl = Instance.new("Highlight")
        hl.Name = name
        hl.FillTransparency = 1
        hl.OutlineColor = color
        hl.OutlineTransparency = 0
        hl.Parent = parent
    end
end

local function RemoveHighlight(parent, name)
    if not parent then return end
    local hl = parent:FindFirstChild(name)
    if hl then hl:Destroy() end
end

-- 修复：幽灵透视
local GhostToggle = Function:CreateToggle({
   Name = "幽灵 ESP",
   CurrentValue = false,
   Flag = "GhostESP",
   Callback = function(Value)
        GhostLock = Value
        if Value then
            task.spawn(function()
                while GhostLock and task.wait(1) do
                    -- 动态查找幽灵，覆盖整个 Workspace
                    for _, obj in ipairs(WorkspaceService:GetDescendants()) do
                        if obj.Name == "Ghost" and obj:IsA("Model") then
                            AddHighlight(obj, "GhostESP_Highlight", Color3.fromRGB(255, 0, 0))
                        end
                    end
                end
            end)
        else
            -- 关闭时清理
            for _, obj in ipairs(WorkspaceService:GetDescendants()) do
                if obj.Name == "Ghost" and obj:IsA("Model") then
                    RemoveHighlight(obj, "GhostESP_Highlight")
                end
            end
        end
   end,
})

-- 修复：互动显示
local EMFEnabled = false
local EMF = Function:CreateToggle({
   Name = "互动 ESP",
   CurrentValue = false,
   Flag = "EMFESP",
   Callback = function(Value)
        EMFEnabled = Value
        if not Value then
            -- 清理现有的
            for _, v in ipairs(WorkspaceService:GetDescendants()) do
                if v.Name == "EMFBillboardGui" then v:Destroy() end
            end
        end
   end,
})

-- 互动监听 (优化性能)
WorkspaceService.DescendantAdded:Connect(function(descendant)
    if descendant.Name == "EMFPart" and descendant:IsA("BasePart") then
        -- 更新计数
        local count = 0
        -- 这里只是个简化的计数，原逻辑不太严谨
        local EMFMerge = "检测到互动点" 
        EMFCountLabel:Set({Title = "互动(电磁场读取)", Content = EMFMerge})

        -- 如果开启了ESP则显示
        if EMFEnabled then
            local BillboardGui = Instance.new("BillboardGui")
            local TextLabel = Instance.new("TextLabel")
            BillboardGui.Name = "EMFBillboardGui"
                 BillboardGui.Parent = descendant
            BillboardGui.AlwaysOnTop = true
            BillboardGui.Size = UDim2.new(0, 40, 0, 20)
            TextLabel.Parent = BillboardGui
            TextLabel.Text = "互动"
            TextLabel.BackgroundTransparency = 1
            TextLabel.Size = UDim2.new(1, 0, 1, 0)
            TextLabel.TextColor3 = Color3.fromRGB(70, 255, 0)
            TextLabel.TextStrokeTransparency = 0
            TextLabel.TextSize = 12
        end
    end
end)

-- 修复：诅咒道具透视
local Cursed = Function:CreateToggle({
   Name = "诅咒道具 ESP",
   CurrentValue = false,
   Flag = "CursedESP",
   Callback = function(Value)
        local targetNames = {
            ["Ouija Board"] = true,
            ["SummoningCircle"] = true,
            ["Tarot Cards"] = true,
            ["VoodooDoll"] = true,
            ["Music Box"] = true
        }

        if Value then
            for _, v in ipairs(WorkspaceService:GetDescendants()) do
                if targetNames[v.Name] and (v:IsA("Model") or v:IsA("Tool")) then
                    AddHighlight(v, "CursedESP", Color3.fromRGB(255, 170, 127))
                end
            end
        else
            for _, v in ipairs(WorkspaceService:GetDescendants()) do
                if targetNames[v.Name] then
                    RemoveHighlight(v, "CursedESP")
                end
            end
        end
   end,
})

-- 修复：发电机透视
local Generators = Function:CreateToggle({
   Name = "发电机 ESP",
   CurrentValue = false,
   Flag = "GenESP",
   Callback = function(Value)
        -- 尝试多种可能的路径
        local targets = {}
        if WorkspaceService:FindFirstChild("Map") and WorkspaceService.Map:FindFirstChild("Generators") then
             table.insert(targets, WorkspaceService.Map.Generators)
        end
        -- 以前的逻辑是找 GeneratorMesh
        for _, v in ipairs(WorkspaceService:GetDescendants()) do
            if v.Name == "GeneratorMesh" or v.Name == "Generator" then
                if Value then
                    AddHighlight(v, "GeneratorsESP", Color3.fromRGB(255, 255, 127))
                else
                    RemoveHighlight(v, "GeneratorsESP")
                end
            end
        end
   end,
})

-- ====================
-- 恶搞与防护
-- ====================
local Section = Function:CreateSection("恶搞")
local Death = Function:CreateButton({
   Name = "自杀 (需清空手持道具)",
   Callback = function()
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            LocalPlayer.Character.Humanoid.Health = 0
        end
   end,
})

-- 玩家加入提示
local PlayerTab = Window:CreateTab("防巡查", "triangle-alert")
Players.PlayerAdded:Connect(function(player)
    PlayerTab:CreateParagraph({Title = "Name: " .. player.Name, Content = "ID: " .. player.UserId})
	Rayfield:Notify({
        Title = "警报",
        Content = "新玩家加入: " .. player.Name,
        Duration = 5,
        Image = "triangle-alert",
    })
end)

-- ====================
-- 自动检测循环 (温度/精灵盒)
-- ====================

-- 优化：精灵盒检测
WorkspaceService.DescendantAdded:Connect(function(child)
    if child:IsA("Sound") and child.Parent and child.Parent.Name == "Handle" then
        local tool = child.Parent.Parent
        if tool and tool.Name == "Spirit Box" then
             Ouijabox:Set({Title = "精灵盒", Content = "检测到声音: " .. child.Name})
             Rayfield:Notify({Title = "精灵盒", Content = "检测到回应", Duration = 3, Image = "check"})
        end
    end
end)

-- 优化：温度检测 (改为每秒检测一次，而不是死循环阻塞)
task.spawn(function()
    while task.wait(2) do
        local foundTemp = false
        for _, v in ipairs(WorkspaceService:GetDescendants()) do
            if v.Name == "_____Temperature" and v:IsA("ValueBase") then
                if v.Value < 0 then -- 稍微放宽条件
                    Thermometer:Set({Title = "冻结温度!", Content = tostring(v.Value)})
                    if not foundTemp then -- 防止重复刷屏
                        Rayfield:Notify({Title = "冻结温度", Content = "发现刺骨寒温!", Duration = 3, Image = "check"})
                    end
                    foundTemp = true
                end
            end
        end
    end
end)

Rayfield:LoadConfiguration()
