LEProcessor
===========

Overview
--------

An `LEProcessor` is responsible for the interpretation of a sentence, and the dispatching of events to plugins for handling. Before processing can begin, the Language Engine core functionality must be turned on (and off again at the end of use), which `LEProcessor` handles.

Processors also manage the internal representation of the world that is necessary to handle the pragmatics of sentences (such as presuppositions). The world model can be accessed for inspection. It can also be refreshed, such as when moving from one directory to another in a file system, thereby changing the surroundings that the processor is "aware of". Viewed anthropomorphically, the world model represents what the processor knows, from directly analyzing the world (via plugins), from being told (as when the user asserts something), and from its own actions (by dispatching).

Since some tasks only require certain types of sentences -- statements, commands, or questions -- `LEProcessor` has settings for all of these. When a sentence is processed, if it's of an unaccepted type, the processor returns an error code to the caller.


Starting and Stopping
---------------------

    + (void)startLanguageEngine;
    + (void)stopLanguageEngine;

These must be called before/after using an `LEProcessor` or bad things will happen.

Creating `LEProcessor`s
-----------------------

Normally, a processor can be created with just `new`.

    + (id)withPlugins:(NSArray*)plugins;

A convenience method for creating a processor, and adding a number of plugins all at once.

Sentence Types
--------------

    - (void)setAcceptsIndicatives:(BOOL)yn;
    - (void)setAcceptsImperatives:(BOOL)yn;
    - (void)setAcceptsInterrogatives:(BOOL)yn;

Adding Plugins
--------------

    - (BOOL)addPlugin:(LEPlugin*)plugin;

Finishing Setup
---------------

    - (void)finishSetup;

After all the plugins have been added, calling `finishSetup:` will extract vocabulary information and type signatures from the plugins, for use in parsing and type checking. Don't forget to call this before using a processor!

Processing Sentences
--------------------

    - (LEProcessorCode)processInput:(NSString*)input;

Calling `processInput:` will result in the input being processed (parsed, converted to meaning, type-checked, etc.). The foreground events that result will then be dispatched to the appropriate plugin, if the plugin has a registered handler method, or the plugin's `executeEvent:` method will be called instead.

    - (LEProcessorCode)eventSetForInput:(NSString*)input output:(LEEventSet*)output;

If the host app wants to handle events differently manually, rather than automatically dispatching the foreground events, it can use `eventSetForInput:`, which will have the same effect as `processInput:`, except for the dispatching to plugins.

World Models
------------

    - (void)setShouldRefreshWorldModel;

Called by plugins when they perform actions that change the world in ways not manifestly present in the sentences being processed. For example, moving to a new directory reveals new, previously unknown files, and would require a refresh of the world model.

    - (LEEventSet*)worldModelDescription;

Used for querying what the processor knows about the world. The world model description is updated to include new knowledge from the input sentences prior to any events being dispatched. Therefore, by the time plugins receive dispatches, the world model represents the way the world "ought to look" as a result of dispatching.

    - (LEEntity*)newEntity;

Create a new entity by updating the world model. This should always be used to create new entities.

    - (BOOL)assertEventPredicate:(NSString*)prd arguments:(NSDictionary*)args sender:(LEPlugin*)plg;

Inform the processor that some new event exists, of the sort classified by `prd`, relating the specified arguments. For example, when analyzing the world, a filesystem plugin might want to inform the processor of a file that exists. This could be done like so, assuming the entity `x` has already been created:

    [processor assertEventPredicate: @"File"
                          arguments: @{ @"Exp": [x] }];

This method automatically creates a new event object in the world model.