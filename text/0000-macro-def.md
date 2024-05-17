- Feature Name: macro_def
- Start Date: 2024-05-20
- RFC PR: [crystal-lang/rfcs#0000](https://github.com/crystal-lang/rfcs/pull/0000)
- Issue: [crsytal-lang/crystal#8835](https://github.com/crystal-lang/crystal/issues/8835)

# Summary

Currently macros only allow a fixed set of macro methods to be called. It is not possible for a user (or the stdlib!) to defined new ones. In essence, what this RFC proposes is to allow ASTNode to ASTNode definitions of macros, as exemplified in the following examples (extracted from [#8835](https://github.com/crystal-lang/crystal/issues/8835)).

```cr
macro def foo(x : StringLiteral) : StringLiteral
  "foo#{x}"
end

macro bar
  {{ foo "x" }}
end

bar # => "foox"
```

# Motivation

The motivation is to be able to have DRY code at the macro level. One example from the stdlib itself is mentioned by [@HertzDevil](https://github.com/crystal-lang/crystal/issues/8835#issuecomment-1084636061):

```cr
macro class ArrayLiteral
  @[Primitive(...)]
  macro def each_with_index(&); end

  @[Primitive(...)]
  macro def size; end

  macro def empty?
    # note: implicit `self`
    size == 0
  end
end
```

This code should:

1. Open the `Crystal::Macros::ArrayLiteral` class.
2. Add the `Primitive` methods `size` and `each_with_index`, the ones that exist today, for doc purposes.
3. Generate a `macro def` named `empty?`.

Then, any call to `ArrayLiteral#empty?` will expand the code from its definition.

Benefits:

1. Reduces the code required to interpret macros.
2. Incorporate in macros the same mechanism for regular primitives in docs.
3. Let users define their own macro definitions.

# Guide-level explanation

Explain the proposal as if it was already included in the language and you were teaching it to another Crystal programmer. That generally means:

- Introducing new named concepts.
- Explaining the feature largely in terms of examples.
- Explaining how Crystal programmers should *think* about the feature, and how it should impact the way they use Crystal. It should explain the impact as concretely as possible.
- If applicable, provide sample error messages, deprecation warnings, or migration guidance.
- If applicable, describe the differences between teaching this to existing Crystal programmers and new Crystal programmers.
- If applicable, discuss how this impacts the ability to read, understand, and maintain Crystal code. Code is read and modified far more often than written; will the proposed feature make code easier to maintain?

For implementation-oriented RFCs (e.g. for compiler internals), this section should focus on how compiler contributors should think about the change, and give examples of its concrete impact. For policy RFCs, this section should provide an example-driven introduction to the policy, and explain its impact in concrete terms.

# Reference-level explanation

This is the technical portion of the RFC. Explain the design in sufficient detail that:

- Its interaction with other features is clear.
- It is reasonably clear how the feature would be implemented.
- Corner cases are dissected by example.

The section should return to the examples given in the previous section, and explain more fully how the detailed proposal makes those examples work.

# Drawbacks

Why should we *not* do this?

# Rationale and alternatives

- Why is this design the best in the space of possible designs?
- What other designs have been considered and what is the rationale for not choosing them?
- What is the impact of not doing this?
- If this is a language proposal, could this be done in a library instead? Does the proposed change make Crystal code easier or harder to read, understand, and maintain?

# Prior art

Discuss prior art, both the good and the bad, in relation to this proposal.
A few examples of what this can include are:

- For language, library, tools, and compiler proposals: Does this feature exist in other programming languages and what experience have their community had?
- For community proposals: Is this done by some other community and what were their experiences with it?
- Papers: Are there any published papers or great posts that discuss this? If you have some relevant papers to refer to, this can serve as a more detailed theoretical background.

This section is intended to encourage you as an author to think about the lessons from other languages, provide readers of your RFC with a fuller picture.
If there is no prior art, that is fine - your ideas are interesting to us whether they are brand new or if it is an adaptation from other languages.

Note that while precedent set by other languages is some motivation, it does not on its own motivate an RFC.
Please also take into consideration that Crystal sometimes intentionally diverges from common language features.

# Unresolved questions

- What parts of the design do you expect to resolve through the RFC process before this gets merged?
- What parts of the design do you expect to resolve through the implementation of this feature before stabilization?
- What related issues do you consider out of scope for this RFC that could be addressed in the future independently of the solution that comes out of this RFC?

# Future possibilities

Think about what the natural extension and evolution of your proposal would
be and how it would affect the language and project as a whole in a holistic
way. Try to use this section as a tool to more fully consider all possible
interactions with the project and language in your proposal.
Also consider how this all fits into the roadmap for the project
and of the relevant sub-team.

This is also a good place to "dump ideas", if they are out of scope for the
RFC you are writing but otherwise related.

If you have tried and cannot think of any future possibilities,
you may simply state that you cannot think of anything.

Note that having something written down in the future-possibilities section
is not a reason to accept the current or a future RFC; such notes should be
in the section on motivation or rationale in this or subsequent RFCs.
The section merely provides additional information.
