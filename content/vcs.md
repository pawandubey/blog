title= "A Friendly Introduction to Version Control and Git"
author= "Pawan Dubey"
date= "2014 06 03"
layout= "post"
tags= ["code"]
#####
Once upon a time there lived two programmers who were great friends. They loved the same programming language, wanted to make similar kinds of things and shared similar interests. Their ideas were convergent. But their code was separated by physical distance.##more##

They tried emailing the whole code back and forth to each other. Initially, it seemed to work and they made progress. But the speed of progress was too much and the wheels came right off the emailing system as their code became large. They had to think of some solution. Something that would work with any size of codebase. Something, which didn’t depend on the size of codebase at all!

Well, the above might be a fictional account, but it would have been a reality of every programmer, had there not been Version Control Systems.

Had there not been what?!

Version Control Systems. The very lifeline of collaborative coding. The name might sound confusing, but the concept is incredibly simple. And you can bet that you have done what it does some way or the other in your life before. Disagree? Let us see.

Version Control Systems don’t do anything other than keep track of the each “version” of your code. Here, a version refers to a saved point in the evolution of your code. You are the one who decides at which point in the life of your code to make the saves for newer versions. Thus, your code is saved not as a single, monolithic file, which has only two “time-related” properties: the date it was created and the date it was last modified on. Instead, your code is saved as a stack of overlapping “versions” of your file. With each newer version stacking on the top of the pile, adding the changes made since the last version to the stack of versions, in turn completing your codebase. If you paid attention, you would have noticed that instead of having only two time-related properties, this new system has any many time-related properties as there are versions in the stack.

In fact, a version represents a point of time in the life of the code itself!

So, okay, you get how it works. But you don’t get how it helps our two geographically separated programmers, who were saddened by their inability to collaborate on a project they both wanted to do? Well, we were just coming to that feature of Version Control Systems. Repositories.

A repository is a copy of the entire code – represented in the form of versions – stored on a memory. Hence, it can be your project folder right there in your computer. If you are version controlling it, you might as well start calling it your repository. What a repository does is simply hold all the updated code. But there we are presented with a problem: where does the repository sit?

Should both of our disgruntled programmers have their own separate repositories? But that won’t be much different from coding individually now, would it? So should they host the repository on an online server instead, accessible by both of them? Excellent idea! A server it is, then!

And indeed, a repository is only effective if it is stored remotely, on a server which is accessible through a network (the internet for example) for other collaborators to contribute code to. But then this brings us to another dilemma. Should all the collaborators on the code host their own local repositories (the complete stack of all the versions) on their computers as well? Or should they only keep the latest versions on their computer?

Well, both of these approaches made sense to the some faction of programmers and two different types of Version Control Systems were developed: Distributed and Centralized. A Distributed VCS applies the former of the two possible approaches we discussed. Each contributor maintains a local instance of the complete repository, with all the information about each version of the code, and all the changes brought in by each version.

In a Centralized VCS, the contributors only keep the latest version of the repository on their systems, for development aid. All the preceding version information is missing from their local instances. Instead, all the information is stored on the server, on the central repository. Since there is only one complete repository, any change made by the contributors must be added to this one central repository only. But not all code is good enough. Hence, somebody would need to proofread and validate it constantly. This become too big a hassle once a project gains momentum. Consider an active project like Mozilla Firefox with hundreds of contributors trying to add dozens of small changes every day. It is a proofreader’s nightmare.

Although later developments in the CVCS would easen most of the pain related to adding changes efficaciously, systems like SVN(Subversion, a CVCS) would never gain full momentum because of its shortcomings, especially in dealing with larger projects.

Thankfully, in the early 2000s, a certain Linus Torvalds would get into dispute with the company hosting the repositories of the Linux kernel, and would ruefully decide to make his own VCS. The world would never be the same again.

Git was born, partly from the need to freely and more effectively manage the Linux kernel codebase, and partly from the genius of the badass known to us as Linus Torvalds. And it promptly changed the whole version control atmosphere. Version Control was suddenly easy. And more importantly, it was not a headache with even the largest of codebases. Just to top it off, Git, the promising newcomer was free and open source. It was the best thing invented since sliced bread. It took the developer world by storm.

Git was a Distributed Version Control System (DVCS). Inferring from our earlier discussion, that means that projects using Git would need their contributors to have their own local versions of the repository stored on the server. Thus, each developer was free to test the changes he made and its effects on the code base (something called Integration Testing and Acceptance Testing) on his own local instance of the repository before trying to add it to the main remote repository. This would massively increase the ease of project management, pulling the developers to adopt Git like a magnet.

Git had other additional features as well, which makes it not only more lightweight, but also a lot easier to deal with. One of those is called branching. Branching is an integral concept of Git, and is another of those features which helped make the choice of developers.

Branching is simple. Consider the main repository to be the trunk of the tree. The base of the trunk, adjacent to the ground represents the initial state of the code. As more and more versions are generated and added to the code base, it slowly evolves, hence the trunk of the tree grows in height. Now at some point in the development of the code, a new feature needed to be added. But it was not advisable to stop the development on the main code just for the new “extra” feature. So the feature was handed over to some of the developers to work on, while the others kept working on the main code base.

These new feature developers created a new “branch” off the main repository for the feature development. See this point as the point on the tree trunk where a new branch sprouts out and away from the main trunk. The branch shares the same wood before its origin from the trunk, but then develops into a separate part of the tree.

Similarly, a branch in a repository is just a fork in the development path of the main code base. The forked branch has the same properties as the main code base had at the point of origin of the branch. But after that point, the main code base and the feature branch can proceed independently of each other, hence the feature development can take place without stopping the main development. Moreover, as the branch is a full repository in itself (you can see it as a virtual repository based on the repository it was branched off), hence all the testing can be done on the branch itself.

But Git does not completely separates the branch from its origin. The branch and its originating code base are always linked passively. One can easily incorporate changes made in the original repository (or any other branch for that matter) into the feature branch after the creation of the branch. Or if one wants to, they can merge back the created branch into its origin (or to any other branch of choice). New branches can be created off branches. The possibilities are endless.

Branches store the changes made to the repository in a very high level of abstraction. At the granular level, Git stores each modification made to the code in the form of “commits”. Each commit is a snapshot of the complete code base at the time of the commit (inclusive of the modifications being committed).

This is again a big difference between DVCS and CVCS. A CVCS stores modifications in the form of modifications only. Hence each version of the repository on a CVCS is nothing but an account of what changed from the last version. So, mathematically speaking, if the original repository is represented as R and the modifications applied to it is represented as ∆R, then the repository versions would be something like this in a CVCS:

R –> ∆R1 –> ∆R2 –> …………………………..–> ∆Rn

Whereas, in a DVCS, like Git, each change, or commit takes a new snapshot of the whole repository at that point, so the representation would be something like:

R –> R+∆R1 –> R+∆R1+∆R2 –> ………………………..–> R+(Σ∆Rn)

Hence, even if the original version (or any of the versions for that matter) is destroyed by some reason, it will still result in no loss of data, whereas everything will go berserk in a CVCS. Git is made for data-security. You never subtract data from a Git repository. You only add data. Even the removal of some code will add a commit documenting the removal. Data is never removed.

Thus, our two programmers finally found a quantum of solace. They could use Git to easily collaborate over the internet and evolve their ideas into something revolutionary. Will they go on to become millionaires in the future? We don’t know. But we do hope they come back to thank Git if they do. That is the least they could commit to.

