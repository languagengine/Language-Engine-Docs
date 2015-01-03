LEPlugin
========

Plugins are the locus of customization for Language Engine, and different sorts of host applications will require different sorts of plugins. A file system app would have a different plugin than a Twitter client, while a robot might have a whole suite of plugins, each one for a different class of behavior.

Plugins have a `name` property, which must be specified uniquely in concrete subclasses, as this is the means by which name spacing is handled in the semantics. A base plugin is provided, which is always included by processors, which has unnamespaced meanings that are common to all domains/tasks. See "Base Plugin" below for more information.

An `LEPlugin` handles all of the domain-specific knowledge that Language Engine needs. This includes the vocabulary items specific to the domain, the meanings that the vocabulary is built out of, and the methods to dispatch to in order to perform actions and realize events.

`LEPlugin` itself is an abstract class. Concrete instances implement different functionality. When an instance is added to a processor, its `setup` method is called, which builds all of the custom knowledge into a form the processor can use. 

The plugins analyze the world when the processor needs to refresh the world model. This means inspecting the world and then informing the processor of what the plugin "sees". 

Lastly, plugins execute events, which is to say they make sure that an event accurately represents the world. Present with an event of copying, for instance, a filesystem plugin would perform an actual copy using an FS lib.

Additional methods can be added to concrete subclasses which the processor can dispatch to. For instance, rather than sending an event of copying to the `executeEvent:eventSet:` method, it might be more convenient to handle it with a method named `copyCopier:Original:Result:eventSet:`. This kind of dispatching, analogous to routes in web servers, makes it easy to separate functionality without sacrificing automatic dispatch. Any such method must have a final argument of type `LEEventSet*`.

`LEPlugin` also comes equipped with a number of methods for defining lexical items, including convenience methods for nouns and verbs. Because English can have non-uniform morphology on verbs, such as "copy" vs. "copied" vs. "copies", there are methods for automatically deriving alternate forms of nouns and verbs.

And finally, because plugins need to give words meaning, possibly in terms of new, domain-specific predicates, `LEPlugin` has methods for adding these as well.

Accessors
---------

    - (NSString*)name;

The name of the plugin must be specified in concrete subclasses.

Overridable Methods
-------------------

    - (BOOL)setup;

This method is where customization happens, and should be over-ridden in concrete subclasses. Return `YES` if setup is successful, and `NO` otherwise (such as if a word cannot be added);

    - (void)analyzeWorld;

Analyze the world and inform the processor of what's known. This method should be over-ridden in concrete subclasses. See also "The World" below.

    - (BOOL)executeEvent:(LEEvent*)event eventSet:(LEEventSet*)evs;

Perform necessary actions to make an event real. Because individual sentences might have multiple events, any time one of them is sent to be executed, the whole event set it's part of is also sent, in case the other events are relevant to the execution of the specific event.

Dispatching Events
------------------

    - (BOOL)setDispatchPredicate:(NSString*)pred keywords:(NSArray*)keywords method:(SEL)sel;

Specify a method to be used to dispatch an event with predicate `pred`, and with argument keywords `keywords`. Any event with the same predicate, and with arguments of exactly those keywords (and no more or fewer keywords), will be dispatched to `sel` on the plugin. There should be as many arguments for `sel` as the length of `keywords` plug an extra arg for the event set.

For example, if we define a method `moveMover:moved:dest:eventSet:` to represent someone moving something from one place to another, we can create a dispatch like so:

    [plugin setDispatchPredicate:@"Move"
                        keywords:@[@"Mover",@"Moved",@"Dest"]
                          method:@selector(moveMover:moved:dest:eventSet:)];

When the plugin is sent an appropriate event to dispatch, it will then use the specified method. An event like this:

    Move(Mover = [x], Moved = [y], Dest = [z])
    
