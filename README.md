------- leaked by lenaki#0001 and Splitta aka Shitta

if not game:IsLoaded() then
	game.Loaded:Wait()
end


getgenv().SplittaW = { 
    Silent = {
        Enabled     = true,
        Part        = "Head", "LowerTorso", "HumanoidRootPart", "RightUpperArm", "LeftUpperArm", "RightLowerLeg", "LeftLowerLeg",
        ClosestPart = true,
        
		PredictMovement    = true,
        PredictionVelocity = 0.132,
        
        AntiGroundShots = true,
        
        WallCheck   = true,
        CheckIf_KO  = true,

		UseKeybind = true,
		Keybind    = "Z",
	},
    SilentFOV = {
        Visible = false,
        Radius  = 3,
    },
    Tracer = {
        Key =   "c",
        Enabled = true,
        Part    = "Torso",
        ClosestPart = false,
        
		DisableTargetDeath = true,
		DisableLocalDeath  = true,
        
        UseCircleRadius      = false,
        DisableOutSideCircle = false,
        
		UseShake   = true,
		ShakeValue = 0,

		PredictMovement    = true,
        PredictionVelocity = 0.185,
        
        WallCheck  = true,
        CheckIf_KO = false,

        Smoothness = 0.05,
    },
    TracerFOV = {
        Visible = false,
        Radius  = 500,
    },
    GunFOV =  {
        Enabled = true,-- // Gun Fov / fov is automatically changed to specific gun equipped
        ["Double-Barrel SG"] = {["FOV"] = 24}, -- DB
        ["Revolver"] = {["FOV"] = 24}, -- rev
        ["SMG"] = {["FOV"] = 23}, -- smg
        ["Shotgun"] = { ["FOV"] = 24}, -- shotgun
        ["Rifle"] = { ["FOV"] = 24}, -- Rifle
        ["TacticalShotgun"] = {["FOV"] = 24}, -- rev
        ["Silencer"] = {["FOV"] = 24}, -- smg
        ["AK47"] = { ["FOV"] = 24}, -- shotgun
        ["AR"] = { ["FOV"] = 24}, -- Rifle
        -- // You Can Add Custom :p
	},
	
	Both = {
		DetectDesync    = true,
		DesyncDetection = 80,
		
		UseUnderGroundKeybind = true,
		UnderGroundKey        = "X",
		
        UnderGroundReolver    = false,
		
		UsDetectDesyncKeybind = true,
		DetectDesyncKey       = "V",
		
		SendNotification      = true,
	},
}

local Prey = nil
local Plr  = nil

local Players, Client, Mouse, RS, Camera =
    game:GetService("Players"),
    game:GetService("Players").LocalPlayer,
    game:GetService("Players").LocalPlayer:GetMouse(),
    game:GetService("RunService"),
    game:GetService("Workspace").CurrentCamera

local Circle       = Drawing.new("Circle")
local TracerCircle = Drawing.new("Circle")

Circle.Color           = Color3.new(1,1,1)
Circle.Thickness       = 1
TracerCircle.Color     = Color3.new(1,1,1)
TracerCircle.Thickness = 1

local UpdateFOV = function ()
    if (not Circle and not TracerCircle) then
        return Circle and TracerCircle
    end
    TracerCircle.Visible  = getgenv().SplittaW.TracerFOV.Visible
    TracerCircle.Radius   = getgenv().SplittaW.TracerFOV.Radius * 3
    TracerCircle.Position = Vector2.new(Mouse.X, Mouse.Y + (game:GetService("GuiService"):GetGuiInset().Y))
    
    Circle.Visible  = getgenv().SplittaW.SilentFOV.Visible
    Circle.Radius   = getgenv().SplittaW.SilentFOV.Radius * 3
    Circle.Position = Vector2.new(Mouse.X, Mouse.Y + (game:GetService("GuiService"):GetGuiInset().Y))
    return Circle and TracerCircle
end

RS.Heartbeat:Connect(UpdateFOV)

local WallCheck = function(destination, ignore)
    local Origin    = Camera.CFrame.p
    local CheckRay  = Ray.new(Origin, destination - Origin)
    local Hit       = game.workspace:FindPartOnRayWithIgnoreList(CheckRay, ignore)
    return Hit      == nil
end

local WTS = function (Object)
    local ObjectVector = Camera:WorldToScreenPoint(Object.Position)
    return Vector2.new(ObjectVector.X, ObjectVector.Y)
end

local IsOnScreen = function (Object)
    local IsOnScreen = Camera:WorldToScreenPoint(Object.Position)
    return IsOnScreen
end

local FilterObjs = function (Object)
    if string.find(Object.Name, "Gun") then
        return
    end
    if table.find({"Part", "MeshPart", "BasePart"}, Object.ClassName) then
        return true
    end
end

local ClosestPlrFromMouse = function()
    local Target, Closest = nil, 1/0
    
    for _ ,v in pairs(Players:GetPlayers()) do
    	if getgenv().SplittaW.Silent.WallCheck then
    		if (v.Character and v ~= Client and v.Character:FindFirstChild("HumanoidRootPart")) then
    			local Position, OnScreen = Camera:WorldToScreenPoint(v.Character.HumanoidRootPart.Position)
    			local Distance = (Vector2.new(Position.X, Position.Y) - Vector2.new(Mouse.X, Mouse.Y)).Magnitude
    
    			if (Circle.Radius > Distance and Distance < Closest and OnScreen) and WallCheck(v.Character.HumanoidRootPart.Position, {Client, v.Character}) then
    				Closest = Distance
    				Target = v
    			end
    		end
    	else
    		if (v.Character and v ~= Client and v.Character:FindFirstChild("HumanoidRootPart")) then
    			local Position, OnScreen = Camera:WorldToScreenPoint(v.Character.HumanoidRootPart.Position)
    			local Distance = (Vector2.new(Position.X, Position.Y) - Vector2.new(Mouse.X, Mouse.Y)).Magnitude
    
    
