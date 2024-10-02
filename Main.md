# VULCAN ANTI-CHEAT API

## Introduction
This API provides a series of hooks and methods to manage player punishment events, client reporting, and anti-cheat measures. This API documentation will hopefully suffise in lieu of actually having raw source code as the anti-cheat is currently closed source.

## Hooks
> **VULCAN-Punishment**

Executed when the punishment event system has started a punishment event. From this state you can cancel punishments using the punishment API directly or return true to disable the punishment event instantly.
```lua
hook.Add( 'VULCAN-Punishment', 'Example', function( ENT, Reason, Kick,  ... )
	MsgN( ENT:Nick( ), ' is being punished for ', Reason ) -- paradox is being punished for Example
	
	PrintTable( { ... } ) -- Print all extra information that is used for the logging.
	
	return true
end )
```
> **VULCAN-Reporter**

Executed if (when) the client sends the bcReporter module information back to the server. The code block will be a set of data about the clients reported system.
```lua
hook.Add( 'VULCAN-Reporter', 'Example', function( ENT, Code )
	MsgN( ENT:Nick( ), ' sent back bcReporter!' ) -- paradox sent back bcReporter!
	
	PrintTable( Code ) -- Print all the system information provided to the server.
   /*
      Sensitivity
      Resolution
      Game Branch
      Operating System
      Configuration (binds, settings, exc)
   */
end )
```
> **VULCAN-Meta**

Executed if the client sends back a invalid meta callback to the server.
```lua
hook.Add( 'VULCAN-Meta', 'Example', function( ENT, Reason )
	MsgN( ENT:Nick( ), ' is going to be punished by bcMeta!' ) -- paradox is going to be punished by bcMeta!
	
   MsgN( Reason ) -- Example
end )
```
> **VULCAN-Sending**

Executed when the server is about to send the bursted modules (bc) to the client, return a string to have it override the file that is going to be sent.
```lua
hook.Add( 'VULCAN-Sending', 'Example', function( Index, ENT )
	MsgN( Index ) -- The code that was to be sent.

   Index = 'print(\'Hello World!\')'

   return Index -- Overriden.
end )
```
> **VULCAN-Activate**

Executed when a player is active and ready to take server commands. Note the client doesn't yet exist in the optimizations API therefore you will not be able to grab CUserCMD information or other valuable information yet.
```lua
hook.Add( 'VULCAN-Activate', 'Example', function( ENT )
   MsgN( ENT:Nick( ) ) -- paradox
end )
```

## Methods

# Punishment
> **Check**

Checks if a punishment event is active for a given entity.
```lua
local Punished, Reason = VULCAN.Punishment:Check( Entity( 1 ) )

MsgN( Punished ) -- Will return true if the player is about to be punished.
MsgN( Reason ) -- Will return the reason if the player is about to be punished.
```
> **Start**

Initiates a punishment event for a player.
```lua
VULCAN.Punishment:Start( Entity( 1 ), 'Cheating', true, 'Additional info' ) -- ENT, Reason, Kick, ...
```
> **Punish**

Finalizes the punishment event, either kicking or banning the player after checking the config.
```lua
VULCAN.Punishment:Punish( Entity( 1 ), 'Cheating', true ) -- ENT, Reason, Kick
```

# Logs
> **Format**

Function format log text with the corrent time and date (also puts a newline at the end!).
```lua
MsgN( VULCAN.Logs:Format( Entity( 1 ), 'Hello!', 'Hello2' ) )
/*
[ 06:02:46 : loopback ] Hello!
   | Hello2
*/
```
> **Log**

Function to log to the players specific log.
```lua
VULCAN.Logs:Log( Entity( 1 ), 'Hi', 'file.txt', false ) -- The last two are optional.
-- The last one will delete the file and never add a header (the VULCAN logo).
```
> **EasyLog**

Function to write a nice log, easily (duh!). It calls the log function with everything filled out for you if you aren't planning on making custom file systems. It also automatically calls Format.
```lua
VULCAN.Logs:EasyLog( Entity( 1 ), 'Hello', 'Hello 2', 'Hello 3' ) 
/*
[ 06:02:46 : loopback ] Hello!
   | Hello2
   | Hello3
*/
```

# Optimizations
> **Bound**

Function used to bind two functions together. Adds the meta table to the function when called.
```lua
local Show = { 
   Test = true
}

function Show:Showcase( )
   MsgN( self.Test ) -- true
end

Showcase = VULCAN.Optimizations:Bound( Show.Showcase, Show ) -- Will return a function.

Showcase( )
```
> **GetStaff**

Generates a table of staff players based on the configuration.
```lua
PrintTable( VULCAN.Optimizations:GetStaff( ) )
```
> **Timer**

Function used to make a dynamic timer that doesn't require Entity validation. The provided Entity WILL be valid.
```lua
VULCAN.Optimizations:Timer( Entity( 1 ), function( ENT )
   MsgN( ENT ) -- Entity( 1 )
end, 5, nil ) -- ENT, Callback, Delay, Meta
```
> **Fetch**

Function to fetch a key from the localizer.
```lua
MsgN( VULCAN.Optimizations:Fetch( Entity( 1 ), 'Health' ) ) -- 100
```
> **Set**

Function to set a key in the localizer.
```lua
VULCAN.Optimizations::Set( ENT, { 
   [ 'Example' ] = true
} )

VULCAN.Optimizations::Set( ENT, 'Example', false )
```
