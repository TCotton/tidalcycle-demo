This video is a quick introduction to TidalCycles, which is an open source software music project for live coding algorithmic patterns. TidalCycles is written in the functional programming language Haskell.

Tidal itself is a domain-specific language inside Haskell called Uzulang [show site] https://uzu.lurk.org/. Other than Haskell, other host languages have different Uzulands, for instance Sturdel for JavaScript, and Vortex for Python.

Tidal doesn’t produce sound on its own. It sends musical and control data  - like patterns, rhythms and effects - to sound engines to create the audio. SuperCollider and SuperDirt software packages are used for this purpose.

TidalCycles main capabilities are patterns of time with transformation and composition patterns. It can be used for studio work, but is best suited for live performances.

It’s not a new project. It was started by Alex McLean in the first decade of this century, but it is actively maintained and the last five years has seen the addition of more features and better documentation.

This video is broken down into two parts. In the first part I will talk about how I found installing TidalCycles on my Mac with some tips to help you get Tidal up and running smoothly on your local environment. The second part I will demonstrate the use of TidalCycles to create a techno version of a well-known pop song. At no point will I announce what that song is because this particular group is notoriously reluctant at allowing privileges to other artists to use their work.

Firstly, installing TidalCycles. I use a Mac, so instructions for either Windows or Linux may be somewhat different.

In particular I am using a MacAir M1 laptop, which I am in the process of replacing with a newer MacAir laptop. As a software engineer I do my programming on a Mac Studio, but I've found using a laptop for live music coding a better experience than a desktop.

The TidalCycles crew have helpfully provided a single script that downloads and then installs all the main components. This can be found [see page] in their documentation: https://tidalcycles.org/docs/getting-started/macos_install/

The script is on this page [see page]: https://raw.githubusercontent.com/tidalcycles/tidal-bootstrap/master/tidal-bootstrap.command

The changelog dated December 2022 states:
“tidalcycles installer script for OSX
#      tested on: Intel Big Sur 11.7.1
#       not tested on Silicon’

I successfully used this script on the latest macOs software version on a MacAir with the Silicon chip.

The script checks for Git and Haskell, installing the latter if not present. If this is the first time you have installed Haskell, then be prepared to wait for sometime as installing this language and it's package manager Cabel is always a lengthy procedure.

It will also install the Pulsar IDE with the required Haskell plugins. Pulsar was previously called Atom. You do not have to use the Pulsar IDE - Visual Studio Code also has TidalCycles plugins. I've used Vim for my work and there is a Vim TidalCycles plugin [see page]: https://github.com/tidalcycles/vim-tidal

The script will also install Supercollider [see page] https://supercollider.github.io/ and [see page: https://github.com/musikinformatik/SuperDirt ] Superdirt.

SuperDirt is the TIdal audio engine for playing samples and synths

