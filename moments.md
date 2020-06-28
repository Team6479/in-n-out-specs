# Moments
This document describes the behaviour and format of Moments.

## Moment IDs
Moment IDs, or MIDs, are used to uniquely identify a Moment.

MIDs are 128-bit numbers represented as 32-character hexadecimal strings.
Letters, when used, must always appear in UPPERCASE, as opposed to lowercase.
For example, `761D29CA573800E53BDDEA5E765671A6` is a valid MID, while `761d29ca573800e53bddea5e765671a6` is not.

If a new Moment is submitted with an invalid MID, the server must give a response code of 400 BAD REQUEST.

When a new Moment is submitted with a valid MID, if an existing Moment has that MID,
the Server must reject the request with a response code of 409 CONFLICT,
at which point the Client may assign the Moment a new MID and re-submit.
Collision is highly unlikely with such large numbers. However, implementations must be able to handle such occurrences.

## Immutability
Moments are immutable. Any data uploaded is permanently accessible to any Entity with the Read permission level. Be careful.

## Format
When submitting or retrieving Moments, they must always be encoded in JSON, preferably minified. They must also follow the following structure:
```json
{
  "v": "1.0.0",
  "event": "event-name",
  "timestamp": 0,
  "actor": "EID",
  "character": "EID",
  "data": "defined by event"
}
```
See [Versions](versions.md) for more info on the field `v`.

`timestamp` is a UNIX timestamp, in seconds.

Note that `data` may contain any variable type, as defined by the Event.
Even if it is ignored, it must still be present. We recommend using `null` in such cases.
If `data` is of the incorrect type or otherwise does not meet the Event's specifications, a code of 422 UNPROCESSABLE ENTITY will be returned.

### Malformed Moments
Any Moment submitted that does not adhere to the above format may be safely ignored.
The Server must return an response code of 400 BAD REQUEST upon its submission.

### Illegal Moments
Moments that follow the above structure but contain illegal or otherwise bad data must be added to the Timeline.
However, they must be ignored when building an Instant.

Unless stated otherwise, all Moments are considered Illegal if the Actor or Character references an Entity which does not exist,
or if they do not strictly follow the particular Event's Data format.

## Events
There are many types of Events, as listed below.

If the Event Name not herein listed is used by a Moment, that Moment is considered Illegal.

### Arbitrary Data
Event Name: `arbitrary`

Any completely spec-compliant implementation should ignore this entirely.
However, it may be used to submit data for out-of-spec purposes.
This exists to prevent out-of-spec programs from creating Illegal Moments.
Alternatively, it may be used by spec-compliant programs to issue human-readable comments not intended for machine use.

#### Data
The Data block may contain any data.

#### Legality
No special requirements are imposed.
However, the specified Character does not need to exist and may be null.

### New Entity
Event Name: `entity`

Creates an entity with the EID specified in `character`.

Upon creation, the Entity is given default values. If the defaults are not desired, one must change them through other Moments.
See [Entities](entities.md)

#### Data
The Data block will be ignored.

#### Legality
This will be considered Illegal if an Entity already exists with that EID.
EIDs are issued on a first-come, first-serve basis.

### Remove Entity
Event Name: `rm-entity`

Removes the Character as an Entity.

Note that while Moments' immutability dictates that the previous Entity's data will always be accessible,
if the Entity is created again, the prior data will not be automatically re-populated.

### Set Name
Event Name: `name`

Sets the human-readable name of the Character.

#### Data
A String containing the new human-readable name of the Character, or null if the Character's name is to be unset.

#### Legality
This will be considered Illegal if the specified name is non-null but does not match the following RegEx: `(\w|\ )*` (only alphanumeric and space characters)

### Set Public Key
Event Name: `pubkey`

Sets the public key of the Character. The name and email address of the key will be ignored.

#### Data
A String containing the new ascii-armored OpenPGP public key of the Character, or null if the public key is to be unset.

#### Legality
This will be considered Illegal if the key is non-null but cannot be parsed by [OpenPGP.js](https://openpgpjs.org).

This will also be considered Illegal if the Actor did not have [permission](perms.md) to set the public key of the Character.

### Set Private Key
Event Name: `privkey`

Sets the private key of the Character.

#### Data
A String containing the new ascii-armored, encrypted OpenPGP private key of the Character, or null if the private key is to be unset.

Since this will (counterintuitively) be publicly accessible, we highly recommend using a strong password.

#### Legality
If the specified key is non-null but cannot be parsed by [OpenPGP.js](https://openpgpjs.org), it will be considered Illegal.

### Grant Permission Level
Event Name: `grant`

Grants a permission level to the Character.

Note that this may promote or demote the Character.

#### Data
The permission level to grant, as an integer.

#### Legality
If the specified permission level is not valid (e.g. `-1` or `17`),
or if the Actor does not have the required permission level (see [Permissions](perms.md)),
it will be considered Illegal.

### Sign In
Event Name: `signin`

Starts a new Session for the Character.

#### Data
A String with the location of the Session, as follows:
* `home` - Working from home outside of a formal meeting
* other - An officially scheduled meeting; for versatility, names are not standardized, but should remain consistent.

For those used specifically by Team 6479, see [Locations](6479/locations.md).

#### Legality
Since Characters cannot have two simultaneous sessions, if a Character signs in more than once before signing out,
all but the most recent will be considered Illegal.
Previous sign-in events may still be recorded but must be considered [invalid](instants.md#end).

### Sign Out
Event Name: `signout`

Ends an existing Session for the Character.

#### Data
If the Session is approved at sign-out, a String with the EID of the approver. If the Session is not approved at sign-out, `null`.

#### Legality
Since Characters must sign in before signing out, any signout event not correlated with a signin event is considered Illegal.
If a Character is signed out twice with only one legal signin event, the first signout event is accepted.

### Cancel Session
Event Name: `cancel`

Ends an existing Session for the Character.

This Session will still be accessible but will be marked invalid, indicating that the Session should not be factored in to one's total time.
The end time will be recorded as the time of the cancellation event.

This should generally be used if someone was actually there on a given day but left before signing out.

#### Data
The Data block is ignored.

#### Legality
If the Character is individually signed out after their Session being cancelled but before their next sign-in, that sign-out supersedes the cancellation
and the cancellation event is considered Illegal. A cancellation event is otherwise Illegal according to the same standards as a sign-out event.

### Conclude Meeting
Event Name: `conclude`

Ends a meeting, canceling all sessions at the given location.
This is not the same as signing out all currently signed-in Characters.

#### Data
The location of the meeting. See [Sign Out](#sign-out).

#### Legality
There are no special conditions under which a Conclude Meeting event is Illegal.

## Timeline List
When a Timeline List is requested from the Server, a chronological list of every Moment, by their MID, will be returned.

The list will be formatted with each MID on its own line, with the oldest first.

## Submission
[See Also](endpoints.md)

The Client must generate the JSON data of the Moment, according to the specifications of this document. The JSON data should be minified.
The Client must then sign the JSON data using the OpenPGP associated with the Actor's most recent OpenPGP public key.
That signed data must be sent as the body of a POST request to `/api/v1/moment/`*`MID`*.