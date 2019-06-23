## FAQ

#### Q1: Why is your code indentation so ugly?

From my experience this is caused by the infamous tabs vs. spaces issue. 

Let me know where and I'll fix it.

___

#### Q2: Your so called refactored solutions are much longer than the ones on LeetCode. What up with that? Isn't long equals bad?

*Some* of the refactored solutions are indeed longer. You know what, even if *all* of them were longer I'd still use the same argument below. Plus, if you do `"long".equals("bad")` in Java, the answer is actually `false` so we're good.

IMHO the refactored versions are at least as `2-4x` clearer as they are longer.

A solution being shorter, does not necessarily mean it is easier to produce during an interview or that it is otherwise better (in terms of software properties). I am a huge fan of concise code, but not on account of clarity. In fact, I'd probably argue that it is often the other way around. 

Longer clear solutions may very well be easier to reproduce since they reflect an intuitive mental model of the solution. If we did a good at breaking the solution down into building blocks, knowing the major ones is hopefully enough to complete the remaining ones on demand.

That said, short (yet convoluted) solutions may be easier to *memorize*. I do believe that poetry and code have much in common, but while memorizing the former will make you look sophisticated, memorizing the latter will make you look `null`.

---

#### Q3: It's hard to come up with clear and concise solutions on the spot, how did you do that?

I'm afraid I didn't.

The solutions I present are the result of grinding the problem over and over again, experimenting with all kinds of code changes, and inspecting whether applying them results in an improved code, or just a different one. The process of making code clearer is iterative. Some changes unlock certain insights, which lead to more changes, which unlock further insights. See also [SB Changes by Kent Beck](https://medium.com/@kentbeck_7670/bs-changes-e574bc396aaa). 

Personally I think it's another insane blind spot present during whiteboard interviews. Engineers do not get the chance to perform this iterative improvement process and must therefore produce the first-ish code they think of, to fit the time (and pressure) constraints. The first-ish code that comes to mind is often neither clear nor correct, unless producing this particular code has been drilled to death. In which case what the interview really checks is how well one produces LeetCode solutions rather than how good of an engineer she or he really is (a.k.a. the corporate whiteboard interview tragedy).

---



