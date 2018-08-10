# Bug Reproducer

Coming soon...

Usually if you want to help out with symphony, you're going to find an existing bug issue like this and try to create a reproducer, but occasionally you might be the person that thinks that you found a bug in. You need create the issue and to create the best possible issue, you should create a reproducer, a very, very small project that shows that bug being reproduced. I actually just found a bug the other day that seems to affect symphonies and master branch only. I don't have it in front of me, but basically as soon as I tried to use the form system to create a form, I got a really strange bug coming from symphonies container. So let's see if we can start a really small project to reproduce that so that we can create an issue. So since I said that, I think the feature affects the master branch. I'm going to use composer create desk, project symphony slash skeleton with Dev Dash Master, and we'll put this into a new container bug reproducer 

directory. 

Once that's done, move into that new directory and the one component I know I'm going to need is the form component saw composer require form. Well, I'm waiting for this. Let's actually go back close a couple of files. Go into that new directory and let's see if we can reproduce it. Now, what I was doing was very simple. I created just a new form class, so right in the source directory. For simplicity, I'll create a new php class, all some form type. I'll make an extended normal abstract type and normally I would have a built form Methadone here, but I'm not even sure if I need to do anything with this forum other than just create it, so we'll keep things as simple as possible. And a controller directory our credit, another new PHP class called container test controller, the APP slash controller namespace. Now make public function 

test. 

Now I want to create a new form in here, so I'm going to need to extend abstract controller one, her framework bundle so that I can say form equals this Arrow create form and pass that our forum class, which is going to be some form type calling on class. 

That's it. 

If I'm right, then the air is actually going to happen right here and I'm not doing anything special at all. So to test this out and we need to do is go and create a route to this. So I'll go with uncomment this out. Again, here we go, set up the url and then back on our on our terminal and install the form. Okay, so we'll run our PHP, dash, dash local Otas, colonate thousand dash t public again, then move over, fine our tablet locals 8,000. Refresh and say hello to my super weird error. Compile error, failed opening required file in some Bar Cash Dev directory. This is related to the container and this is very low level code and you should not be able to trigger this. Now, just to make sure I'm going to go back to my terminal, I'm just going to rm Dash, rf my bar cache directory entirely to make sure that something totally weird didn't happen. I'll rerun the web server and when I refresh. Yep, I get the same exact thing. Okay. Awesome. So I'm gonna take a screenshot of this so that we have it for the issue later 

and then go back to and then go back to synchrony repository. Click on symphony, really go to the issues part, click new issue and this is a bug report so we'll get started and it's really nice because it gives you a really nice outline of all the information that you want to do. So symphony affected versions. We're going to say master branch. If we wanted to, we could also check to see if this affects the currently stable branch. That would be a pretty good idea. We he create another reproducer with that and see if it works. My guess is that this is not going to be on the stable branch because this looks like a pretty critical error 

description 

when trying to use. Trying to create any form type a strange container, require failure, occurs and here or put in our screenshot and then here's the next part, how to reproduce so I can put the steps and how to reproduce. That's very simple, but instead let's actually push a reproducer Oh and don't forget a title. 

Um, 

actually use the air here. It's a failed opening required acquired container cache file when it using form system. So to get the reproducer up to get hub control c, let's start a new get repositor right here. We'll add everything. 

Um, 

if I thought I had what would have been even better for me to make an initial commit when I started the project and then make one clean commit for each of the things I did, like install a form of component and then actually make my test code. Now I'm gonna. Go back to get hub credit, new repository, and we'll call it symphony 

container. 

How about bog 

tanner reproducer? 

You don't need to put anything special in. It will create the repository. I'll copy these two lines down here for an existing repository, moved back over, push those up, refresh and cool. Here is our reproducer library, so let's copy that. Go over here, give details about how the trigger that air inside of that thing, but just to make sure make things a little simple. I also want to describe what I did inside of that installed. I created a new project from CFA scout and I installed symphony formed headmaster. We decide composer require form, but that project always gets the latest things. Then I'll link to the exact spot where the error happens. I don't have any possible solutions. I don't have any additional context so I don't. 

Right? 

No. The bottom, I'll put a nice message and even for me as a core contributor when I create a poll request or an issue like I'm not sure if this is the real issue. It could still be something that I'm doing wrong, but that's fine. This air is clearly caused me problems and it's confused me and I've done and if it's confused me, it's pro. Even if it is my mistake, somehow it's probably confusing. Other people, there may be some improvement that we need to make. My guess is that this is a bog, but honestly I really don't know for sure. So anyways, let's submit that new issue and there it is out there. It's well documented. It has a real producer. Let's see what happens.