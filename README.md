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

local Toggle = Tabs.General:AddToggle("Bring Players", { Title = "Bring Players", Default = false })

Toggle:OnChanged(function(on)
  if on then
    local selectedPlayers = MultiDropdown:GetValue() -- ดึงรายชื่อผู้เล่นที่ถูกเลือกใน MultiDropdown
    local offset = 2 -- ระยะห่างระหว่างผู้เล่นกับผู้ใช้
    local playerPositions = {} -- เก็บตำแหน่งของผู้เล่น

    -- เก็บตำแหน่งของผู้เล่นก่อนเทเลพอร์ต
    for _, playerName in ipairs(selectedPlayers) do
      local player = game.Players:FindFirstChild(playerName)
      if player then
        table.insert(playerPositions, player.Character.HumanoidRootPart.CFrame)
      end
    end

    -- เทเลพอร์ตผู้เล่นไปยังตำแหน่งที่อยู่ข้างหน้าผู้ใช้ ห่าง 2 ตามจำนวนผู้เล่นที่เลือก
    for i, playerName in ipairs(selectedPlayers) do
      local player = game.Players:FindFirstChild(playerName)
      if player then
        player.Character:SetPrimaryPartCFrame(playerPositions[i] * CFrame.new(0, offset, 0))
        offset = offset + 2
      end
    end
  end
end)

Options.MyToggle:SetValue(false)

-- ส่วนที่เหลือของสคริปต์ไม่มีการเปลี่ยนแปลง

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
