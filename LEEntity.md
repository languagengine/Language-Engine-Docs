LEEntity
========

Overview
--------

An `LEEntity` represents an entity, such as a file or a person, as opposed to an event or state-of-being (such as the state of being-a-file or being-a-person).

`LEEntity`s are created by the processor, and should never be created from methods on `LEEntity`. Either they will be created automatically, or if it's necessary to create new ones during the course of executing an event, the `LEProcessor` method `newEntity` can be used.

Some entities represent strings, and these therefore have string values associated with them. Entities for strings can be used to represent things like the name of something, or its location, as distinct from the object itself.

Some entities also represent the user and the computer, because of the special roles they each in a discourse.

String Values
-------------

    - (NSString*)stringValue;

Returns `nil` if the entity has no string value.

    - (BOOL)setStringValue:(NSString*)str;

Returns `YES` if the string value was successfully set, and `NO` otherwise.

User/Computer Roles
-------------------

    - (BOOL)isComputer;
    - (BOOL)isUser;