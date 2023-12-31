repeat wait()
until game:FindFirstChild("CoreGui")
        and game.Players.LocalPlayer

repeat wait(.25)
until game:IsLoaded()
        and game.Players.LocalPlayer.Character



local plr = game:GetService('Players').LocalPlayer
if game.GameId ~= 994732206 then
    warn('day k phai la blocc trai cay')
    return
end

repeat wait()
until plr:FindFirstChild("Backpack")
        and plr:FindFirstChild("DataLoaded")

repeat wait(1) until game:GetService('Players').LocalPlayer.Character
repeat wait(1) until game:GetService('Players').LocalPlayer.Character:FindFirstChild('HumanoidRootPart')
repeat wait(1) until game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild('Main')


local CommF = game.ReplicatedStorage.Remotes.CommF_
local race = " V1"
if game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("CommF_"):InvokeServer(unpack({[1] = "Alchemist",[2] = "1"})) == -2 then
    race = " V2"
end
if game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("CommF_"):InvokeServer(unpack({[1] = "Wenlocktoad",[2] = "1"})) == -2 then
    race = " V3"
end
if plr.Backpack:FindFirstChild("Awakening") or plr.Character:FindFirstChild("Awakening") then
    race = " V4"
end

local url = 'https://checkacc.braventran.repl.co/layfile'

local Request = (syn and syn.request) or (http and http.request) or http_request or (fluxus and fluxus.request) or request

local function filterInventory(inventory, itemList)
  local filteredInventory = {}
  for _, item in ipairs(inventory) do
    if table.find(itemList, item.Name) then
      table.insert(filteredInventory, item)
    end
  end
  return filteredInventory
end

local function getSkillName(skill)
  local skillNames = {
    Godhuman = "God",
    Superhuman = "SH",
    DragonTalon = "DT",
    ElectricClaw = "EC",
    SharkmanKarate = "SK",
    DeathStep = "DS"
  }
  return skillNames[skill] or skill
end
function getAwakenedAbilities()
    local awakenedAbilities = {}

    pcall(function()
        for i, v in pairs(CommF:InvokeServer("getAwakenedAbilities")) do
            if v.Awakened then
                table.insert(awakenedAbilities, i)
            end
        end
        warn('awakenedAbilities')
    end)

    return awakenedAbilities
end

function postData()
  local player = game.Players.LocalPlayer
  local key = getgenv().DCTSetting.key or getgenv().Setting.key
  local note = getgenv().DCTSetting.note or getgenv().Setting.note
  repeat wait() until player.Character and player.Character:FindFirstChild('HumanoidRootPart')
  repeat wait() until player.PlayerGui:FindFirstChild('Main')
  repeat wait() until game:FindFirstChild("CoreGui") and player

  while wait(1) do
    local username = player.Name
    local level = player.Data.Level.Value
    local beli = player.Data.Beli.Value
    local fragments = player.Data.Fragments.Value
    local DevilFruit = player.Data.DevilFruit.Value
    local skills = {}
    local inventory = game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("CommF_"):InvokeServer("getInventory")

    for _, skill in ipairs({ "Superhuman","ElectricClaw","DragonTalon","SharkmanKarate","DeathStep","Godhuman" }) do
      if game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("CommF_"):InvokeServer("Buy" .. string.gsub(skill, " ", ""), true) == 1 then
        table.insert(skills, getSkillName(skill))
      end
    end
    
    local weaponsList = { "Mirror Fractal", "Soul Guitar", "Valkyrie Helm", "Cursed Dual Katana" ,"Dark Fagment" }
    local fruitsList = { "Dough-Dough", "Mammoth-Mammoth", "Leopard", "Dragon-Dragon", "Mammoth-Mammoth", "Kitsune-Kitsune", "T-T" }

    local filteredWeaponsInventory = filterInventory(inventory, weaponsList)
    local filteredFruitInventory = filterInventory(inventory, fruitsList)
    local filteredDarkFragmentInventory = filterInventory(inventory, {"Dark Fragment"})
    local numDarkFragments = #filteredDarkFragmentInventory -- Đếm số Dark Fragment trong inventory
    

    local fruitName = filteredFruitInventory[1] and filteredFruitInventory[1].Name or "None" local darkFragmentName = filteredDarkFragmentInventory[1] and filteredDarkFragmentInventory[1].Name or "None"
    local materialName = ""
    if filteredWeaponsInventory[1] then materialName = "Mirror Fractal" end
    if filteredWeaponsInventory[2] then materialName = materialName == "" and "Soul Guitar" or materialName .. ", Soul Guitar" end
    if filteredWeaponsInventory[3] then materialName = materialName == "" and "Valkyrie Helm" or materialName .. ", Valkyrie Helm" end
    if filteredWeaponsInventory[4] then materialName = materialName == "" and "Cursed Dual Katana" or materialName .. ", Cursed Dual Katana" end
    if filteredWeaponsInventory[5] then materialName = materialName == "" and "Dark Fragment " or materialName .. ", Dark Fragment " end
    if materialName == "" then materialName = "None" end

    local placeName = game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name
    local awakenedAbilities = getAwakenedAbilities()

    local pvData = {
        key = key,
        note = note,
        userName = username,
        Level = level,
        Beli = beli,
        Fragments = fragments,
        Skills = skills,
        InventoryFruit = fruitName,
        InventoryItem = materialName,
        PlaceName= placeName,
        Race = race,
        DarkFragment = darkFragmentName,
        DarkFragmentsCount = numDarkFragments,
        AwakenedAbilities = awakenedAbilities,
        DevilFruit = DevilFruit
      }

    local responses = Request({
      Url = url,
      Method = "POST",
      Headers = {
        ["Content-Type"] = "application/json"
      },
      Body = game:GetService("HttpService"):JSONEncode(pvData)
    })

    print(responses.Body)
  end
end

postData()
