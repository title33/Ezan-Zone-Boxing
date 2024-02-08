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

local playersInServer = {} -- เก็บข้อมูลผู้เล่นทั้งหมดในเซิร์ฟเวอร์

-- ดึงข้อมูลผู้เล่นจากเซิร์ฟเวอร์
for _, player in pairs(game.Players:GetPlayers()) do
    table.insert(playersInServer, player.Name)
end

local MultiDropdown = Tabs.General:AddDropdown("Player", {
    Title = "Select Players",
    Description = "You can select players",
    Values = playersInServer, -- ให้ Values เป็นชื่อของผู้เล่นในเซิร์ฟเวอร์
    Multi = true,
    Default = {}, -- ตั้งค่า Default เป็นตัวเลือกที่ต้องการให้เริ่มต้น
})

MultiDropdown:OnChanged(function(Value)
    local Values = {}
    for Value, State in next, Value do
        table.insert(Values, Value)
    end
    print("Mutlidropdown changed:", table.concat(Values, ", "))
end)

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


local Toggle = Tabs.General:AddToggle("Bring  Players", {Title = "Bring Players", Default = false })

Toggle:OnChanged(function(on)
    if on then
        local selectedPlayers = MultiDropdown:GetValue() -- ดึงรายชื่อผู้เล่นที่ถูกเลือกใน MultiDropdown
        local offset = Slider:GetValue() -- ดึงค่าระยะห่างจาก Slider

        for _, playerName in ipairs(selectedPlayers) do
            local player = game.Players:FindFirstChild(playerName)
            if player then
                player.Character:SetPrimaryPartCFrame(game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0, offset, 0))
                offset = offset + 2
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
