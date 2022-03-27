local m = {}
local runServ = game:GetService("RunService")
local uis = game:GetService("UserInputService")

function m:GetMouse()
	local events = {}
	local b = Instance.new("BindableEvent",script)

	b.Event:Connect(function(n, ...)
		local f = events[n]	
		if f and f[1] then 
			for i,v in pairs(f[1])do 
				v(...)
			end
		end
	end)

	local function event(n)
		events[n] = {{}, 0, {}}

		local event = {
			Connect = function(self,f)
				table.insert(events[n][1], f)
				local o = {}
				function o:Disconnect()
					events[n] = nil
				end
				return o
			end,
			Wait = function()
				local c = events[n][2]
				repeat task.wait() until events[n][2] ~= c
				return unpack(events[n][3])
			end,
		}
		event.connect = event.Connect
		event.wait = event.Wait
		return event
	end

	local function fire(n, ...)
		events[n][2] += 1
		events[n][3] = {...}
		b:Fire(n, ...)
	end
	local s = setmetatable({},{__index = self})
	s.Hit = CFrame.new()
	s.Target = nil
	s.ViewSizeX = 0
	s.ViewSizeY = 0
	s.Origin = CFrame.new()
	s.TargetFilter = {}
	s.TargetSurface = Enum.NormalId.Top
	s.UnitRay = nil
	s.X = 0
	s.Y = 0

	s.KeyDown = event("KeyDown")
	s.KeyUp = event("KeyUp")
	s.Button1Down = event("Button1Down")
	s.Button1Up = event("Button1Up")
	s.Button2Down = event("Button2Down")
	s.Button2Up = event("Button2Up")
	s.Move = event("Move")
	s.Idle = event("Idle")
	s.WheelBackward = event("WheelBackward")
	s.WheelForward = event("WheelForward")

	s.Icon = ""

	local params = RaycastParams.new()
	params.IgnoreWater = true

	local last = uis:GetMouseLocation()
	local normal = {
		[Vector3.new(0,1,0)] = Enum.NormalId.Top,
		[Vector3.new(0,-1,0)] = Enum.NormalId.Bottom,
		[Vector3.new(1,0,0)] = Enum.NormalId.Right,
		[Vector3.new(-1,0,0)] = Enum.NormalId.Left,
		[Vector3.new(0,0,1)] = Enum.NormalId.Back,
		[Vector3.new(0,0,-1)] = Enum.NormalId.Front,

	}
	local idle = false

	uis.InputBegan:Connect(function(input, gp)
		if not gp then 
			if input.KeyCode ~= Enum.KeyCode.Unknown then
				local k = string.lower(string.split(tostring(input.KeyCode),".")[3])
				fire("KeyDown",k)
			end
			if input.UserInputType == Enum.UserInputType.MouseButton1 then
				fire("Button1Down")
			end
			if input.UserInputType == Enum.UserInputType.MouseButton2 then
				fire("Button2Down")
			end
		end
	end)

	uis.InputEnded:Connect(function(input, gp)
		if not gp then 
			if input.KeyCode ~= Enum.KeyCode.Unknown then
				local k = string.lower(string.split(tostring(input.KeyCode),".")[3])
				fire("KeyUp",k)
			end
			if input.UserInputType == Enum.UserInputType.MouseButton1 then
				fire("Button1Up")
			end
			if input.UserInputType == Enum.UserInputType.MouseButton2 then
				fire("Button2Up")
			end
		end
	end)

	uis.InputChanged:Connect(function(inp, gp)
		if not gp then 
			if inp.UserInputType == Enum.UserInputType.MouseMovement then 
				--idle = false 
				--fire("Move")
			end
			if inp.UserInputType == Enum.UserInputType.MouseWheel then
				if inp.Position.Z > 0 then 
					fire("WheelForward")
				else 
					fire("WheelBackward")
				end
			end
		end
	end)

	runServ.RenderStepped:Connect(function()
		local p = uis:GetMouseLocation()
		s.X = p 
		s.Y = p
		s.UnitRay = workspace.CurrentCamera:ViewportPointToRay(p.X, p.Y)
		s.ViewSizeX = workspace.CurrentCamera.ViewportSize.X
		s.ViewSizeY = workspace.CurrentCamera.ViewportSize.Y
		params.FilterDescendantsInstances = s.TargetFilter
		s.Origin = CFrame.new(workspace.CurrentCamera.CFrame.Position, s.Hit.Position)

		local dir = s.UnitRay.Direction * 1000
		local result = workspace:Raycast(s.Origin.Position, dir, params)

		s.Hit = CFrame.lookAt(result and result.Position or s.Origin.Position + dir, s.Origin.Position)
		s.Target = result and result.Instance
		s.TargetSurface = result and normal[Vector3.new(math.round(result.Normal.X),math.round(result.Normal.Y),math.round(result.Normal.Z))] or Enum.NormalId.Top
		
	end)

	runServ.Heartbeat:Connect(function()
		if (uis:GetMouseLocation() - last).Magnitude <= 0 then
			fire("Idle") -- wip
		else 
			fire("Move")
		end
		last = uis:GetMouseLocation()
	end)

	--cant add icon changing because you cant do that with userinputservice :(
	return s
end

return m
