-- Initialize players and their starting points
local players = {}
local startingPoints = {
    Vector3.new(0, 5, 0),
    Vector3.new(0, 5, -20),
    Vector3.new(0, 5, -40)
}

-- Function to handle player respawning
local function onPlayerRespawn(player)
    local startingPoint = startingPoints[math.random(1, #startingPoints)]
    player.Character:SetPrimaryPartCFrame(CFrame.new(startingPoint))
end

-- Function to handle player joining
game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        -- Respawn player when they die
        character:WaitForChild("Humanoid").Died:Connect(function()
            wait(2)
            onPlayerRespawn(player)
        end)
    end)
    
    -- Add player to the table
    table.insert(players, player)
    
    -- Teleport player to starting point
    onPlayerRespawn(player)
end)

-- Function to start the game
local function startGame()
    -- Teleport all players to their starting points
    for _, player in pairs(players) do
        onPlayerRespawn(player)
    end
end

-- Start the game when the server is ready
game:GetService("RunService").Stepped:Connect(function()
    if game.Players.NumPlayers > 0 then
        startGame()
        game:GetService("RunService").Stepped:Disconnect()
    end
end)
