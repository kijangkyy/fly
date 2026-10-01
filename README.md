local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")
local camera = workspace.CurrentCamera

local flying = false
local speed = 50

local bv = Instance.new("BodyVelocity")
bv.MaxForce = Vector3.new(1e5, 1e5, 1e5)
bv.Velocity = Vector3.new(0,0,0)

local bg = Instance.new("BodyGyro")
bg.MaxTorque = Vector3.new(1e5, 1e5, 1e5)
bg.D = 100

local function toggleFly()
    flying = not flying
    if flying then
        humanoid.PlatformStand = true
        bv.Parent = hrp
        bg.Parent = hrp
    else
        humanoid.PlatformStand = false
        bv.Parent = nil
        bg.Parent = nil
    end
end

UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if input.KeyCode == Enum.KeyCode.F then
        toggleFly()
    end
end)

RunService.RenderStepped:Connect(function()
    if not flying then return end
    local dir = Vector3.new(0,0,0)
    local cf = camera.CFrame

    if UserInputService:IsKeyDown(Enum.KeyCode.W) then dir += cf.LookVector end
    if UserInputService:IsKeyDown(Enum.KeyCode.S) then dir -= cf.LookVector end
    if UserInputService:IsKeyDown(Enum.KeyCode.A) then dir -= cf.RightVector end
    if UserInputService:IsKeyDown(Enum.KeyCode.D) then dir += cf.RightVector end
    if UserInputService:IsKeyDown(Enum.KeyCode.Space) then dir += Vector3.new(0,1,0) end
    if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then dir -= Vector3.new(0,1,0) end

    bv.Velocity = dir.Magnitude > 0 and dir.Unit * speed or Vector3.new(0,0,0)
    bg.CFrame = cf
end)