will successfully dispatch with the specified method `moveMover:moved:dest:eventSet:`, while an event like this will not because of the missing keyword destination argument:

    Move(Mover = [x], Moved = [y])

Adding Custom Meanings
----------------------

    - (BOOL)addPredicate:(NSString*)prd;

Add a custom predicate of events, such as `Copy` or `File`.

    - (BOOL)addRelation:(NSString*)rel;

Add a custom argument relation, such as `Copier`, `Original`, or `Result`.

Adding Custom Vocabulary
------------------------

    - (BOOL)addLexicalEntry:(NSString*)frm
                   category:(NSString*)cat
                  semantics:(NSString*)sem;

Add a new word to the plugin, with the form `frm`, syntactic category `cat`, and semantics `sem`. See the docs for syntactic categories and semantic representations for more information on what these strings should look like.

    - (BOOL)addCountNounSg:(NSString*)sgf
                        pl:(NSString*)plf
                        semantics:(NSString*)sem;

Add lexical entries for a count noun with singular and plural forms `sgf` and `plf`, respectively, with semantics `sem`. See the docs for semantic representations for more information on what the `sem` string should look like.

    - (BOOL)autoAddCountNoun:(NSString*)sgf;

Add lexical entries for a count noun with singular form `sgf`, automatically deriving a plural form and a semantics. The automatically derived semantics involves creating a new event predicate `AutoEvent_Sgf`, for whatever `sgf` is (eg. `AutoEvent_File` if `sgf == @"file"`, notice the capitalization!), as well as assigning the meaning

    \(x : Entity) -> (s : Event) * AutoEven_sgf s * Exp s x
    
to the new word. Again, with `sgf == @"file"`, the example meaning would be

    \(x : Entity) -> (s : Event) * AutoEven_File s * Exp s x

.

    - (BOOL)addMassNoun:(NSString*)ms semantics:(NSString*)sem;

Add a lexical entry for a mass noun with form `msf`, with semantics `sem`. See the docs for semantic representations for more information on what the `sem` string should look like.

    - (BOOL)autoAddMassNoun:(NSString*)msf;

Add a lexical entry for a mass noun with form `msf`, with automatically derived semantics as for count nouns.

    - (BOOL)addIntransitiveVerbBare:(NSString*)baref
                               perf:(NSString*)perff
                               prog:(NSString*)progf
                               pass:(NSString*)passf
                                ger:(NSString*)gerf
                          fstSgPres:(NSString*)fstSgPres
                          sndSgPres:(NSString*)sndSgPres
                          thdSgPres:(NSString*)thdSgPres
                          fstPlPres:(NSString*)fstPlPres
                          sndPlPres:(NSString*)sndPlPres
                          thdPlPres:(NSString*)thdPlPres
                          fstSgPast:(NSString*)fstSgPast
                          sndSgPast:(NSString*)sndSgPast
                          thdSgPast:(NSString*)thdSgPast
                          fstPlPast:(NSString*)fstPlPast
                          sndPlPast:(NSString*)sndPlPast
                          thdPlPast:(NSString*)thdPlPast
                          semantics:(NSString*)sem;

Add a lexical entry for an intransitive verb with bare form `baref`, perfective form `perff`, progressive form `progf`, passive form `passf`, gerund form `gerf`, and tense forms with agreement, and with semantics `sem`. Tense forms are for agreement a full paradigm of pronouns:

                  |          Person            |
                  |-----,----------,-----------|
                  | 1st |   2nd    |    3rd    |
    ---,----------|-----|----------|-----------|
       | Singular |  I  |   you    | he/she/it |
     # |----------|-----|----------|-----------|
       | Plural   | we  | you/yall |   they    |
    ---|----------|-----|----------|-----------|

