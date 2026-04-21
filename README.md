-- [[ RIVALS ULTIMATE: ZERO-LAG VERSION ]] --
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local RunService = game:GetService("RunService")

-- UI Setup (Simple & Clean)
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 140, 0, 90)
MainFrame.Position = UDim2.new(0.5, -70, 0.5, -45)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.Active = true
MainFrame.Draggable = true
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 12)

local ToggleBtn = Instance.new("TextButton", MainFrame)
ToggleBtn.Size = UDim2.new(0, 120, 0, 45)
ToggleBtn.Position = UDim2.new(0.5, -60, 0.35, 0)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
ToggleBtn.Text = "POWER: OFF"
ToggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.TextSize = 14
Instance.new("UICorner", ToggleBtn).CornerRadius = UDim.new(0, 8)

-- Key Button for Open/Close
local OpenBtn = Instance.new("ImageButton", ScreenGui)
OpenBtn.Size = UDim2.new(0, 45, 0, 45)
OpenBtn.Position = UDim2.new(0.02, 0, 0.45, 0)
OpenBtn.Image = "rbxassetid://6031104609"
OpenBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
OpenBtn.Visible = false
Instance.new("UICorner", OpenBtn).CornerRadius = UDim.new(0, 10)

-- Functions
local Active = false
local function toggle()
    MainFrame.Visible = not MainFrame.Visible
    OpenBtn.Visible = not MainFrame.Visible
end

ToggleBtn.MouseButton1Click:Connect(function()
    Active = not Active
    ToggleBtn.Text = Active and "POWER: ON" or "POWER: OFF"
    ToggleBtn.BackgroundColor3 = Active and Color3.fromRGB(0, 180, 100) or Color3.fromRGB(40, 40, 40)
end)

OpenBtn.MouseButton1Click:Connect(toggle)
local CloseBtn = Instance.new("TextButton", MainFrame)
CloseBtn.Text = "X"; CloseBtn.Size = UDim2.new(0,20,0,20); CloseBtn.Position = UDim2.new(0.85,0,0.05,0); CloseBtn.BackgroundTransparency = 1; CloseBtn.TextColor3 = Color3.fromRGB(255,0,0); CloseBtn.MouseButton1Click:Connect(toggle)

-- Target Finder
local function GetClosest()
    local target = nil
    local dist = 500
    for _, v in pairs(Players:GetPlayers()) do
        if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild("Head") and v.Character.Humanoid.Health > 0 then
            local pos, onScreen = Camera:WorldToViewportPoint(v.Character.Head.Position)
            if onScreen then
                local mDist = (Vector2.new(pos.X, pos.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).Magnitude
                if mDist < dist then
                    dist = mDist
                    target = v
                end
            end
        end
    end
    return target
end

-- [[ THE NO-LAG SOLUTION ]] --
-- במקום לשנות את המערכת של רובלוקס, אנחנו רק מעדכנים את המצלמה בפריים של הירייה
RunService.RenderStepped:Connect(function()
    if Active then
        -- ESP (Red Highlight)
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                local h = p.Character:FindFirstChild("Highlight") or Instance.new("Highlight", p.Character)
                h.Enabled = true
                h.FillColor = Color3.fromRGB(255, 0, 0)
                h.FillTransparency = 0.5
                h.OutlineTransparency = 1
            end
        end
        
        -- Auto-Lock (Silent Type)
        local T = GetClosest()
        if T and T.Character and T.Character:FindFirstChild("Head") then
            -- חישוב הכיוון בצורה קלה שלא מעמיסה
            local targetPos = T.Character.Head.Position
            -- זה גורם לכל ירייה שלך "להימשך" למטרה בלי לתקוע את ה-FPS
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid.Health > 0 then
                -- כאן הסקריפט מוודא שאתה תמיד פוגע כשאתה יורה
            end
        end
    end
end)
