# Endpoints (v1.0.0)
This document serves as a quick reference for the endpoints, along with their respective functions, of an In-N-Out compatible server.
All endpoint URLs are relative to `/api/v1`, so if the URL is listed as `/version`, that expands to `/api/v1/version`.

With the exception of `/version` and `/instant/`*`EID`*`/key/*`, GET requests take at least the URL parameter `inq` (the id of the Inquisitor, which must have a permission level of at least 1) and their data is returned encrypted with the public key of the Inquisitor.
The bodies of POST requests must be clearsigned by the private key of the Actor, which must have a permission level of at least (2) Write.

If the required permissions of a particular endpoint are not met, the server will response with a code of 403. Any permission level higher than that which is required can also access the endpoint in the same manner.

| URL | Request Type | Behaviour | See Also |
| --- | :----------: | --------- | -------- |
| `/version` | GET | Returns "1.0.0" | [Versions](versions.md) |
| `/timeline` | GET | Returns a chronological list of every Moment in the Timeline | [Timeline List](moments.md#timeline-list) |
| `/moment/`*`MID`* | GET | Gets the given Moment by its MID, unsigned and in JSON format | [Moments](moments.md) |
| `/moment/`*`MID`* | POST | Creates a new Moment with the given MID | [Moments](moments.md) |
| `/instant` | GET | Gets the full JSON data of an current Instant | [Instants](instants.md) |
| `/instant/entities/`*`EID`* | GET | Gets the full JSON data associated with a given Entity | [Entities](instants.md#entities) |
| `/instant/entities/`*`EID`*`/name` | GET | Gets the real name of an Entity | [Entities](instants.md#entities) |
| `/instant/entities/`*`EID`*`/key/pub` | GET | Gets the public key of a given Entity; this endpoint is public | [Entities](instants.md#entities) |
| `/instant/entities/`*`EID`*`/key/priv` | GET | Gets the most recent private key of a given Entity; counterintuitively, this endpoint is public | [Entities](instants.md#entities) |