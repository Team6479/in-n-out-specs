# Terminology
This document describes various terms used throughout these standards.

## Timeline
A Timeline is a chronological set of Moments.

## Moment
A Moment is an immutable entry in a Timeline containing new or modified data.

Moments are referred to by their Moment ID (MID).

## Instant
An Instant is a snapshot of the state of a system at a certain time according to a Timeline.

## Entity
An Entity is any account within the system. An Entity may have various [permission levels](perms.md).

Entities are referred to by their Entity ID (EID), which acts like a username.

## Actor
An Actor is an Entity which Acts, or creates data. Specifically, each Moment is created by an Actor.

## Character
A Character is the Entity affect by or otherwise associated with certain data. Specifically, a Moment describes an Event happening to a Character.

## Inquisitor
An Inquisitor is an Entity which Inquires, or seeks data. Specifically, when a request is made to the server for data, the Inquisitor is the Entity requesting the data.

In general, GET requests have an Inquisitor while POST requests have an Actor.

## Event
An Event is the particular type of data described by a Moment.

## Session
The time from an Entity signing in to signing out.

## Server
A centralized location which responds to [requests](endpoints.md) and stores data.

## Client
Any interface by which data may be transmitted to the Server.