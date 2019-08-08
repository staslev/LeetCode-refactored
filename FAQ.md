## FAQ

#### Why is code readability so important?

Or put another way, "Why should anyone care about code readability?".

There is an enormous amount of material on this subject, ranging from [academic papers](https://scholar.google.com/scholar?hl=en&as_sdt=0%2C5&q=code+readability&btnG=) to [posts and essays on the web](https://www.google.com/search?q=why+is+code+readability+important). 

If I had to put it all into one sentence I would probably say that it all boils down to code readability having an immense effect on how easy it is to perform two extremely important software activities: introduce new features, and fix bugs. In fact, one of [Lehman's Laws](https://en.wikipedia.org/wiki/Lehman%27s_laws_of_software_evolution) states that a system must be continually adapted or it becomes progressively less satisfactory. 

In other words, if you can't adapt your software (e.g., by fixing bugs and introducing new features), your software will become less and less satisfactory. That's actually a big deal, since if your software is "less and less satisfactory", users are likely to start ditching it. Nowadays, users equal money, so the math is pretty clear at this point.

---

#### Why is it called LeetCode refactored?

> "Refactoring is the process of changing a software system in such a way that it does not alter the external behavior of the code yet improves its internal structure." 
>
> --Martin Fowler, "Refactoring: Improving the Design of Existing Code"

The original solutions (code copy-pasted up to curlies and whitespaces, from LeetCode's discussion section) all boil down to a single method performing a certain task. The alternative solutions presented here retain the original methods' signatures, yet introduce changes to the internal structure of the code. This fits the above definition perfectly, and therefore:

*LeetCode Refactored*.

---
#### Does the world really need another LeetCode solution set?

Probably not. Who knows what the world really needs these days.

What I have noticed though, is that existing solution sets, and [there're plenty of them on GitHub](https://lmgtfy.com/?q=leetcode+solutions+github), tend to focus purely on the algorithmic nature of the problem. Consequently, I believe many of them leave much to be desired as far as *software engineering* goes, readability (and therefore also maintainability) in particular.

It could actually be funny, if it wasn't  so tragic, because I assume most of the people who wind up on [LeetCode](https://leetcode.com/) probably do so in order to prepare for an interview for a software engineering position, in the scope of which they will be practicing software engineering. Corporate whiteboard interviewers tend to put little emphasis on checking for software engineering qualities in favour of whiteboard skills, resulting in a process optimised for the survival of whiteboard engineers.

It never ceases to amaze me, how often, and to what extent, common solutions present code that would fail to pass a typical code review. Now, I'm by no means saying that these solutions aren't clever, or even occasionally genius, what I'm saying is that things like code structure & readability seem to be the first casualties in the *corporate whiteboard interview tragedy*.

Personally I happen to believe in software engineering practices, and wish more of them could be taken into account when educating and interviewing software engineers. 

---

#### Your so called refactored solutions are much longer than the ones on LeetCode. What up with that? Doesn't long equal bad?

*Some* of the refactored solutions are indeed longer. Even if *all* of them were longer I would still use the same argument (below👇). Anyway, if you do `"long".equals("bad")` in Java, the answer is actually `false` so we're good.

IMHO the refactored versions are at least as `2-4x` clearer as they are longer.

A solution being shorter, does not necessarily mean it is easier to produce during an interview or that it is otherwise better (in terms of software properties). I am a huge fan of concise code, but not on account of clarity.

Long and clear solutions may very well be easier to reproduce since they reflect an intuitive mental model of the solution. If we did a good job at breaking the solution down into building blocks, knowing the major ones should (hopefully) be enough to complete the remaining parts on demand.

That said, short and convoluted solutions may be easier to *memorize*. I do believe that poetry and code have much in common, but while memorizing the former will make you look sophisticated, memorizing the latter will make you look `null`.

---

#### Your so called refactored solutions are not as performant as the original ones, why would I want that?

Abstraction and performance have always been somewhat conflicting aspects. By introducing abstractions we usually hurt performance at some level. Oftentimes the performance hit is so negligible it's not even worth thinking about.
In some cases however, say if you're working on the Linux kernel, a NASA project, Aviation software, or other mission critical systems, it does make a difference and should be carefully considered.

In any case, a performance penalty should be weighted against the long term maintainability of the code. A penalty of 10 milliseconds once in a blue moon may make sense in some systems, if the gain is a more maintainable code. The same penalty in a realtime mission critical system could result in people dying, which can hardly be justified by any degree of code maintability.

---

#### It's hard to come up with clear and concise solutions on the spot, how did you do that?

I most definitely did not.

The solutions I present are the result of grinding the problem over and over again, experimenting with all sorts of code changes, and inspecting whether applying them results in an improved code, or just a different one. The process of making code clearer is iterative. Some changes may unlock certain insights, which then lead to more changes, which then unlock further insights. The solutions presented here were never a write-once code. See also this [recent tweet by Kent Beck](https://twitter.com/KentBeck/status/1144570824692776960) and also his post about [SB Changes](https://medium.com/@kentbeck_7670/bs-changes-e574bc396aaa). 

In my view iterative improvement requires at least two resources:

1. Time
   * Because it takes time for the wheels to turn.
2. An IDE
   * Because that's where code is written, y'all. And because automatic refactoring is a must. Performing this kind of changes manually is extremely error prone and is essentially asking for trouble.

During whiteboard interviews interviewees typically have neither, which I believe is a shame (literally). 

The first-ish code that comes to mind is often neither clear nor correct, unless producing this particular code has been drilled to death. In which case what the interview really checks is how well one produces LeetCode solutions from memory rather than how good of a software engineer they really are (a.k.a. *the corporate whiteboard interview tragedy*).

---

#### Why do you keep repeating the phrase *corporate whiteboard interview tragedy*?

I think that the *corporate* and the *whiteboard interview* parts are pretty clear.

As for the tragedy, well… in my personal view whiteboard interviews have a number of significant flaws which tend to make human beings suffer:

1. Unrepresentative Context
   - It's extremely difficult to come up with algorithms on the spot, so one needs to know them. Many (if not most) of today's software engineers are not well trained algorithm masters simply because their daily tasks revolve around entirely different challenges. The very same challenges that they will go back to dealing with once (if) they get past the whiteboard interviews and start their *actual job*.
2. Unrepresentative Method
   - Online resources are not available during whiteboard interviews. Yet today's software engineering is deeply rooted in being able to acquire knowledge on demand, typically from online resources. The fact one doesn't know something right now may change in a few hours, and change back again in a week or so time. In and out.
3. Questionable KPIs
   - A correlation between one's ability to do well in whiteboard interviews and one's competence as a software engineer is yet to be established. It could be that people who do well in whiteboard interviews have a great memory, or great puzzle solving skills, but that is not what necessarily constitutes a competent software engineer.
   - If evidence of such a correlation does exist, it's a shame no data was made publicly available. Or at least I wasn't able to get my hands on it.
4. Inconsistent Results
   * Chance and luck are great factors in whiteboard interviews. A number of FAANG employees I talked to, said they were unsure of whether they would have made the cut had they re-interviewed. I would expect engineering interviews to be a little more consistent and chance resilient than "oh, good thing they asked me that question I stumbled upon just the other day".
5. Juniority Bias
   - Senior software engineers who spent a while working in the industry have all but forgotten how quicksort works (Quickly? Because otherwise it was a poor choice of name), or what it takes to construct a heap. Graduates coming straight from college may have these subjects present in their cache.
   - There's also a physiological factor here, if you take a software engineer who has been practicing their craft for 20 years and ask them to study the magic behind 3Sum, 8-queens, and other interview gems, well, they may not be all that happy to do so.



Many justify whiteboard interviews by saying they allow software companies to scale.

> "**To scale**, Facebook is willing to take some false negatives and we both pay the price for it in this quadrant."

A  [post](https://www.facebook.com/notes/kent-beck/fear-leads-to-anger-primary-and-secondary-emotions/1708089742557216/) Kent Beck wrote after he had been asked to take what I assume was a whiteboard interview at Facebook. 

> best move--pulling out my 25-year-old Prolog skillz to solve 8 Queens

[That's another evidence of that lovely experience](https://www.quora.com/How-was-Kent-Beck-recruited-to-Facebook) on Quora. 

If even the great [Kent Beck](https://en.wikipedia.org/wiki/Kent_Beck) fell casualty to the corporate whiteboard interview tragedy, what should we mortals do?

Somehow the assumption that whiteboard interviews allow software companies to efficiently scale has become an axiom, and the status quo, embraced by many tech companies worldwide. Arguments like "Google uses whiteboard interviews; Google scaled well and is very successful; ergo, whiteboard interviews help software companies scale well and be successful" are just not how causality works.

In light of the considerable issues above, I would expect to find scientific evidence supporting the superiority of whiteboard interviews over other interview methods. However, such evidence is nowhere to be found. At least I couldn't find any, if you come across anything of that sort please do share.

---

#### Why is your code indentation so ugly?

Could be a [tabs vs. spaces issue](https://youtu.be/SsoOG6ZeyUI?t=37). Let me know and I'll fix it.