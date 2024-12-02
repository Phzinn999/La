local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Fluent " .. Fluent.Version,
    SubTitle = "by dawid",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = true,
    Theme = "Dark",
    MinimizeKey = Enum.KeyCode.LeftControl
})

local Tabs = {
    Main = Window:AddTab({ Title = "Main", Icon = "" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

Tabs.Main:AddButton({
    Title = "Clique-me",
    Callback = function()
        print("Botão clicado!")
    end
})

local Slider = Tabs.Main:AddSlider("Volume", {
    Title = "Ajuste o Volume",
    Min = 0, Max = 100, Default = 50
})

Slider:OnChanged(function(Value)
    print("Volume ajustado para:", Value)
end)

-- Adicionando o Slider para Speed Hack
local SpeedSlider = Tabs.Main:AddSlider("Speed", {
    Title = "Ajuste a Velocidade",
    Min = 16, Max = 200, Default = 16
})

-- Adicionando o botão para aplicar a velocidade
Tabs.Main:AddButton({
    Title = "Aplicar Velocidade",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player and player.Character and player.Character:FindFirstChild("Humanoid") then
            player.Character.Humanoid.WalkSpeed = SpeedSlider.Value
            print("Velocidade ajustada para:", SpeedSlider.Value)
        end
    end
})

-- Função para adicionar ESP
local function addESP(player, color)
    local highlight = Instance.new("Highlight")
    highlight.Name = "ESP"
    highlight.FillColor = color
    highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
    highlight.FillTransparency = 0.5
    highlight.OutlineTransparency = 0
    highlight.Parent = player.Character
end

-- Adicionando ESP para jogadores e assassino
for _, player in pairs(game.Players:GetPlayers()) do
    if player ~= game.Players.LocalPlayer then
        player.CharacterAdded:Connect(function(character)
            if player.Name == "NomeDoAssassino" then
                addESP(player, Color3.fromRGB(255, 0, 0)) -- Vermelho para o assassino
            else
                addESP(player, Color3.fromRGB(0, 255, 0)) -- Verde para os jogadores
            end
        end)
    end
end

-- Função para coletar itens raros
local function collectRareItems()
    for _, item in pairs(workspace:GetDescendants()) do
        if item:IsA("Tool") and item:FindFirstChild("Rarity") and item.Rarity.Value == "Rare" then
            item.Handle.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame
        end
    end
    print("Itens raros coletados!")
end

-- Adicionando o botão para coletar itens raros
Tabs.Main:AddButton({
    Title = "Coletar Itens Raros",
    Callback = function()
        collectRareItems()
    end
})

local Keybind = Tabs.Main:AddKeybind("Keybind", {
    Title = "Keybind",
    Default = Enum.KeyCode.E,
    Mode = "Toggle",
    Callback = function(Value)
        print("Keybind clicked!", Value)
    end,
    ChangedCallback = function(New)
        print("Keybind changed!", New)
    end
})

Keybind:OnClick(function()
    print("Keybind clicked:", Keybind:GetState())
end)

Keybind:OnChanged(function()
    print("Keybind changed:", Keybind.Value)
end)

task.spawn(function()
    while true do
        wait(1)
        local state = Keybind:GetState()
        if state then
            print("Keybind is being held down")
        end
        if Fluent.Unloaded then break end
    end
end)

Keybind:SetValue("MB2", "Toggle")

local Input = Tabs.Main:AddInput("Input", {
    Title = "Input",
    Default = "Default",
    Placeholder = "Placeholder",
    Numeric = false,
    Finished = false,
    Callback = function(Value)
        print("Input changed:", Value)
    end
})

Input:OnChanged(function()
    print("Input updated:", Input.Value)
end)

SaveManager:SetLibrary(Fluent)
InterfaceManager:SetLibrary(Fluent)

SaveManager:IgnoreThemeSettings()
SaveManager:SetIgnoreIndexes({})
InterfaceManager:SetFolder("FluentScriptHub")
SaveManager:SetFolder("FluentScriptHub/specific-game")

InterfaceManager:BuildInterfaceSection(Tabs.Settings)
SaveManager:BuildConfigSection(Tabs.Settings)

Window:SelectTab(1)

Fluent:Notify({
    Title = "Fluent",
    Content = "The script has been loaded.",
    Duration = 8
})

SaveManager:LoadAutoloadConfig()
