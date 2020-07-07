So the problem I want to talk to you about today is this; Software engineers suck at making diagrams. You all are horrible at making diagrams that don't suck, and the worst part is that you don't even know it.

As a software architect I have to deal with this a lot. Some bright young engineer, two or three years into the job, hungry and brimming with excitement will wander over to my desk, and they'll say "Hey Bob, have you got a second? I've got this idea", and then they'll show me this and then stand there expectantly while I stare into the void. And the longer I look, the more horrifyinbg details I notice. Like what is this green circle, and why is it connected to time? This diagram includes a mixer and some audit purging and over here is a box that you have to turn your head 180 degrees to see and this cuylinder here contains risk, so you'd better not ever open it.

And in case you think I'm just being snarky about your drawing skills, I'm not I swear, my handwriting is worse than yours, I can't reliably draw a rectangle and an arrow on a whiteboard, which is a serious disability for someone in my line of work. In fact, one of the major reasons we hired Ryan was that his whiteboard penmanship is just excellent and I hoped to get him to do all the diagrams for me.

Here's a neater diagram and you might be thinking "wow, that looks pretty nice" and I'd agree, if I were judging it as a piece of art, but as a diagram this, too, sucks enormous hairy balls. I swear, the more you look at it, the more you'll hate it because while it looks logical enough from the outside, you need to ask one question: what does this system _do_?

Well, apparently it calculates counts, where a + b =c with a params toler, which comes from security provided by this faceless gentleman.  Who is he? Why is he putting security in the UI? If the calculation engine has a fail event, it goes into the middle of this empty space while a complete event will go on this line. Other outputs might reject this circle or go in an output store, which seems sensible enough.

The data validation integrity validation rejects tolerance, and the whole thing is in a box marked "physical security".

Now I'm sure that the person who made this artefact understood it, but that's nto how communication works. I have this argument with my autistic son all the time. It doesn't MATTER that you can read your handwriting if your teacher can't. the whole point of writing stuff down is so that another human being can understand what you're trying to say and assess its  merit. If you bring me a diagram like this, that's just a labyrinth of half-formed ideas, I'm not going to pretend any more, I'm too old for that shit, I\'m just going to say "I'm sorry, I haven't a clue what this is supposed to mean."'

Now I suck at making diagrams, too. I suck harder than any of you, because I'm _such_ a klutz with a pen and paper. True story: I wrote a note for that same teenage son, to excuse him from school in the afternoon for a dentist appointment, and the school refused to believe it was an adult's handwriting. I had to call up and have a whole argument with them, because they assumed that he was a particularly inept forger.

That was sort of okay for the first 15 years of my career, and then I ended up as the only architect at MADE.com and people started asking me questions like "Do we have a diagram of the architecture? I want to see how everything fits together."

At this point in MADE's trajectory, we had maybe 10 or so services, each of which might have had an API and a UI and some event consumers and a database or two, and some of them were deployed on EC2 instances, and some were on a Nomad cluster, and they were split across maybe six AWS accounts, and were written in three production languages, and communicated with around 50 domain events.

And so when somebody asked me this question, I never knew how to answer it, because its like sommebody asked you "Hey, do you have a map?" and you're like "of ... what? Like where?" and they're just "no, I mean I just want a map of, yunno, everything, to see how the universe fits together"

It's not a question that can be meaningfully answered, except with blank stares and nervous laughter, but people kept on asking it, and so eventually we set up a working group - the working group on diagrams and visual language - to figure out how to draw diagrams and communicate effectively through pictures.

I'm going to talk a bit about the C4 model in a second, and we're going to make some diagrams together, but first I'm going to show you the tl;dr.

This is a checklist for figuring out if your diagram is trash or not, I\'ll quickly read through it.

And this is a GOOD LOOKING diagram. If I had drawn this freehand on a whiteboard, I'd be super pleased with myself, but let's check it against the checklist.

