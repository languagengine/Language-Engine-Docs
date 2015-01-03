LEEventSet
==========

Overview
--------

`LEEventSet` represents a collection of `LEEvent`s. It's essentially just a wrapped array, with some help methods for filtering the array by matching.

Accessors
---------

    - (NSArray*)events;

Matching
--------

    - (NSArray*)findEventsPredicate:(NSString*)pred
                          arguments:(NSDictionary*)args;

Returns those member events which return true for a corresponding call to `matchPredicate:Arguments:`.