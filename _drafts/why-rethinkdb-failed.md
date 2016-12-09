---
layout: post
title:  "RethinkDB: why we failed"
---

When we [announced][shutdown-announcement] that RethinkDB is shutting
down, I promised to write a post-mortem. I took some time to process
the experience, and I can now write about it clearly.

In the [HN discussion thread][hn-discussion] people proposed many
reasons for why RethinkDB failed, from inexplicable perversity of
human nature and clever machinations of MongoDB's marketing people, to
failure to build an experienced go-to-market team, to lack of numeric
type support beyond 64-bit `float`. I aggregated the comments into a
list of proposed failure reasons [here][hn-failure-reasons].

Some of these reasons have a ring of truth to them, but they're
symptoms rather than causes. For example, saying that we failed to
monetize is tautological. It doesn't illuminate the reasons for *why*
we failed.

In hindsight, two things went wrong -- we picked a terrible market and
optimized the product for the wrong metrics of goodness. Each mistake
likely cut RethinkDB's valuation by one to two orders of magnitude. So
if we got either of these right, RethinkDB would have been the size of
MongoDB, and if we got both of them right, we eventually could have
been the size of RedHat[1].

# Terrible market

Our thinking went something like this. New companies aren't getting
built on top of Oracle, so there is a window of opportunity to build a
new infrastructure company. The database market is huge. If we build a
product that captures some of that market, we'll end up building a
very successful company.

Unfortunately you're not in the market you think you're in -- you're
in the market *your users* think you're in. And our users clearly
thought of us as an open-source developer tools company, because
that's what we really were. Which turned out to be very unfortunate,
because the open-source developer tools market is one of the [worst
markets][another-redhat] one could possibly [end up
in][dev-tools-market]. Thousands of people used RethinkDB, often in
business contexts, but most were willing to pay less for the lifetime
of usage than the price of a single Starbucks coffee (which is to say,
they weren't willing to pay anything at all).

This wasn't because the product was so good people didn't need to pay
for support, or because developers don't control budgets, or because
of failure of capitalism. The answer is basic
microeconomics. Developers love building developer tools, often for
free. So while there is massive demand, the supply vastly outstrips
it. This [drives][five-forces] the number of alternatives up, and the
prices down to zero.

To see how this plays out for other companies consider MongoDB (valued
at roughly $1.6B with ~700 employees), and Docker (valued at roughly
$1B with ~300 employees). Both companies completely dominate in their
respective markets. Two very rough rules of thumb for private growth
stage technology companies is that valuations are a 10x multiple of
annual revenue, and that [revenue per employee][estimate-revenue] is
around $200K/year. Which means that MongoDB's annual revenue is around
$140-$160M, and Docker's annual revenue is around $60-$100M.

That looks pretty good, until you look at dominant B2B technology
companies in markets that *aren't* developer tools. Companies like
SalesForce, or Palantir, or Box (which faces stiff competition). All
of a sudden MongoDB and Docker start looking tiny.

And these are massive successes. If relatively established companies
with existing partnerships, distribution infrastructure, and access to
large accounts are having trouble growing, what does it mean for a
startup in its germination stage?

For us, it meant an intractable customer acquisition funnel. If a
startup in a fertile B2B market has to process a hundred leads to get
to ten opportunities to get to a single sale, for a developer tools
startup that number goes up 10x. You have access to plenty of high
quality prospects -- lots of people are downloading your product and
engaging with you, but you have to burn through a ridiculous number of
leads to converge to a single sale.

This has disastrous domino effects. It demoralizes the team, and
makes it very challenging to attract investment and hire top
talent. In turn, that constrains your resources so you can't make
sufficient investment in product and distribution. Startups live and
die by momentum, and early distribution challenges almost always doom
you to eventual death.

# Wrong metrics of goodness

Ok, so the market is bad, but other developer tools companies are
still selling a lot of product. Why not RethinkDB?

While we couldn't do anything about the dynamics of the market (other
than building something else), the product decisions were entirely
within our control. We wanted to build an elegant, robust, and
beautiful product, so we optimized for the following metrics:

- __Correctness.__ We made very strict guarantees, and
  [fulfilled][jepsen1] them [religiously][jepsen2].
- __Simplicity of the interface.__ We took on most of the complexity
  in the implementation, so application developers wouldn't have to.
- __Consistency.__ We made everything from the query language, to the
  client drivers, to cluster configuration, to documentation, to the
  marketing copy on the front page as consistent as possible.

If these trade-offs seem familiar, they're straight from the [worse is
better][worse-is-better] essay. It turned out that correctness,
simplicity of the interface, and consistency are the wrong [metrics of
goodness][worse-is-worse] for most users. The majority of users wanted
these three trade-offs instead:

- __Timely arrival.__ They wanted the product to actually exist when
  they needed it, not three years later.
- __Palpable speed.__ People wanted RethinkDB to be fast on workloads
  they *actually tried*, rather than "real world" workloads we
  suggested. For example, they'd write quick scripts to measure how
  long it takes to insert ten thousand documents without ever reading
  them back. MongoDB mastered these workloads brilliantly, while we
  fought the losing battle of educating the market.
