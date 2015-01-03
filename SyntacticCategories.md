Syntactic Categories
====================

Syntactic categories in Language Engine are a polymorphic, feature-based variant of categorial grammar categories, defined using a BNF like so:

    <cat> ::= <var>
            | "C[" <cform> "]"
            | "S[" <vform> "]"
            | "N[" <nform> "," <num> "]"
            | "D[" <pers> "," <num> "," <case> "," <wh> "]"
            | "PEv[" <string> "]"
            | "PEnt[" <string> "]"
            | "Part[" <string> "]"
            | "String"
            | <cat> "\" <cat>
            | <cat> "/" <cat>
            | <cat> ">" <cat>
    <var> = "_" | "_" <number>
    <cform> ::= <var> | "ind" | "imp" | "intr" | "bg"
    <vform> ::= <var> | "bare" | "perf" | "prog" | "pass" | "ger" | "fin"
    <nform> ::= <var> | "comm" | "prop"
    <pers> ::= <var> | "1" | "2" | "3"
    <num> ::= <var> | "sg" | "pl" | "ms"
    <case> ::= <var> | "nom" | "acc"
    <wh> ::= <var> | "-WH" | "+WH"

Parenthesization is omitted, but has the obvious form: the connectives "/", "\" and ">" are of equal precedence and so all but the outermost parentheses can be omitted.

The category `C[...]` represents full clauses with mood. Also known in mainstream syntax as `CP`. The feature `<cform>` represents the kinds of clause that exist: foregrounded/main clauses, which can be indicative (statements/assertions), imperative (commands), or interrogative (questions), and also backgrounded clauses. Expressions with category `C[...]` must have the semantic type `Prop`.

The category `S[...]` represents the moodless portion of a clause, also known as `TP` in mainstream syntax. The feature `<vform>` represents the morphological form of the verb that heads the clause. Expressions with the category `S[...]` must have the semantic type `Event -> Prop`.

The category `N[...]` represents nouns, with forms `comm` for common nouns like "file", and `prop` for proper nouns like "John". Number is represented by the feature `<num>`, with singular (`sg`), plural (`pl`), and mass (`ms`). Expressions with the category `N[...]` must have semantic type `Entity -> Prop`.

The category `D[...]` represents determiners (or `DP`s), which have quantificational meanings. The person feature `<pers>` has possible values `1`, `2`, and `3` for first, second, and third person. The case feature `<case>` can be either nominative (`nom`) or accusative (`acc`). The WH feature `<wh>` can be either `+WH` or `-WH` to indicate if the DP is a WH phrase (e.g. "who", "what", etc.). Expressions with the category `D[...]` must have the semantic type `(Entity -> Prop) -> Prop`, which corresponds to quantificational meanings.

The category `PEv[...]` corresponds to prepositional phrases that are arguments to verbs (e.g. put the book *on the table*), rather than adverbial modifiers. The string feature is used to code for classes of prepositions that can be used with a verb, which is usually idiosyncratic. Expressions with the category `PEv[...]` must have the semantic type `Event -> Prop`.

The category `PEnt[...]` corresponds to prepositional phrases that are arguments to nouns (e.g. a book *of poems*), rather then adnominal modifiers. The string feature is as for `PEv[...]`. Expressions with the category `PEnt[...]` must have the semantic type `Entity -> Prop`.

The category `Part[...]` corresponds to verb particles, used for verb particle constructions where the verb+particle form a complex, possibly discontinuous, unit, and where other verbs with the same morphology may exist with different meanings. The string feature allows idiosyncratic classification as in other cases. For example, "pass out" which means "faint" vs. "pass" which means "go by". The particle "out" would have the category `Part["out"]`, while the corresponding version of "pass" would have the category `itv/Part["out"]` (where `itv` is an abbreviation of category for intransitive verbs). Expressions with the category `Part[...]` must have the semantic type `One`, indicating that particles themselves contribute no meaning, and instead it is the particular version of the verb which codes all of the meaning.

The category `String` corresponds to strings of words which are treated as non-linguistically analyzable. For example, in the sentence "I accidentally typed 'svdfdsfjhg'", the string 'svdfdsfjhg' is not a word of any normal sort like you'd find in a dictionary but rather a quoted string, but it still behaves like a definite noun phrase (similar to names like "John"). Expressions with the category `String` must have the semantic type `String`.

The category former `\` (said "under") represents a syntactic function: an expression with category `A\B` combines with an expression of category `A` to its left to form an expression of category `B`. For instance, the verb "runs" can be given the category `D[...]\S[...]`, with appropriate features, whereupon it combines with noun phrases (= `D[...]`s, or DPs in mainstream parlance) like "the man" to its left, to form sentences phrases like "the man runs". An expression with the category `A\B` must have the semantic type `A' -> B'`, where `A'` is the semantic type that corresponds to syntactic `A`, and `B'` to `B`.

The category former `/` (said "over") represents a syntactic function: an expression with category `B/A` combines with an expression of category `A` to its right to form an expression of category `B`. For instance, the word "the" can be given the category `D[...]/N[...]`, with appropriate features, whereupon it combines with nouns like "file" to its right, to form noun phrases like "the file". An expression with the category `B/A` must have the semantic type `A' -> B'`, where `A'` is the semantic type that corresponds to syntactic `A`, and `B'` to `B`.

The category former `>` represents a syntactic function formed by process of displacement (or WH movement, in mainstream parlance), and is typically found in the categories of fronted WH operators. You will probably not need to use this.

Categories and features can also be variables. This is used to represent polymorphism (analogous to Haskell's polymorphism). Variable come in two flavors, the dummy variable `_` which matches anything and doesn't bind a value, and `_n` which binds a value. So for example the word "see" might be given the category `itv/D[_,_,acc,_]` because it takes a direct object that's accusative case, but it doesn't care what the number, person, or WH status is. On the other hand, the word "and" might be given the category `(_0\_0)/_0` because for any two things of the same category, it can combine them to form a new thing of the same category (e.g. combining two nouns to form a conjoined noun, or combining two verbs to form a conjoined verb). The numbers on non-dummy variables make it possible for a category or feature to be bound to the variable, which all other occurrences of the variable then share. So combining `(_0\_0)/_0` with an `N[fs]` to its right produces a `N[fs]\N[fs]`, while combining it with an `S[fs]` produces a `S[fs]\S[fs]`.