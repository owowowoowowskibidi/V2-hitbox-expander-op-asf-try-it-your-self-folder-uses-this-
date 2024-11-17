local Players = game:GetService("Players")
local Lp = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

_G.HeadSize = 5.9
_G.Toggle = true

local MOVE_SPEED_MULTIPLIER = 1
local JUMP_POWER_MULTIPLIER = 8
local FALL_SPEED_MULTIPLIER = 8

local function adjustMovementSpeeds(character)
    local humanoid = character:FindFirstChild("Humanoid")
    if humanoid then
        humanoid.WalkSpeed = humanoid.WalkSpeed * MOVE_SPEED_MULTIPLIER
        humanoid.JumpPower = humanoid.JumpPower * JUMP_POWER_MULTIPLIER
    end
end

local function isBehindWall(player)
    local character = player.Character
    if character and character:FindFirstChild("HumanoidRootPart") then
        local camera = workspace.CurrentCamera
        local rootPart = character.HumanoidRootPart
        local ray = Ray.new(camera.CFrame.Position, (rootPart.Position - camera.CFrame.Position).unit * 1000)
        local hit, position = workspace:FindPartOnRayWithIgnoreList(ray, {camera, Lp.Character})
        return hit and not hit:IsDescendantOf(character)
    end
    return false
end

local function isToolOrWeapon(part)
    local parent = part.Parent
    if parent and parent:IsA("Tool") then
        return true
    end
    return false
end

local function applyHitbox(player)
    if player ~= Lp and player.Character then
        local ht = player.Character:FindFirstChild("HumanoidRootPart")
        if ht and not isToolOrWeapon(ht) then
            ht.Size = Vector3.new(_G.HeadSize, _G.HeadSize, _G.HeadSize)
            ht.Transparency = 1
            ht.CanCollide = _G.Toggle
            ht.Material = Enum.Material.Neon
            ht.BrickColor = BrickColor.new("Royal purple")
        end
    end
end

local function updateHitboxes()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= Lp and player.Character and not isBehindWall(player) then
            applyHitbox(player)
        end
    end
end

Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        wait(1)
        if not isBehindWall(player) then
            applyHitbox(player)
        end
    end)
end)

Lp.CharacterAdded:Connect(function(character)
    wait(1)
    adjustMovementSpeeds(character)
end)

for _, player in pairs(Players:GetPlayers()) do
    player.CharacterAdded:Connect(function()
        wait(1)
        if not isBehindWall(player) then
            applyHitbox(player)
        end
    end)
end

while wait(0.5) do
    updateHitboxes()
end
