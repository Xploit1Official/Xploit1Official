
local function createOutline(part)
    local outline = Instance.new("SelectionBox")
    outline.LineThickness = 0.05
    outline.Color3 = Color3.new(1, 1, 0) -- Yellow outline color
    outline.Adornee = part
    outline.Parent = part
end

local function highlightPlayer(player)
    local character = player.Character
    if character then
        for _, part in ipairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                createOutline(part)
            end
        end
    end
end

local function removeHighlight(player)
    local character = player.Character
    if character then
        for _, part in ipairs(character:GetDescendants()) do
            if part:IsA("SelectionBox") then
                part:Destroy()
            end
        end
    end
end

-- Function to highlight all players in the game
local function highlightAllPlayers()
    for _, player in ipairs(game.Players:GetPlayers()) do
        highlightPlayer(player)
    end
end

-- Function to remove highlight from all players in the game
local function removeAllHighlights()
    for _, player in ipairs(game.Players:GetPlayers()) do
        removeHighlight(player)
    end
end

highlightAllPlayers()

-- Connect functions to player added/removed events to update highlights accordingly
game.Players.PlayerAdded:Connect(function(player)
    highlightPlayer(player)
end)

game.Players.PlayerRemoving:Connect(function(player)
    removeHighlight(player)
end)

local function raycastToPlayers()
    for _, player in ipairs(game.Players:GetPlayers()) do
        local character = player.Character
        if character then
            for _, part in ipairs(character:GetDescendants()) do
                if part:IsA("BasePart") then
                    local ray = Ray.new(part.Position, workspace.CurrentCamera.CFrame.Position - part.Position)
                    local hit, position = workspace:FindPartOnRay(ray, player.Character, false, true)
                    if hit then
                        if hit:IsDescendantOf(player.Character) then
                            part.Transparency = 0 -- Highlight is visible
                        else
                            part.Transparency = 0.5 -- Highlight is partially visible through walls
                        end
                    end
                end
            end
        end
    end
end

while true do
    raycastToPlayers()
    wait(1) -- Adjust the frequency based on your preference and performance considerations
end
