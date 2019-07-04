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

In my view iterative improvement requires at least two resources:

1. Time, due to obvious reasons.
2. An IDE, since many changes are done using automatic refactoring. Doing this kind of changes by hand can be tricky and error prone.

During a whiteboard interview people have neither, which I believe is a shame (literally). 

The first-ish code that comes to mind is often neither clear nor correct, unless producing this particular code has been drilled to death. In which case what the interview really checks is how well one produces LeetCode solutions from memory rather than how good of a software engineer they really are (a.k.a. *the corporate whiteboard interview tragedy*).

---

#### Why do you keep saying *corporate whiteboard interview tragedy*?

I think that the *corporate* and the *whiteboard interview* parts are pretty clear.

As for the tragedy, well… in my personal view whiteboard interviews have a number of significant flaws:

1. Unrepresentative Context
   - It's extremely difficult to come up with algorithms on the spot, so one needs to know them. Many (if not most) of today's software engineers are not well trained algorithm masters simply because their daily tasks revolve around entirely different challenges. The very same challenges that they will go back to dealing with once (if) they get past the whiteboard interviews and start their *actual job*.
2. Unrepresentative Method
   - Online resources are not available during whiteboard interviews. Yet today's software engineering is deeply rooted in being able to acquire knowledge on demand, typically from online resources. The fact one doesn't know something right now may change in a few hours, and change back again in a week or so time. In and out.
3. Questionable KPIs
   - A correlation between one's ability to do well in whiteboard interviews and one's competence as a software engineer is yet to be established. It could be that people who do well in whiteboard interviews have a great memory, or great puzzle solving skills, but that is not what necessarily constitutes a competent software engineer.
   - If evidence of such a correlation does exist, it's a shame no data was made publicly available. Or at least I wasn't able to get my hands on it.
4. Inconsistent
   * Chance and luck are great factors in whiteboard interviews. A number of FAANG employees I talked to, said they were unsure of whether they would have made the cut had they re-interviewed. I would expect engineering interviews to be a little more consistent and chance resilient.
5. Juniority Bias
   - Senior software engineers who spent a while working in the industry have all but forgotten how quicksort works (Quickly? Because otherwise it was a poor choice of name), or what it takes to construct a heap. Graduates coming straight from college may have these subjects present in their cache.
   - There's also a physiological factor here, if you take a software engineer who has been practicing their craft for 20 years and ask them to study the magic behind 3Sum, 8-queens, and other interview gems, well, they may not be all that happy to do so.



Many justify whiteboard interviews by saying they allow software companies to scale.

> "**To scale**, Facebook is willing to take some false negatives and we both pay the price for it in this quadrant."

A  [post](https://www.facebook.com/notes/kent-beck/fear-leads-to-anger-primary-and-secondary-emotions/1708089742557216/) Kent Beck wrote after he had been asked to take what I assume was a whiteboard interview at Facebook. 

> best move--pulling out my 25-year-old Prolog skillz to solve 8 Queens

[Another evidence of that lovely experience](https://www.quora.com/How-was-Kent-Beck-recruited-to-Facebook) on Quora. 

If even the great [Kent Beck](https://en.wikipedia.org/wiki/Kent_Beck) fell casualty to the corporate whiteboard interview tragedy, what should we mortals do?

Somehow the assumption that whiteboard interviews allow software companies to efficiently scale has become an axiom, and the status quo, embraced by many tech companies worldwide. Arguments like "Google uses whiteboard interviews; Google scaled well and is very successful; ergo, whiteboard interviews help software companies scale well and be successful" are just not how causality works.

In light of the considerable issues above, I would expect to find scientific evidence supporting the superiority of whiteboard interviews over other interview methods. However, such evidence is nowhere to be found. At least I couldn't find any, if you come across anything of that sort please do share.

---

#### Why is your code indentation so ugly?

Could be a [tabs vs. spaces issue](https://youtu.be/SsoOG6ZeyUI?t=37). Let me know and I'll fix it.