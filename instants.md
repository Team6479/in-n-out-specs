# Instants
Instants are built from the timeline and show the current state.

When requested, an Instant is returned in the following format:
```json
{
  "entities": {
    "eid": { ... },
    ...
  }
}
```
The format for each individual Entity is described in [Entities](#entities).

## Entities
Each Entity's data within the Instant is returned in the following format:
```json
{
  "lvl": 0,
  "name": "John Doe",
  "pubkey": "-----BEGIN PGP PUBLIC KEY BLOCK-----...",
  "privkey": "-----BEGIN PGP PRIVATE KEY BLOCK-----...",
  "sessions": [
    { ... },
    ...
  ]
}
```
Note that `name`, `pubkey`, and `privkey` may be null if they have not been set.
`lvl` will always be a valid numeric permission level. It defaults to zero if not set otherwise.

## Sessions
Each Session's data within the Instant is returned in the following format:
```json
{
  "start": 0,
  "end": 0,
  "valid": true,
  "location": "location-id",
  "approver": "eid",
}
```

### Start
The UNIX timestamp, in seconds, of the time when the Character was signed in.

### End
The UNIX timestamp, in seconds, of the time when the Character was signed out.
This will be null if the Character is signed in without being signed out.

### Valid
This will be considered true only if the Character is signed in,
then signed out before their next sign-in event.

Only valid Sessions should be considered when computing any time totals.

### Location
The ID of the location at which the meeting took place. See [Sign In](moments.md#sign-in).

### Approver
The EID of the Entity which approved the Session. See [Sign Out](moments.md#sign-out).