These correspond to different versions of the same verb (e.g. I *am*, you *are*, he/she/it *is*, etc.). The bare form is citation form (as in infinitives and commands, e.g. to *be*), perfective form is for use with perfective auxiliary "have" (e.g. I have *been*, I have *seen*), progressive form is for use with perfective auxiliary "be" (e.g. I am *being*, I am *seeing*), passive form is for use with passive auxiliary "be" (e.g. I was *seen*), and gerundive form is for use in certain modificational verb phrases (e.g. while *being*, while *seeing*).

See the docs for semantic representations for more information on what the `sem` string should look like.

    - (BOOL)addTransitiveVerbBare:(NSString*)baref
                             perf:(NSString*)perff
                             prog:(NSString*)progf
                             pass:(NSString*)passf
                              ger:(NSString*)gerf
                        fstSgPres:(NSString*)fstSgPres
                        sndSgPres:(NSString*)sndSgPres
                        thdSgPres:(NSString*)thdSgPres
                        fstPlPres:(NSString*)fstPlPres
                        sndPlPres:(NSString*)sndPlPres
                        thdPlPres:(NSString*)thdPlPres
                        fstSgPast:(NSString*)fstSgPast
                        sndSgPast:(NSString*)sndSgPast
                        thdSgPast:(NSString*)thdSgPast
                        fstPlPast:(NSString*)fstPlPast
                        sndPlPast:(NSString*)sndPlPast
                        thdPlPast:(NSString*)thdPlPast
                        semantics:(NSString*)sem;

Add a lexical entry for a transitive verb, with morphology as for an intransitive verb. See the docs for semantic representations for more information on what the `sem` string should look like.

    - (BOOL)autoAddIntransitiveVerb:(NSString*)baref;

Add a lexical entry for an intransitive verb, with the morphology and semantics automatically derived from the bare form `baref`. Like for nouns, this creates a predicate `AutoEvent_Baref` (e.g. `AutoEvent_Look` if `baref == @"look"`). The semantics it produces is

    \(x : Entity) -> \(e : Event) -> AutoEvent_Baref e * Subj e x

using the base plugin's `Subj` relation in place of a custom argument relation. 

    - (BOOL)autoAddIntransitiveVerb:(NSString*)baref
                          semantics:(NSString*)sem;

Add a lexical entry for an intransitive verb, with the morphology derived automatically, but with the semantics given explicitly. See the docs for semantic representations for more information on what the `sem` string should look like.
                          
    - (BOOL)autoAddTransitiveVerb:(NSString*)baref;

Add a lexical entry for a transitive verb, with the morphology and semantics automatically derived from the bare form `baref`. The semantics it produces is

    \(y : Entity) -> \(x : Entity) -> \(e : Event) ->
        AutoEvent_Baref e * Subj e x * Obj e y

using the base plugin's `Subj` and `Obj` relations in place of custom argument relations.

    - (BOOL)autoAddTransitiveVerb:(NSString*)baref
                        semantics:(NSString*)sem;

Add a lexical entry for a transitive verb, with the morphology derived automatically, but with the semantics given explicitly. See the docs for semantic representations for more information on what the `sem` string should look like.

Base Plugin
-----------

The base plugin comes with a number of pre-defined meanings, as follows:

    SpeechAct : Event -> Prop
    Speaker : Event -> Entity -> Prop
    Addressee : Event -> Entity -> Prop
    
These encode the speech act event itself, and the roles of speaker (usually the user) and addressee (usually the computer). Generally, plugins will not need to use these, because build-in pronouns and other constructions handle picking out speech act roles.
                               
    Subj : Event -> Entity -> Prop
    Obj : Event -> Entity -> Prop
    Dest : Event -> Entity -> Prop
    Exp : Event -> Entity -> Prop
    
These encode default argument roles. For vocabulary items added with the `auto*` methods, these will be used instead of custom argument roles. So for example, an automatically generated verb entry for "open" will have a `Subj` argument, instead of, say, a `Opener` role.
                               
    Named : Event -> Prop
    Name : Event -> Entity -> Prop

Some meanings specific to the state of having a name.