Does it have a title? Do we understand what type of diagram it is? Does it even have a type? What does type even mean? Do we understand what this "error" element is? What technology is "bus logic"? What's the relationship between transport and bus logic? How does data get between those boxes? What does TDS mean, is RDS a relational database service? What does the scheduler do, and how does it do it? Why does report gen have an arrow pointing at the business user's face?

This is a terrible diagram, because even though I know what the author was trying to depict, I have no idea how this system is supposed to _Work_ and if I didn't know the context in which this was produced, I'd have zero idea what it was even supposed to _do_.

I'd argue that _some_ of these items can be relaxed in context, like maybe if your diagram is embedded in a document titled "Trade Report Generation system architecture" you can skip the diagram title, or maybe _by convention_ you know what type the diagram is because everyone draws diagrams in exactly the same way, but as it stands, this is not a useful artefact.

After some research the working group at MADE settled on using the C4 model for diagrams. This is a set of standards for producing diagrams that were written by a guy called Simon Brown, a software architect with a particular obsession about visual language.

The C4 model has four types of diagram, that are hierarchically organised to show the system at different levels of abstraction.

The first is a context diagram. I talk about these _a lot_ because - it turns out - this is what people mean when they ask me for a diagram of the architecture.

A context diagram shows the technology landscape, and how a system fits within it. This is the diagram that you show the tech leadership team when you have an idea for a new system. It shows the key users, and other systems that your system will interact with. Importantly, it describes those interactions in an easy to understand way. At this level, we don't talk about technology choices, there's no information on the protocols or database choices or programming languages that we're using. These systems are described purely in terms of the business capabilities that they support.

Notice, please, that each element on the diagram has a description that explains what it does. It's trivially easy for me to read this diagram and understand what this system is for, and how it interacts with these other things.

The way the C4 model works is that we can choose one of these boxes and we zoom in to show more detail. The next level down from a context diagram is a _container_ diagram. This shows us the deployable units within a system. Container is a terrible name for these things, and it's even more terrible in this post-docker world, because it's so overloaded. I think of these containers as being individual _processes_ that are separately executing, and are probably separated by some kind of network boundary. 

On this diagram we can see that our customer actually interacts with either a web site or a mobile app, and both of those are backed by an API. At this level we're including technology choices, so we can see that the API is a Java app with Spring MVC. The web site is written in Angular, and makes some kind of JSON HTTP call to the API which, in turn, reads and writes from an Oracle database via JDBC.

This is the diagram that you show to a new engineer on the first day that they join your team, because it shows them all the high-level components that make up your system, and which technologies are present.

80% of the time, a context diagram and container diagram is all you need, but sometimes you need to drop down again to see a component diagram. Here we're zooming into the API container and we can see the components that make it up. A component in the C4 model is a _logical_ chunk of functionality inside a single process. Some of these - for example the Reset Password Controller - are single classes. Others, like the Mainframe Banking System Facade are almost certainly composed of multiple classes, but we can logically chunk that into higher level components.

Lastly, we can, if we really need to, zoom into a single component and look at a code level diagram.

This particular diagram is a UML class diagram of the mainframe facade. I don't have much to say about UML as a capital T thing, except to note that class diagrams suck, and you shouldn't use them. Sequence diagrams are useful here. The point of a code-level diagram is to explain the micro-design of a particular component in nauseating detail. Generally, if you really need a code-level view of a system, it's because you're discussing some particularly fine-grained point. Keeping these up to date is a non-starter unless you're generating them from the code, because they get out of date quickly.

So the C4 model lets us zoom all the way out to see an overview of an entire system, and we can explain its workings to a business stakeholder, and then we can step down to talk about the high-level technology choices that we're making and how we might deploy this thing, and then we can step down again to talk about design patterns that we're using to structure the system, and finally we can get down in the weeds and look at the micro-design of the code.

If you have to make diagrams of things, this is a great model to start with, because it answers so many of our checklist items. Every element and every relationship has a clearly defined type, has a label, explains the technology choices if applicable.

