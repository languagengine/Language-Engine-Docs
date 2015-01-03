Semantic Representations
========================

This provides the type theory LETT that meanings are represented in. This is not an explanation of LETT, or of type theory generally. For more information on the ideas behind LETT, read our white papers.

All whitespace is significant.

Substantial differences from ITT are noted with an exclamation mark next to the rule name.

Conventional abbreviations for function and pair types, and dummy variables for binders, are used as well, and are not mentioned.

Conventional parenthesization/precedence is used, and not mentioned.

The notation used below is:

    <context> !-[<signature>] <judgment>

    <premise>
    <premise>
    ...
    ------------ Rule Name
    <conclusion>

The signature is typically omitted as it's uniformly propagated through inference rules unchanged.

LETT
----

    G contains x at type A
    ---------------------- var
          G !- x : A
    
    S contains c at type A
    ---------------------- prim
        G !-[S] c : A
    
       G !- A : Prop
    ------------------- trust-me
    G !- trustMe(A) : A
    
    ---------------- Prop-F
    G !- Prop : Prop
    
    --------------- One-F
    G !- One : Prop
    
    ------------- One-I
    G !- <> : One
    
    ------------------- Boolean-F
    G !- Boolean : Prop
    
    ------------------- Boolean-I-true
    G !- true : Boolean
    
    -------------------- Boolean-I-false
    G !- false : Boolean
    
    G !- A : Prop    G, x : A !- B : Prop
    ------------------------------------- ->-F
          G !- (x : A) -> B : Prop
    
           G, x : A !- M : B
    --------------------------------- ->-I
    G !- \(x : A) -> M : (x : A) -> B
    
    G !- M : (x : A) -> B    G !- N : A
    ----------------------------------- ->-E
           G !- M N : [N/x]:B
    
    G !- A : Prop    G, x : A !- B : Prop
    ------------------------------------- *-F
          G !- (x : A) * B : Prop
    
    G !- M : A    G !- N : [M/x]B
    ----------------------------- *-I
    G !- < M , N > : (x : A) * B
    
    G !- M : (x : A) * B
    -------------------- *-E-fst
      G !- fst(M) : A
    
      G !- M : (x : A) * B
    ------------------------- *-E-snd
    G !- snd(M) : [fst(M)/x]B
    
    ------------------ String-F
    G !- String : Prop
    
         G, x : A !- M : B
    --------------------------- require !
    G !- require x : A in M : B
    
    ------------------ Entity-F !
    G !- Entity : Prop
    
    ----------------- Event-F !
    G !- Event : Prop
    
    G !- M : Entity    G !- N : String
    ---------------------------------- stringVal !
        G !- stringVal(M;N) : Prop
    
      G !- A : Prop
    ------------------ Max-F !
    G !- Max(A) : Prop
    
    G !- M : Max(A)    G, x : A !- B : Prop
    --------------------------------------- Max-E !
     G !- unwrapMax x : A = M in B : Prop