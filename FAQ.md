## FAQ

#### Why is it called LeetCode refactored?

> "Refactoring is the process of changing a software system in such a way that it does not alter the external behavior of the code yet improves its internal structure." 
>
> --Martin Fowler, "Refactoring: Improving the Design of Existing Code"

The original solutions (code copy-pasted from LeetCode's discussion section) all boil down to a single method performing a certain task.

The alternative solutions presented here retain the original methods' signatures, yet introduce changes to the internal structure of the code. This fits the above definition perfectly, and therefore:

*LeetCode Refactored*.

---
#### Your so called refactored solutions are much longer than the ones on LeetCode. What up with that? Doesn't long equal bad?

*Some* of the refactored solutions are indeed longer. Even if *all* of them were longer I would still use the same argument👇. Anyway, if you do `"long".equals("bad")` in Java, the answer is actually `false` so we're good.

IMHO the refactored versions are at least as `2-4x` clearer as they are longer.

A solution being shorter, does not necessarily mean it is easier to produce during an interview or that it is otherwise better (in terms of software properties). I am a huge fan of concise code, but not on account of clarity. In fact, I'd probably argue that it is often the other way around. 

Long and clear solutions may very well be easier to reproduce since they reflect an intuitive mental model of the solution. If we did a good at breaking the solution down into building blocks, knowing the major ones is hopefully enough to complete the remaining ones on demand.

That said, short and convoluted solutions may be easier to *memorize*. I do believe that poetry and code have much in common, but while memorizing the former will make you look sophisticated, memorizing the latter will make you look `null`.

---

#### It's hard to come up with clear and concise solutions on the spot, how did you do that?

I most definitely did not.

The solutions I present are the result of grinding the problem over and over again, experimenting with all sorts of code changes, and inspecting whether applying them results in an improved code, or just a different one. The process of making code clearer is iterative. Some changes may unlock certain insights, which then lead to more changes, which then unlock further insights. This is was not a write-once code. See also this [recent tweet by Kent Beck](https://twitter.com/KentBeck/status/1144570824692776960) and also his post about [SB Changes](https://medium.com/@kentbeck_7670/bs-changes-e574bc396aaa). 

Personally I think it's another huge blind spot during whiteboard interviews. Engineers do not get the chance to perform this iterative improvement process and must therefore produce the first-ish code they think of, in order to fit the time (and pressure) constraints. The first-ish code that comes to mind is often neither clear nor correct, unless producing this particular code has been drilled to death. In which case what the interview really checks is how well one produces LeetCode solutions rather than how good of a software engineer they really are (a.k.a. the corporate whiteboard interview tragedy).

---

#### Why is your code indentation so ugly?

Could be a [tabs vs. spaces issue](https://youtu.be/SsoOG6ZeyUI?t=37). Let me know and I'll fix it.