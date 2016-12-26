ElmahR is dead, long live RElmah!
====

[ElmahR](http://elamhr.apphb.com) is my pet project, although I must say so far [it's been much more successful than expected](http://www.nuget.org/packages?q=elmahr). It was really born as a playground to test and learn some new stuff, but it caught momentum, and it even received some contributions. It has about 30 forks on Bitbucket, and a few collateral projects using it or modding it have popped up. I'm pretty happy with all that :)

But... It started more than two years ago, and the way I built it while experimenting new things made it grow in a way which wasn't really following a plan. I tried to keep it tidy and simple, but honestly there are portions of the code which are not as clean as I'd like, and they would need some major rethinking. Also, several of the features that I pushed into it are not actually so useful, so why keep them? And finally, the world has been running fast, delivering new libraries, or new versions of existing ones, at a very high pace which I was not really able to cope with. SignalR is the main issue here, ElmahR is still using v1.x, and moving to v2.x would be a breaking change. Furthermore, the task of [writing a book](http://www.robychechi.it/roby/signalr-cookbook-ready-and-available-on-stores) did not really help with all that...

While I really want to keep ElmahR alive and actual, on the other hand the codebase really needs a lot to reorg to enable easy maintenance and addition of new needed features. There's no way I can do that while keeping backward compatibility and avoiding getting crazy...

So, I really see only one way to move forward: a reboot! And to make it clear that it's a real restart, I decided to rename it! It's a drastic decision, I love the ElmahR name and I'll miss it. Also, there's a concrete risk of loosing users with this move, and I'll try my best to prevent this from happening. But I think the reasons for doing such a move are stronger than the potential drawbacks. So be it: from ElmahR to **RElmah**!

Why **RElmah**?

*   It's a big reset, without backward compatibility, so I need to make that disconnect very clear. I do not want people to assume that a Nuget package update will automagically fix things...
*   It's a full redesign, which several concepts going away and several others added.
*   The name "ElmahR" was trying to express how the project was born (ElmahR = ELMAH + SignalR), whereas **RElmah** stands for "Reactive ELMAH", and it's putting the focus on the Reactive spirit this new version is willing to embrace.

So, I officially announce that the project is taking this direction, and in the next few months **RElmah** will gradually become available. The main features of this new project will be:

*   [Github](https://github.com/): ElmahR is on [Bitbucket](https://bitbucket.org/wasp/elmahr/overview) and I'm very happy with it, but I want to check if the Github community can bring more help to the project than it happened there
*   It will still be based on [ELMAH](https://code.google.com/p/elmah/) and [SignalR](http://www.asp.net/signalr) (v2), of course, but also on [Owin](http://owin.org/) and [Rx](http://msdn.microsoft.com/en-us/data/gg577609.aspx) on the server, and probably on [RequireJS](http://requirejs.org/) on the client
*   The code base will be simpler, streamlined, with fewer but stronger features: less is more :) Among them we'll have:
    *   application groups
    *   users per application group
    *   better organized server side, with a clean separation between the portion serving dashboard clients and a new "admin" one
    *   more fine grained configuration, with a better way to make it "live" and not just from web.config
    *   independent js client, cross domain
*   The dashboard will be redesigned on top of [Bootstrap 3](http://getbootstrap.com/). I might switch from [Knockout](http://knockoutjs.com/) to [AngularJS](https://angularjs.org/), but I'm not yet sure about that either

Ok, this is the plan, in the next few days I'll do a few early experiments before actually starting pushing stuff in its [new repo](https://github.com/wasphub/RElmah). Stay tuned!
