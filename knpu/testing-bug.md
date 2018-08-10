# Testing Bug

Coming soon...

All right, we just helped review our first poll request and moving forward, let's see what other trouble we can get into. So in addition to reviewing poll requests, one of the most important things is helping to triage bugs. So if you go to issues, 

of course you can always, uh, look at an issue that's a feature of these tend to be more discussions about whether or not that's a good feature, which is always good, but bugs you click the bug label. These are the things that are hard. There's not a lot of bugs in symphony. So if there is a bug, a lot of times they're very, they're pretty complex and the core team needs help with people actually verifying the bug, asking questions, asking them more information from the person that created the issue and ultimately creating a reproducer, which is a very, very small project that easily shows that bug and action. 

So I'm actually gonna show you above the. I found the other day. It's 27, nine, zero one. All right, so let's look at this. So he says that he got an air when trying to serialize a doctrine query builder in debug mode with the web profiler you cannot see realize or unsterilized pdo instances coming from the data collector. So when the, what one the, the web profiler works by collecting a bunch of information about the request and serializing it to a cache file. So it looks like something failed during that process. So you said how to reproduce, make sure you're using the web profiler, create a connection of Ta type doctrine, [inaudible] connection. So he's using the lower level doctrine connection object instead of the entity manager. And then he says, use the function exec update by passing an argument of type query builder 

and even gives them a possible solution down here. All right, so what we can do to help us as you is, even though he gave a pretty good way to reproduce it, if we can create a small symphony project that shows this air and action, it might help and post it to this, it might help somebody else solve the issue or it might even give us the confidence to solve the issue. So let's try it first, notice that this is a issue against these stable version of symphony four point one. So I'm going to create a new symphony project against, but against that four point one version instead of Dev master like before. So I'll hit control c, I'm going to go back to our top level contributing directory and use composer create dash project, symphony slash skeleton with no version constraints. So just uses the latest one. 

I'll 

call this triage issue 27, 27 nine zero one. 

Okay. 

Once that finishes move into that directory and the one thing that we know are going to need for this is a doctrine because he's using doctrine. He says we're all going to need to use the web profiler, but we'll worry about that in a section and a second. So let's go back over here and I'll say a composer require orm that will actually give us the whole doctrine or am not just the deep ball, but that's fine. 

Okay. 

When that finishes, let's go ahead and get some of our codes set up to test. So I'm gonna close my previous directory. Go into this new directory and we're going to go straight into using a controller here, straight into creating a controller. All credit new PHP class this summer called issue 27 nine. Oh, one controller. Give it the name, space and public function test. Alright, so if you look back at the issue, he says that he's using the doctrine debulk nexion object. So I'm going to move back over to my terminal here and run bin Console. Debug. That's a class that I use very often, but if you use diva auto wiring and scroll up. Yup. It looks like we can type it the type hint, this connection class here and get the service that he's talking about. Fall type in connection, 

connection. 

And then the second thing he says is that he's going to call it an exec update function is going to pass it an argument of type of query builder if you're used to, if you normally, if you're used to using the arm, it has a query builder, but if you use the lower doctrine debell it also has its own query builder. It's not important, but these are little details that sometimes you need to learn about when you're triaging an issue so you can create a debox very bothered by saying, connection Arrow, create query builder. I'm not even going to do anything with that query builder yet. Ultimately to test this feature, I will probably need to create an entity, a database connection so that we can actually create the update, but for now let's just start right in the beginning of our code. So then he says that he runs the method x exec update, but actually there is no method called exec update, but he probably means execute, update, and if we weren't sure that would be a question that we could ask him to move this along and we're going to pass that. The qb. 

No. 

Well then this is where things get interesting because I noticed when I was filling in this that it looks like the first argument. This is a string, so I'm gonna hold command and click into this and check this out. The first argument, that query is supposed to be a string, but the user is passing a query builder. In other words, this doesn't look like a bug. To me. This just looks like the user's passing the wrong argument and that the whole reason that it almost works and only blows up in the web profiler is that if you look at the old command again and click into that queer object. If you look at this, it at the object actually does have a two string method, so in other places it is working, but ultimately the profiler, it doesn't work. This is why his possible solution says in inside of a different class to basically take that sql which is actually the query builder and his case and cast it to a string which changes it to a string that is a valid solution, but ultimately this user is. It looks like they're actually using dysfunction and correctly so that we can reply with that information to help things along. 

And even though this looks like it might be user mistake, it's still awesome that they opened the issue because this might be something that we still can fix or maybe make them more obvious error. 

Whoa. 

I want to be very clear about what code I checked out. So I'm actually in a copy of the, uh, 

actually 

my copy, the exact use statement here so I can give you the exact class. Actually expects a string first argument, not a query builder. And then to be extra Grad, I'm actually going to link to that. So this is extra credit, but I can go to the doctor and d ball repository on Github. I'll hit tee to pull up this screen, find that class, search for execute, update, and find the function. There it is. Click this line and you can see it gives me a url to this exact line. Uh, but then another trick is to hit the y key in your keyboard and that will change that master to the actual commit. The nice thing about that is if that there, if there are changes in the future to the master branch by Linkedin and the specific commit, this will line 1068 will always be this exact line. Oh, and actually the better line would be the Ph with documentation. I'll give a little bit more information. Oh, so that is fixed. Is valid, but it's just kind of for an invalid use case. I give a nice entry on there, 

boom. Now I have the power to close this issue. You probably won't have that power and that's fine. You just made this very easy so that when someone comes later that can look at this and close the issue, probably that's what they're going to do. Alright. So that was actually, we just help probably close an issue. Um, what would I really want to show is when you find the legitimate bug and how you can create a reproducer project so that it can make it really easy for somebody else to fix that bug. Let's do that next.