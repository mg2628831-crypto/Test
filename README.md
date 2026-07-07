local player = game.Players.LocalPlayer
local camera = workspace.CurrentCamera

local function getClosestPlayer()
    local closestPlayer = nil
    local shortestDistance = math.huge
    
    for _, otherPlayer in pairs(game.Players:GetPlayers()) do
        if otherPlayer \~= player and otherPlayer.Character and otherPlayer.Character:FindFirstChild("Humanoid") and otherPlayer.Character.Humanoid.Health > 0 then
            local character = otherPlayer.Character
            local part = character:FindFirstChild(aimPart)
            
            if part then
                local screenPos, onScreen = camera:WorldToViewportPoint(part.Position)
                if onScreen then
                    local mousePos = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
                    local distance = (Vector2.new(screenPos.X, screenPos.Y) - mousePos).Magnitude
                    
                    if distance < shortestDistance then
                        shortestDistance = distance
                        closestPlayer = otherPlayer
                    end
                end
            end
        end
    end
    return closestPlayer
end

game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.E then
        aimbotEnabled = not aimbotEnabled
        print("Auto Mira: " .. (aimbotEnabled and "ATIVADO" or "DESATIVADO"))
    end
end)

game:GetService("RunService").RenderStepped:Connect(function()
    if not aimbotEnabled then return end
    
    local target = getClosestPlayer()
    if target and target.Character and target.Character:FindFirstChild(aimPart) then
        local targetPart = target.Character[aimPart]
        
        -- Mira suave na posição do alvo
        camera.CFrame = CFrame.lookAt(camera.CFrame.Position, targetPart.Position)
    end
end)
