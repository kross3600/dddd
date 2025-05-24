local RunService = game:GetService("RunService")

local cueBall = workspace:WaitForChild("CueBall")
local maxDistance = 100
local lineParts = {}

-- Cria uma linha visual entre dois pontos
local function drawLine(from, to)
	local part = Instance.new("Part")
	part.Anchored = true
	part.CanCollide = false
	part.Material = Enum.Material.Neon
	part.BrickColor = BrickColor.new("Bright red")
	part.Transparency = 0.2
	part.Size = Vector3.new(0.1, 0.1, (from - to).Magnitude)
	part.CFrame = CFrame.new((from + to) / 2, to)
	part.Parent = workspace

	table.insert(lineParts, part)
end

-- Limpa linhas antigas
local function clearLines()
	for _, part in ipairs(lineParts) do
		if part and part.Parent then
			part:Destroy()
		end
	end
	lineParts = {}
end

-- Função para desenhar trajetória com 1 rebote
local function drawTrajectory()
	clearLines()

	local origin = cueBall.Position
	local direction = cueBall.CFrame.LookVector

	-- Primeiro raycast
	local result1 = workspace:Raycast(origin, direction * maxDistance)
	if result1 then
		drawLine(origin, result1.Position)

		-- Calcula a direção refletida
		local incoming = direction
		local normal = result1.Normal
		local reflected = incoming - 2 * incoming:Dot(normal) * normal

		-- Segundo raycast (rebote)
		local bounceOrigin = result1.Position + normal * 0.1
		local result2 = workspace:Raycast(bounceOrigin, reflected * maxDistance)
		if result2 then
			drawLine(bounceOrigin, result2.Position)
		else
			drawLine(bounceOrigin, bounceOrigin + reflected * 10)
		end
	else
		drawLine(origin, origin + direction * maxDistance)
	end
end

-- Atualiza a cada frame
RunService.RenderStepped:Connect(drawTrajectory)
# dddd
