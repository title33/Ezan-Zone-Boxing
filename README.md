local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/title33/SaveManager/main/README.md"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Xylo Hub",
    SubTitle = "by Sky",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = true, -- The blur may be detectable, setting this to false disables blur entirely
    Theme = "Amethyst",
    MinimizeKey = Enum.KeyCode.LeftControl -- Used when theres no MinimizeKeybind
})

--Fluent provides Lucide Icons https://lucide.dev/icons/ for the tabs, icons are optional
local Tabs = {
    General = Window:AddTab({ Title = "General", Icon = "http://www.roblox.com/asset/?id=11254763826" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

local Options = Fluent.Options

local playersInServer = {}


    local Slider = Tabs.General:AddSlider("Distance", {
        Title = "Distance",
        Description = "Distance Player",
        Default = 2,
        Min = 0,
        Max = 5,
        Rounding = 1,
        Callback = function(Value)
            print("Slider was changed:", Value)
        end
    })

    Slider:OnChanged(function(Value)
        print("Slider changed:", Value)
    end)

    Slider:SetValue(3)

     -- Store all players' names

-- Get player names from the server
for _, player in pairs(game.Players:GetPlayers()) do
    table.insert(playersInServer, player.Name)
end

local MultiDropdown = Tabs.General:AddDropdown("Player", {
    Title = "Select Players",
    Description = "You can select players",
    Values = playersInServer, -- Use the list of player names
    Multi = true,
    Default = {}, -- Set the default selection to none
})

MultiDropdown:OnChanged(function(Value)
    local selectedPlayers = {}
    for Value, State in next, Value do
        if State then
            table.insert(selectedPlayers, Value)
        end
    end
    print("Selected players:", table.concat(selectedPlayers, ", "))
end)
 
    local Toggle = Tabs.General:AddToggle("Bring  Players", {Title = "Bring Players", Default = false })

    Toggle:OnChanged(function()
        print("Toggle changed:", Options.MyToggle.Value)
    end)

    Options.MyToggle:SetValue(false)





 


-- Addons:
-- SaveManager (Allows you to have a configuration system)
-- InterfaceManager (Allows you to have a interface managment system)

-- Hand the library over to our managers
SaveManager:SetLibrary(Fluent)
InterfaceManager:SetLibrary(Fluent)

-- Ignore keys that are used by ThemeManager.
-- (we dont want configs to save themes, do we?)
SaveManager:IgnoreThemeSettings()

-- You can add indexes of elements the save manager should ignore
SaveManager:SetIgnoreIndexes({})

-- use case for doing it this way:
-- a script hub could have themes in a global folder
-- and game configs in a separate folder per game
InterfaceManager:SetFolder("FluentScriptHub")
SaveManager:SetFolder("FluentScriptHub/specific-game")

InterfaceManager:BuildInterfaceSection(Tabs.Settings)
SaveManager:BuildConfigSection(Tabs.Settings)


Window:SelectTab(1)


-- You can use the SaveManager:LoadAutoloadConfig() to load a config
-- which has been marked to be one that auto loads!
SaveManager:LoadAutoloadConfig()
