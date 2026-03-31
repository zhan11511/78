-- 超级盒子防御 Delta 专用 · 无UI 防封全家桶
getgenv().Fly = false
getgenv().FlySpeed = 45
getgenv().God = false
getgenv().Speed = 28
getgenv().AutoFarm = false
getgenv().AutoCollect = true
getgenv().AntiBan = true

-- Delta 防检测
local VirtualUser = game:GetService("VirtualUser")
game.Players.LocalPlayer.Idled:Connect(function()
    if AntiBan then
        VirtualUser:CaptureController()
        VirtualUser:ClickButton2(Vector2.new())
    end
end)

-- 飞行+防飞出地图
game:GetService("RunService").Heartbeat:Connect(function()
    local plr = game.Players.LocalPlayer
    local char = plr.Character
    if not char then return end
    local root = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChild("Humanoid")

    -- 无敌
    if God and hum then
        hum.Health = 9999
        hum.MaxHealth = 9999
    end

    -- 速度
    hum.WalkSpeed = Speed

    -- 飞行
    if Fly and root then
        hum.GravityScale = 0.15
        hum.PlatformStand = true
        local cam = workspace.CurrentCamera
        local uis = game:GetService("UserInputService")
        local dir = Vector3.new(0,0,0)

        if uis:IsKeyDown(Enum.KeyCode.W) then dir = dir + cam.CFrame.LookVector end
        if uis:IsKeyDown(Enum.KeyCode.S) then dir = dir - cam.CFrame.LookVector end
        if uis:IsKeyDown(Enum.KeyCode.A) then dir = dir - cam.CFrame.RightVector end
        if uis:IsKeyDown(Enum.KeyCode.D) then dir = dir + cam.CFrame.RightVector end
        if uis:IsKeyDown(Enum.KeyCode.Space) then dir = dir + Vector3.new(0,1,0) end
        if uis:IsKeyDown(Enum.KeyCode.LeftControl) then dir = dir - Vector3.new(0,1,0) end

        if dir.Magnitude > 0 then
            root.Velocity = dir.Unit * FlySpeed
        end

        -- 防飞出地图（Delta 专属边界）
        root.Position = Vector3.new(
            math.clamp(root.Position.X, -300, 300),
            math.clamp(root.Position.Y, -100, 300),
            math.clamp(root.Position.Z, -300, 300)
        )
    else
        if hum then
            hum.GravityScale = 1
            hum.PlatformStand = false
        end
    end
end)

-- 自动捡点
game.Workspace.ChildAdded:Connect(function(obj)
    if AutoCollect and (obj.Name == "Point" or obj.Name == "Coin") then
        pcall(function()
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = obj.CFrame
        end)
    end
end)

-- 自动刷怪
task.spawn(function()
    while task.wait(0.3) do
        if not AutoFarm then continue end
        pcall(function()
            local char = game.Players.LocalPlayer.Character
            local enemies = game.Workspace:FindFirstChild("Enemies")
            if enemies and char then
                local root = char.HumanoidRootPart
                for _,v in pairs(enemies:GetChildren()) do
                    if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
                        root.CFrame = v.HumanoidRootPart.CFrame * CFrame.new(0,0,-3)
                        if char:FindFirstChildOfClass("Tool") then
                            char:FindFirstChildOfClass("Tool"):Activate()
                        end
                    end
                end
            end
        end)
    end
end)

-- Delta 手机快捷指令
print("==== Delta 专用指令 ====")
print("getgenv().Fly = true — 飞行开")
print("getgenv().Fly = false — 飞行关")
print("getgenv().God = true — 无敌开")
print("getgenv().AutoFarm = true — 自动刷怪开")
warn("✅ Delta 脚本加载成功！")
