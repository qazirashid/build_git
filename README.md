# Building of git from scratch
These are my personal notes made in the my quest to understand how git was built from scratch by Linus Torvalds. 

## Motivation
It is important to understand how good software is built. While learning programming languages is necessary, it is not sufficient to build good software. 
When looking at software engineering, there is a lot of noise and plethora of tools that cause a dispersion of thought and lack the timeless qualities of good software. It is difficult to build a focus. 
Git is a great software and it is widely used by the development community. Therefore, if I am able to build up good understanding of how git was built, I can carry those lessons and apply those to building of other software. Why git? because it was built by Linus Torvalds. I could have taken Linux Kernel as the target, but that is too low level and is concerned about hardware abstraction. 
git is a good candidate because it is application level software and hence it might be more useful in learning how to build good application level software. 


## My Setup
I am using Ubuntu 22.04 LTS. This is not a studied choice. I just happended to have a VM running this so I went ahead with this. Hopefully it won't be an issue.

## Methodology
I would like to start with cloning the source code for git from github. Checking out the first commit. Try to understand it. Build it and run it to the extent I can. Once I think I have good understanding of first commit, checkout the next commit and repeat. 




### Clone original git repository
Create a new directory. let's start by cloning the git repository.

    git clone https://github.com/git/git.git ./

Then look at the log in reverse order. 

    git log --reverse
