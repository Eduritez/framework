## :globe_with_meridians: Getting started:

```bash
git clone https://github.com/AzureLegacy/AnimeBalls
```

## :page_facing_up: Documentation:

### :package: Framework

This game uses a custom lightweight framework that simplifies communication between scripts (similar to Knit). It also improves workflow by simplifying frequent lines of code.

It's organized by two types of script: services and controllers, for server and client respectively.

- Integrated functions:
    - `service.Init()` for setting up initial variables (yields)
    - `service.Start()` for main tasks in a script
    - `service.PlayerSetup(player: Player)` when player joins, which runs first across all scripts (yields)
    - `service.PlayerAdded(player: Player)` when player joins
    
- Optional functions:
    - `service.Step(dt: number)` for the main RunService.Stepped loop in a script
    - `service.RenderStep(dt: number)` for the main RunService.RenderStepped loop in a script
    - `service.CharacterAdded(player: Player)` when any character is added
    - `service.PlayerRemoving(player: Player)` when player leaves
    - `service.BindToClose()` when the server is shutting down

Modules can be obtained by service.modules.ModuleName, where ModuleName is an uniquely named module in *ReplicatedStorage.SharedModules* or *ServerScriptService.Modules*

### :clipboard: Conventions

- Script names are always **PascalCase**
- Components and methods of a module / table are always **PascalCase**
- Service / controller / module variables are always **PascalCase**
- Other variables are always **camelCase**

## :gear: Services:
> Naming convention: *____Service (e.g, EnemyService, UnitService, etc...)*

**Overview:**
- Services are server scripts located inside *ServerScriptService.Scripts*
- Other services can be obtained by `service.framework:Get("ExampleService")` in service.Init
- Functions in other services (e.g `function service.Update(...)` in ExampleService) can be called by `ExampleService.Update(...)`
- Controllers can be obtained by `service.framework:Fetch("ExampleController")` in service.Init

**Remotes:**
- Service functions that can be called from the client (remotes) are in **service.Client**
    ```lua
    function service.Client.ExampleFunction(player: Player, ...)
    end
    ```
- Remotes can be called from controllers as follows:
    ```lua
    ExampleService.ExampleFunction:Fire(...)
    local output = ExampleService.ExampleFunction:Invoke(...) --//Yields
    ```

**Example:**
```lua 
--[[ExampleService]]
service = {Client = {}}

--//Initialize variables
local ReplicatedStorage = game:GetService("ReplicatedStorage")

--//Initialize services, controllers, and modules INLINE
local CurrencyService
local ExampleController
local ExampleInfo

function service.Init()
    --//Define controllers, services, and modules by their script name
    CurrencyService = service.framework:Get("CurrencyService")

    ExampleController = service.framework:Fetch("ExampleController")

    ExampleInfo = service.modules.CurrencyInfo
end

--//Remotes
function service.Client.Click(player: Player, ...)
    return true
end

--//Main
function service.ExampleFunction(...)
end

function service.PlayerAdded(player: Player)
    task.wait(2)
    ExampleController.Replicate:Fire(player)
end
    
function service.Start()
    service.ExampleFunction()
    CurrencyService.ExampleFunction()
end

return service
```

## :wrench: Controllers:
> Naming convention: *____Controller (e.g, EnemyController, UnitController, etc...)*

**Overview:**
- Controllrs are client scripts located inside *StarterPlayer.StarterPlayerScripts.Scripts*
- Other controllers can be obtained by `service.framework:Get("ExampleController")` in service.Init
- Functions in other controllers (e.g `function service.Update(...)` in ExampleController) can be called by `ExampleController.Update(...)`
- Services can be obtained by `service.framework:Fetch("ExampleService")` in service.Init

**Remotes:**
- Controller functions that can be called from the server (remotes) are in **service.Server**
    ```lua
    function service.Server.ExampleFunction(...)
    end
    ```
- Remotes can be called from services as follows:
    ```lua
    ExampleController.ExampleFunction:Fire(player, ...)
    ExampleController.ExampleFunction:Fire(playerTable, ...)
    ExampleController.ExampleFunction:FireAll(...)
    ```

**Example:**
```lua
--[[ExampleController]]
service = {Server = {}}

--//Initialize variables
local ReplicatedStorage = game:GetService("ReplicatedStorage")

--//Initialize controllers, services, and modules INLINE
local CurrencyController
local ExampleService
local ExampleInfo

function service.Init()
    --//Define controllers, services, and modules by their script name
    CurrencyController = service.framework:Get("CurrencyController")

    ExampleService = service.framework:Fetch("ExampleService")

    ExampleInfo = service.modules.ExampleInfo
end

--//Remotes
function service.Server.Replicate(...)
    print("Replicated")
end

function service.Start()
    local output = ExampleService.Click:Invoke()
end

return service
```
