LEEvent
=======

Overview
--------

`LEEvent` represents events or states-of-being such as copying or being a file. `LEEvent`s are created for you by `LEProcessor`in the course of interpreting a sentence.

An event is classified by a predicate, such as `@"Copy"` for a copying event or `@"File"` for a being-a-file state. Events also have arguments, which are (lists of) `LEEntity`s that are involved in the event. An event of copying might have a `@"Copier"` argument, which represents the person or thing performing the action of copying, a `@"Original"` argument for the item being copied, and `@"Result"` argument for the copies produced by the action. Or a being-a-file state might have an `@"Experience"` argument for the experiencer of the state (i.e. the file)

A good analogy for what events are is a keyword argument function call:

    Copy(Copier = [x], Original = [y], Result = [z])
    File(Experiencer = [w])

Lists are used instead of individual items so that events which require multiple participants in a given role can be represented. For instance, an event of surrounding must be performed by multiple items, not just one, so we might think of it like so:

    Surround(Surrounder = [x,y,z], Surrounded = [w])

Events are considered either foreground or background (i.e. not foreground). A foreground event is an event which constitutes part of the core meaning of a sentence, rather than incidental (background) information. Backgrounded events are not dispatched to plugins when performing events, but can be accessed in case they contribute to the dispatching. For instance, "move this file to that folder" would have a foreground event of moving, and a being-a-file and being-a-folder event in the background. Only the moving must occur, but if the folder doesn't exist, the plugin would want to create it, so knowing about it is necessary.

Events are also considered either existent or non-existent. An existent event represents a single event or state which exists in the world (one occurrence of copying, for instance), whereas a non-existent event represents the denial that any such event or state exists.

Accessors
---------

    - (NSString*)pluginName;
    - (NSString*)predicate;
    - (NSString*)event;
    - (BOOL)foreground;
    - (BOOL)exists;
    - (NSArray*)keywords;
    - (NSArray*)arguments;
    - (NSArray*)argumentsForKeyword:(NSString*)keyword;

Matching Events
---------------

    - (BOOL)matchPredicate:(NSString*)pred arguments:(NSDictionary*)args;

For convenience, there is a method `matchPredicate:arguments:`, which makes it easy to determine if an event has a given predicate and/or arguments. The predicate can be `nil`, in which case it will ignored. For example, the copying event from above, repeated here:

    Copy(Copier = [x], Original = [y], Result = [z])
    
would match like so:

    [thatEvent matchPredicate: @"Copy"
                    arguments: @{ @"Original": @[y] }]
    == Yes

because the predicate is indeed `@"Copy"` and the `@"Original"` argument is indeed the just `y`. The match fails if either the predicates are different, or if for any argument name in the `arguments` dictionary, the corresponding values in the event are different.