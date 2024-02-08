local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/title33/SaveManager/main/README.md"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Xylo Hub",
    SubTitle = "by Sky",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = true,
    Theme = "Amethyst",
    MinimizeKey = Enum.KeyCode.LeftControl
})

local Tabs = {
    General = Window:AddTab({ Title = "General", Icon = "http://www.roblox.com/asset/?id=11254763826" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

local Options = Fluent.Options

local playersInServer = {}
for _, player in pairs(game.Players:GetPlayers()) do
    table.insert(playersInServer, player.Name)
end

local MultiDropdown = Tabs.General:AddDropdown("Player", {
    Title = "Select Players",
    Description = "You can select players",
    Values = playersInServer,
    Multi = true,
    Default = {},
})

local selectedPlayers = {}

MultiDropdown:OnChanged(function(Value)
    selectedPlayers = Value
    local Values = {}
    for _, PlayerName in ipairs(Value) do
        table.insert(Values, PlayerName)
    end
    print("MultiDropdown changed:", table.concat(Values, ", "))
end)

local Toggle = Tabs.General:AddToggle("Bring Players", { Title = "Bring Players", Default = false })

Toggle:OnChanged(function(on)
    if on then
        while wait() do
            for _, playerName in ipairs(selectedPlayers) do
                local player = game.Players:FindFirstChild(playerName)
                if player then
                    player.Character:SetPrimaryPartCFrame(game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0, -3, 0))
                end
            end
        end
    end
end)

Options.MyToggle:SetValue(false)

SaveManager:SetLibrary(Fluent)
InterfaceManager:SetLibrary(Fluent)
SaveManager:IgnoreThemeSettings()
SaveManager:SetIgnoreIndexes({})
InterfaceManager:SetFolder("FluentScriptHub")
SaveManager:SetFolder("FluentScriptHub/specific-game")
InterfaceManager:BuildInterfaceSection(Tabs.Settings)
SaveManager:BuildConfigSection(Tabs.Settings)
Window:SelectTab(1)
SaveManager:LoadAutoloadConfig()