- __A use case.__ We set out to build *a good database system*, but
  users wanted *a good way to do X* (e.g. a good way to store JSON
  documents from hapi, a good way to store and analyze logs, a good
  way to create reports, etc.)

It's not that we didn't try to ship quickly, make RethinkDB fast, and
build the ecosystem around it to make doing useful work easy. We
did. But correct, simple, and consistent software takes a very long
time to build. That put us three years behind the market.

By the time we felt RethinkDB satisfied our design goals and we were
confident enough to recommend it to be used in production, almost
everyone was asking "how is RethinkDB different from MongoDB?" We
worked hard to explain why correctness, simplicity, and consistency
are important, but ultimately these weren't the metrics of goodness
that mattered to most users.

To be honest, it hurt. It hurt a lot. It was unfathomable to us why
people would choose a system that barely does the thing it's supposed
to do (store data), has a big kernel lock, throws away errors at
random, implements single node features that stop working when you
shard, has a barely working sharding system despite it being one of
the core features of the product, provides essentially no correctness
guarantees, and exposes a hodge-podge of interfaces that have no
discernible consistency or unity of vision.

Every time MongoDB shipped a new release and people congratulated them
on making improvements, I felt pangs of resentment. They'd announce
they fixed the BKL, but really they'd get the granularity level down
from a database to a collection. They'd add more operations, but
instead of a composable interface that fits with the rest of the
system, they'd simply bolt on one-off commands. They'd make sharding
improvements, but it was obvious they were unwilling or unable to make
even rudimentary data consistency guarantees.

But over time I learned to appreciate the wisdom of the
crowds. MongoDB turned regular developers into heroes *when people
needed it*, not years after the fact. It made data storage fast, and
let people ship products quickly. And over time, MongoDB grew up. One
by one, they fixed the issues with the architecture, and now it is an
excellent product. It may not be as beautiful as we would have wanted,
but it does the job, and it does it well.

When it became clear in mid-2014 that we couldn't compete, we worked
hard to differentiate from MongoDB. We found a very elegant way to add
[realtime push][realtime-push], hoping to enable developers to build a
generation of apps they couldn't build before. But that wasn't
enough. Suddenly we found ourselves competing with Meteor and
Firebase, companies that were dedicated to solving the realtime
problem for years before we even thought of it. Again we were three
years behind the market, and again we found ourselves unable to
compete.

# What about the cloud?

A few people suggested that we should have built a cloud offering. We
actually did have one in the works, so it's an interesting topic I'd
like to cover.

The obvious problem with a small database company building a cloud
service is that it pattern matches to a common startup failure mode --
splitting focus. Building, shipping, and operating reliable
multi-tenant cloud services is hard. It requires non-trivial expertise
and resources, so if you go down that path you find yourself running
two startups at once. But we were facing an existential threat and
were rapidly running out of options, so we gave it a shot
anyway. Let's suppose for the moment we could have pulled it off.

Our reasoning went like this. A database cloud offering could mean one
of three things: managed hosting, database as a service (DaaS), or
value-added platform as a service (PaaS). Let's do a quick back of the
napkin market analysis using a $200K/employee in annual revenue [rule
of thumb][estimate-revenue] we used above:

|                 |  Managed Hosting |   DaaS  |       PaaS              |
|:----------------|:----------------:|:-------:|:-----------------------:|
| __Company__     | Compose.io, mLab | FaunaDB | Parse, Firebase, Meteor |
| __Employees__   |        ~30       |   ~30   |       ~30               |
| __Revenue__     |      < $10M      |  < $10M |      < $10M             |

So these markets are small, even smaller than the database market
itself. But could one of them be a better bet than others?

Managed hosting is essentially running the database for people on AWS
so they don't have to. The alternative to using these services is
setting up the database on AWS yourself. That's a pain, but it isn't
actually *that* hard. So there is a very hard cap on how much managed
database hosting services can charge. Considering that Compose.io and
mLab are offering MongoDB which has one to two orders of magnitude
more users than RethinkDB, we reasoned that offering managed hosting
wouldn't make a dent.

Database as a service is a more complex version of managed hosting --
DaaS offerings abstract node management from the user entirely. You
simply run your queries and the system handles them. You don't know
anything about how many nodes are run under the hood. This business is
very challenging -- partly because DaaS companies have to compete with
the giants (e.g. DynamoDB and DocumentDB), and partly because
customers are very resistant to completely hand off data management to
a startup when there are so many other substitutes and alternatives
(do *you* know anyone who uses a DaaS offering from a startup?) So a
DaaS offering was out.

The last option was to build a value-added platform as a service. We
thought this was a promising direction because here we had a massive
technical advantage. Firebase and Meteor had to built
application-level realtime logic on top of MongoDB, which
fundamentally limits the realtime querying capabilities and
performance at scale. On the other hand, we controlled the stack all
the way down, so we could offer significant advantages Firebase and
Meteor couldn't build.

