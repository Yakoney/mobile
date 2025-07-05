--[[
    @author Yakoney
    @description Grow a Garden auto-farm script
    @ui VisibilityUI
    https://www.roblox.com/games/126884695634066
]]

--// Services
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local InsertService = game:GetService("InsertService")
local MarketplaceService = game:GetService("MarketplaceService")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer
local Leaderstats = LocalPlayer:WaitForChild("leaderstats")
local Backpack = LocalPlayer:WaitForChild("Backpack")
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

local ShecklesCount = Leaderstats:WaitForChild("Sheckles")
local GameInfo = MarketplaceService:GetProductInfo(game.PlaceId)

--// ReGui
local ReGui = loadstring(game:HttpGet('https://raw.githubusercontent.com/depthso/Dear-ReGui/refs/heads/main/ReGui.lua'))()
local PrefabsId = "rbxassetid://" .. ReGui.PrefabsId

--// Folders
local GameEvents = ReplicatedStorage:WaitForChild("GameEvents")
local Farms = workspace:WaitForChild("Farm")

local Accent = {
    DarkGreen = Color3.fromRGB(45, 95, 25),
    Green = Color3.fromRGB(69, 142, 40),
    Brown = Color3.fromRGB(26, 20, 8),
}

--// ReGui configuration (Ui library)
ReGui:Init({
	Prefabs = InsertService:LoadLocalAsset(PrefabsId)
})
ReGui:DefineTheme("VisibilityUI", {
	WindowBg = Accent.Brown,
	TitleBarBg = Accent.DarkGreen,
	TitleBarBgActive = Accent.Green,
    ResizeGrab = Accent.DarkGreen,
    FrameBg = Accent.DarkGreen,
    FrameBgActive = Accent.Green,
	CollapsingHeaderBg = Accent.Green,
    ButtonsBg = Accent.Green,
    CheckMark = Accent.Green,
    SliderGrab = Accent.Green,
})

--// Dicts
local SeedStock = {}
local OwnedSeeds = {}
local HarvestIgnores = {
	Normal = false,
	Gold = false,
	Rainbow = false
}

--// Globals
local SelectedSeed, AutoPlantRandom, AutoPlant, AutoHarvest, AutoBuy, AutoSell, SellThreshold, NoClip, AutoWalkAllowRandom, AutoWalk, AutoWalkStatus, AutoWalkMaxWait, SelectedSeedStock

--// Functions for clearing and collecting
local function ClearTable(t)
	for k in pairs(t) do t[k] = nil end
end

local function CollectSeedsFromParent(Parent, Seeds)
	for _, Tool in ipairs(Parent:GetChildren()) do
		local NameVal = Tool:FindFirstChild("Plant_Name")
		local CountVal = Tool:FindFirstChild("Numbers")
		if NameVal and CountVal then
			Seeds[NameVal.Value] = {
				Count = CountVal.Value,
				Tool = Tool
			}
		end
	end
end

local function CollectCropsFromParent(Parent, Crops)
	for _, Tool in ipairs(Parent:GetChildren()) do
		if Tool:FindFirstChild("Item_String") then
			table.insert(Crops, Tool)
		end
	end
end

--// Fix GetOwnedSeeds and SeedStock clearing
local function GetOwnedSeeds()
	ClearTable(OwnedSeeds)
	local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
	CollectSeedsFromParent(Backpack, OwnedSeeds)
	CollectSeedsFromParent(Character, OwnedSeeds)
	return OwnedSeeds
end

local function GetSeedStock(IgnoreNoStock)
	ClearTable(SeedStock)
	local SeedShop = PlayerGui:WaitForChild("Seed_Shop")
	local Items = SeedShop:FindFirstChild("Blueberry", true).Parent
	local NewList = {}

	for _, Item in ipairs(Items:GetChildren()) do
		local MainFrame = Item:FindFirstChild("Main_Frame")
		if not MainFrame then continue end

		local StockText = MainFrame.Stock_Text.Text
		local StockCount = tonumber(StockText:match("%d+")) or 0
		SeedStock[Item.Name] = StockCount
		if IgnoreNoStock and StockCount > 0 then
			NewList[Item.Name] = StockCount
		end
	end

	return IgnoreNoStock and NewList or SeedStock
end

--// Replace UI Window Title
local function CreateWindow()
	local Window = ReGui:Window({
		Title = VisibilityUI | Yakoney,
        Theme = "VisibilityUI",
		Size = UDim2.fromOffset(300, 200)
	})
	return Window
end

--// Declare UI and logic below as in the original script, using fixed vars above
-- (Refer to the original script structure to continue building upon this)
-- Add remaining logic back using the now-corrected variables and structure
--
-- For brevity, only fixes and changed parts are shown here, but you can now proceed
-- to add all other modules (AutoPlantLoop, Harvest logic, Noclip, Walk logic, etc.)
-- with these fixed globals and updated UI theme/title.

-- StartServices() should be called at the end to begin all automation routines.
-- Attach your full original logic below this section to complete the script.

-- RunService and Backpack connections
RunService.Stepped:Connect(function()
	if NoClip and NoClip.Value then
		local Character = LocalPlayer.Character
		if Character then
			for _, Part in ipairs(Character:GetDescendants()) do
				if Part:IsA("BasePart") then
					Part.CanCollide = false
				end
			end
		end
	end
end)

Backpack.ChildAdded:Connect(function()
	if AutoSell and AutoSell.Value then
		local CropCount = #GetInvCrops()
		if CropCount >= SellThreshold.Value then
			SellInventory()
		end
	end
end)

-- Start all automation loops
StartServices()