Nevertheless, I still required further audio assistance for this project. I found that the audio distorts unless it is restrained by setting gains on every channel and by using audio software such as Reaper [see page: https://www.reaper.fm/]. In order to use Reaper with Supercollider and Superdirt you will need to install software called Blackhole 16 channel on the Mac. This acts as a way of routing from Superdirt to Reaper.

Reaper worked fine for me but I have already purchased a copy of Final Cut Pro, which is a digital audio workstation and MIDI sequencer for the MacOS. I also DJ vinyl and I use Final Cut Pro for post-production editing.

Whether you use Reaper or Logic Pro, you will need to set up an aggregate device in the Audio MIDI setup. This is used as an input in the global settings and in the audio software.

In the codebase you will see the use of the orbit function. Each orbit is used for a  separate audio channel that you send your patterns through.

Let’s take a look at the Tidal file I’m going to use.

At the top of the file is ‘cps (120/60/4)’. This is a global setting, the first part is 120 cycles per minute. This is the equivalent of 120 beats per minute. The second value is the number of seconds in a minute and the last value, four, states that for every cycle there are four beats.

Firstly, let’s jump down to d2. First to note, 2 used here is the number of the pattern, you’ll see shortly how this value can be used in other patterns for transitions. D2 is the equivalent to ‘p 2'. The integer can also be a string, like d “bassSound”. Like the integer, the string can also be used as part of transitions.

On the note line here are the chords for the intro to the famous pop song that shall not be named. The second line is the sound used for these notes: “<bass3:0>”. What this notation states is use the first file, 0, from the bass3 samples folder.

I’m not going to explain every single Tidal function, but let’s look at what live coding means.

Running d2, change room to 0.1 and gain to 0.8. It’’s a different sound, but not the heavy darker sound traditionally associated with techno that I wanted for this track.

Patterns are pure functions within Haskell, which means that they are essentially immutable once they are evaluated. However, there are ways of reusing code. In this page there are two examples.

In a do block it is possible to add let variables. This is block scope and lexical so therefore these variables are not available outside of the do block

The advantage of this is that it creates one pattern, in this case d1, to which can be added global changes to all of the different patterns listed in the stack.

Another variation is (go to line 76), uses let but not in a do block. Here the “in” keyword directly ties the previous let variables to the pattern in the stack.

There is also a way of creating reusable patterns in Haskell and importing them into the Tidal boot file. But that is out of scope for this short introduction

An alternative to declaring patterns are to use the transition functions. [Go toline 53] Here I used the xfadeIn. In this example the first integer represents pattern number 1 that is gradually faded in over 4 cycles. There is an entire page on transitions in the TidalCycle documentation: https://tidalcycles.org/docs/reference/transitions/

Go to line #77. This uses a piano sample from the pop song. Notice the use of the chop function. The chop divides the sample into 32 tiny pieces, which are then played back in order. Chop is one of a number of functions that can be used as part of the Tidal sampling package [see page] https://tidalcycles.org/docs/reference/sampling/
7
Is it essential to be able to program in Haskell in order to use TidalCycles? Some knowledge would be useful, for example it is possible to view the function signatures.

‘:t palindrome’, returns ‘palindrome :: Pattern a -> Pattern a’ which tells us that it takes a pattern as input, and gives back a pattern as output. But gives only a partial explanation

Another recommended way of understanding the functions is to examine the source code. The source code has through code comments. Here is the palindrome function in the source code: https://codeberg.org/uzu/tidal/src/commit/660ac746ec5c89da7ead100c0c46bf67e4037f41/tidal-core/src/Sound/Tidal/UI.hs#L572 The code comments give a more thorough explanation: “-- | @palindrome p@ applies @rev@ to @p@ every other cycle, so that the pattern
-- alternates between forwards and backwards. For example, these are equivalent:”

This means it alternates equally between forward and reverse.

SuperCollider requires the addition of a boot file. There is, helpfully, a boot script in the documentation that you can use but I also needed to provide further customisation. [Give examples]

To find help when installing and configuring TidalCycle I would go to the following sources.

Firstly, the official documentation page [show page]: https://tidalcycles.org/docs which is reasonably thorough

The second place is the discussion forum, called Tidal Club [show page]: https://club.tidalcycles.org/ You may find answers to issues you are stuck on.

There is also a Discord channel. I’ve found the users on the TidalCycle Discord channel to be very helpful and promptly answered my queries.

The third place are YouTube tutorials. There are at least two different collections I'm aware of, but the most thorough and up-to-date are by Alex McLean: https://www.youtube.com/playlist?list=PLfNfl4zYWvO7XaVtHUmHW4RQBuB04FV93

Finally, I’ve found ChatGPT surprisingly good at diagnosing issues. I’ve imputed a few different SuperCollider error stacks and it accurately diagnosed the root cause of the retrospective problems.

So that is a very brief introduction to installing TIdalCycles and associated software on a Mac. Let me give you an example of TidalCycles for live music creation