So we built [Horizon][horizon] and started working on Horizon Cloud --
a way for users to deploy and scale RethinkDB/Horizon apps. The
challenges of building three large projects (RethinkDB, Horizon, and
Horizon Cloud) with a very small team eventually caught up with us,
and we never managed to ship the cloud offering before we ran out of
money. Kudos to the engineering team, though. They came very, very
close.

# Meta questions

There is one more level of root cause analysis that we can do. Why did
we pick a bad market and optimize the product for the wrong metrics?

When I was a little kid I wanted to build my own radio. I made a box
out of plywood, threw some metal junk inside, and connected the box to
a power cord. I had books on electronics at home, but didn't think I
needed them -- I had unwavering faith that I could do it on my
own. Eventually I did build a working receiver, but it took me years
to finally realize I needed to learn basic electronics.

Early RethinkDB was quite a bit like that. We had no intuition for
products or markets, so we'd go through the motions of building a
company without actually understanding what we were doing. What's
more, we had enormous [optimism bias][rationality]. Just like
physicians know that gifts from pharmaceutical companies have biasing
effects for other physicians but believe they are immune from the
effect, we believed we were immune from the laws of economics and the
math of running a business. The math, of course, eventually caught up
with us.

Could we have done anything to avoid these mistakes? Not any more than
I could have built a working radio as a little kid. We were
unconsciously incompetent, and it took years for that incompetence to
become conscious.

A few people pointed out that we would have done better if we had
built an experienced go-to-market team. That's 100% true, but the
timing of our personal development didn't line up with the needs of
the company. Initially we didn't know we needed go-to-market
expertise, so we didn't seek to include it on the founding team[2]. By
the time we built up a mental model that maps well to reality, we
found ourselves short on cash, in a difficult market filled with
capable competitors, and a product that's three years behind. By then,
the best go-to-market team in the world couldn't have saved us.

# Parting thoughts

Many people have very strong feelings about the developer tools
market. Engineers love building developer tools, so they badly want
developer tools companies to thrive.

I am hesitant to dismiss the market entirely -- partly because I don't
want to generalize from a single experience, partly because I don't
like saying "it cannot be done", and partly because there are quite a
few exceptions. GitHub, MongoDB, and Docker have built formidable
companies. GitLab and Unity seem to be doing well.

If you do set out to build a developer tools company, tread
carefully. The market is filled with good alternatives. User
expectations are high and prices are low. Think deeply about the value
you're offering to the customer. Remember -- wanting the world to be a
certain way doesn't make it so.

In 2009, we were pitching the early idea for RethinkDB (we had no
software yet) to an audience of investors at the YCombinator demo
day. We ended the pitch with a slide of three key points to
remember. "If you only remember three things about RethinkDB," we
said, "remember these." It worked. People didn't remember anything
else about the pitch, but they did remember the three points at the
end.

I'll now leave you with three key points to remember. If you remember
anything about this post, remember these:

- Pick a large market but build for specific users.
- Learn to recognize the talents you're missing, then work like hell
  to get them on your team.
- Read [The Economist], [Poor Charlie's Almanack], [Thinking Fast and
  Slow], and [HPMOR].

---

_[1] Don't read into these numbers too closely. I'm ball-parking it,
but it should give you a general idea of the cost of these mistakes._

_[2] Incidentally, recognizing good business people without having
strong business intuition is about as hard as recognizing good
engineers without having a strong intuition for engineering._

[shutdown-announcement]: https://rethinkdb.com/blog/rethinkdb-shutdown/
[hn-discussion]: https://news.ycombinator.com/item?id=12649414
[hn-failure-reasons]: https://gist.github.com/coffeemug/af8dcb6f653a7f9c31daedbbdaa3402c
[dev-tools-market]: https://techcrunch.com/2014/08/22/will-developer-tools-startups-ever-find-investors/
[five-forces]: https://en.wikipedia.org/wiki/Porter's_five_forces_analysis
[another-redhat]: https://techcrunch.com/2014/02/13/please-dont-tell-me-you-want-to-be-the-next-red-hat/
[estimate-revenue]: https://www.saastr.com/how-to-figure-out-your-competitors-revenues-in-about-70-seconds/
[jepsen1]: https://aphyr.com/posts/329-jepsen-rethinkdb-2-1-5
[jepsen2]: https://aphyr.com/posts/330-jepsen-rethinkdb-2-2-3-reconfiguration
[worse-is-better]: http://dreamsongs.com/RiseOfWorseIsBetter.html
[worse-is-worse]: http://www.artima.com/weblogs/viewpost.jsp?thread=24807
[realtime-push]: https://rethinkdb.com/blog/1.16-release/
[horizon]: http://horizon.io/
[rationality]: https://doc.research-and-analytics.csfb.com/docView?document_id=1048541371&serialid=mofPYk1Y4WanTeErbeMtPx6ur0SCIcSlaZ7sKGPdQQU%3D
[The Economist]: http://www.economist.com/
[Poor Charlie's Almanack]: https://www.amazon.com/Poor-Charlies-Almanack-Charles-Expanded/dp/1578645018
[Thinking Fast and Slow]: https://www.amazon.com/Thinking-Fast-Slow-Daniel-Kahneman/dp/0374533555
[HPMOR]: http://hpmor.com/
