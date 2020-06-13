# Dota2

[![GoDoc Widget]][GoDoc] [![Go Report Card Widget]][Go Report Card]

[GoDoc]: https://godoc.org/github.com/MrJoshLab/go-dota2
[GoDoc Widget]: https://godoc.org/github.com/MrJoshLab/go-dota2
[Go Report Card Widget]: https://goreportcard.com/badge/github.com/MrJoshLab/go-dota2
[Go Report Card]: https://goreportcard.com/report/github.com/MrJoshLab/go-dota2

## Introduction 
**go-dota2** is a DOTA 2 client plugin for go-steam. The intent is to replicate as much of the client functionality as possible.

Also see: the [C#/SteamKit Implementation](https://github.com/paralin/Dota2).

## Implementation Progress

Complete.

 - [x] GC session state management
 - [x] Player profile fetching / call tracking
 - [x] SOCache tracking / state management
 - [x] Basic chat interaction
 - [x] Lobby tracking / state management
    - [x] Read lobby state correctly
    - [x] Implement normal lobby operations
 - [x] Party tracking / state management
    - [x] Read party and invite state correctly
    - [x] Implement normal party operations
 - [x] Code generation for API
 - [x] Code generation for events

## SOCache Mechanism

The caching mechanism makes it easy to watch for changes to common objects, like `Lobby, LobbyInvite, Party, PartyInvite`.

This mechanism is used everywhere, these objects are not exposed in their own events.

```go
import (
	gcmm "github.com/MrJoshLab/go-dota2/protocol"
	"github.com/MrJoshLab/go-dota2/cso"
)

eventCh, eventCancel, err := dota.GetCache().SubscribeType(cso.Lobby)
if err != nil {
    return err
}

defer eventCancel()

lobbyEvent := <-eventCh
lobby := lobbyEvent.Object.(*gcmm.CSODOTALobby)
```

Events for the object type are emitted on the eventCh. Be sure to call `eventCancel` once you are done with the channel to prevent resource leaks.

The cache object also adds interfaces to get and list the current objects in the cache.

## Implementation Generation

The base API implementation is generated by the [apigen](./apigen) code. Using heuristics, request IDs are matched to response IDs, and events and action-only requests are identified. Some manual tweaking is done in the overrides file.

Next, the API information is used to build a Go code-gen set of implementations around the `MakeRequest` request tracking mechanism.

This means that ALL of the Dota API will be available in this codebase, although only some of it is documented.

## go-steam Dependency

This library depends on `go-steam`. Currently we are using the [FACEIT fork](https://github.com/faceit/go-steam).

