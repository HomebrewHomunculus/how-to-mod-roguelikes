# How to Mod Roguelikes

A walkthrough on creating your own modifications and variants of free and open-source roguelike games.


## Learn by doing: Creating your own mod of _Sil-Q_

Prerequisite knowledge: basic knowledge of git and Linux.

Knowledge of the C programming language is not necessary to make changes to
a game, but it will help you later if/when you want to make more
complex changes to the game logic.


### 1. Set up your operating system

As a general guideline, for any open-source C projects, roguelike or
otherwise, I recommend you get a **Linux** environment.

If you don't have a Linux installation ready, you can quite easily boot it off a USB stick.
Alternatively, you can run a Linux virtual machine using a tool such as **VirtualBox VM**.
It doesn't really matter which way you run it, as long as you are able to run GCC to compile C projects.

For small bouts of C development, I use VirtualBox on Windows and a Ubuntu 20.04 image, since that was the first OS image I found.
Other Linux distributions are available.
[VirtualBox](https://www.virtualbox.org/) is free and open-source (FOSS), available for all platforms, and costs nothing.
Setup instructions for Windows can be found on the web. If you've never used a Linux before, then the Ubuntu variant is an easy introduction, and there are plenty of guides for installing it.

If your computer runs on **MacOS**/OS X, that will most likely work as well, since it's a Unix system. I haven't tried setting it up for C development myself, so I can't comment either way.

If you want to do this on **Windows**, then _technically_, you _could_ mess
around with **Cygwin** to get C to compile. I don't recommend trying that option,
though. It's simply not worth the pain, in my experience. However, the
[Sil-Q readme](https://github.com/sil-quirk/sil-q#windows-with-cygwin---tested-with-sil-q)
does say that it has been tested and confirmed to work.


### 2. Set up your tools

If you installed Linux, then you will most likely already have `git`.
If not, install it now.

Later, you will likely need some build tools (e.g. `gcc`, `make`), but these
too may have been packaged with your OS installation. If not, they can be
downloaded later when the need arises. Dependencies can typically be installed
with a package manager such as `apt-get` (Ubuntu, etc.) or `brew` (MacOS).
If you need to install dependencies using Cygwin, well, you're on your own.

You will also need a text editor or IDE for editing files. Any simple text editor will
do for the basic changes we will make in this tutorial, though they may be
less convenient later for bigger changes to code. Use whatever you prefer.

### 3. Clone or fork the repository

Now we need to acquire the source code.
I won't explain the principles of `git` in here, merely list your
options.

The source code for Sil-Q (and most open-source roguelikes) is hosted
on GitHub, so our main options are to either clone or fork the project.

https://github.com/sil-quirk/sil-q

Cloning works without a GitHub account, and is sufficient for looking at the code
and playing around with it for your own entertainment.
All you need is the git command line tool.
To clone the Sil-Q source code, execute this command:

```sh
git clone https://github.com/sil-quirk/sil-q.git
```

However, if you want to release a modified game to anyone else, you have to always release the source code (I am not a lawyer and this is not legal advice - always consult the specific license of the software you're modifying. Sil-Q's license can be read in [LICENSE.md](https://github.com/sil-quirk/sil-q/blob/master/LICENSE.md)).

For purposes of distributing your code, forking the project is a better
option than cloning it, but this will require you to have your own GitHub account.

Navigate to https://github.com/sil-quirk/sil-q , then click ”Fork” on the top right. After a few seconds, you should have your personal repository, with a
name something like _Your_Profile/sil-q_.

Now, clone the forked repository to your machine.

```sh
git clone https://github.com/Your_Profile/sil-q.git
```

### 4. Compile the unmodified game


Now that you have a local copy of the source code, follow the
instructions provided in the repository to compile it and test that the game runs:

1. The _makefile_, which defines the compilation of the executables,
is at `src/Makefile.std.` Open this file in a text editor and
read the instructions within. The `README.md` suggests to uncomment one of the variations, but it looks like the file also contains a standard build option,
so we'll try that first. Navigate to the src folder, then run the make command as advised in `README.md`:

  ```sh
  cd src
  make -f Makefile.std install
  ```

2. The `make` program should now run and begin to build the project. However, it runs into an error:

  ```output
  gcc -Wall -O1 -pipe -g -D"USE_X11" -D"USE_GCU"   -c -o main-gcu.o main-gcu.c
  main-gcu.c:64:10: fatal error: curses.h: No such file or directory
     64 | #include <curses.h>
        |          ^~~~~~~~~~
  compilation terminated.
  make: *** [<builtin>: main-gcu.o] Error 1
  ```

  (If you see other errors about missing tools, such as `gcc`
  or `make`, you may need to install them now, before proceeding to the next step.)

3. The error seems to suggest that we are missing a library called `curses`.
At this point, we can either attempt to acquire the missing library, or we can attempt another build option.

  I happen to know that `curses` is a terminal library.
 Since X11 is a GUI window system, perhaps it doesn't depend on terminal
 libraries. So, let's attempt the X11 build option.

4. As advised in the `README.md` and the makefile:

  ```
  Some "examples" are given below, they can be used by simply
  removing the FIRST column of "#" signs from the "block" of lines
  you wish to use, and commenting out "standard" block below.
  ```

  Thus, in `src/Makefile.std`, we comment out lines 115-116 (with the `#` symbol),
  like so:

  ```makefile
  ##
  ## Standard -- "main-x11.c" & "main-gcu.c"
  ##
  # CFLAGS = -Wall -O1 -pipe -g -D"USE_X11" -D"USE_GCU"
  # LIBS = -lX11 -lcurses

  ```

  And uncomment lines 129-130:

  ```makefile
  ##
  ## Variation -- "main-x11.c"
  ##
  CFLAGS = -Wall -O1 -pipe -g -D"USE_X11"
  LIBS = -lX11
  ```

  You'll note that this X11 option doesn't mention `curses`, which is promising.

5. We save the file, and then run the `make` command again:

  ```sh
  make -f Makefile.std install
  ```

  The build now seems to complete without errors - at least on my system.
  However, I may have had some C build tools already installed from before.
  If you're on a fresh OS install and run into more errors about missing
  dependencies, then you'll need to figure out how to acquire them using your own initiative.

6. A new executable file has now appeared in the repository root: `sil`. (We can tell by its timestamp that it's a newly modified file. Also, the last line printed during the build was `cp sil ..`, which is the command to "copy the file named sil into the parent directory". Since the build was run from the `src` directory, its parent is the root directory of the project.)

  We can now run the game:

  ```sh
  cd ..
  ./sil
  ```

  It runs! Woohoo!

  [image1]

  However, this is not the X11 window version we wanted.
  (How can we tell? There is only one Window, and the GUI for Angband-based
    games is notable for running in multiple windows.)

  The other two executables in this directory, `silg` and `silx`, are in fact
  nothing but scripts that launch the main Sil executable, with some parameters.
   `silg` is for the graphical tiles mode, and `silx` for the X11 mode.
   So, we start the windowed mode:

  ```sh
  ./silx
  ```

  [image2]

  That's more like it!


### 5. Take a well-deserved break

If you got this far, **congratulations**! Setting up a development environment and troubleshooting build tools is some of the most tedious work in software development.

You may be feeling a little drained after doing all that (depending on how many
errors you ran into).
But here are the good news: with the boring part out of the way, **the fun part can now begin!**
Now that we've confirmed that the game compiles and runs correctly _without modifications_, we can begin modifying it.

This is the order we do things in: we ALWAYS compile the unmodified repository
before making any changes to it. This is also true when setting up your own
repository on a new system. Why? So that we know whether any problems we
may run into are due to our changes, or due to something else.

From now on, after each change we make to the source files, we build the game again, and try to launch it. If the game fails to either build or launch,
then we'll know that the error was introduced by our latest
modifications, and is not a problem with the original source or our tools.


### 6. Modify the game's source

Great, now let's jump into the actual source files!

For our very first change, we want to start small. Think of a change that's
very tiny, yet immediately visible within the game. This is our "Hello World"
modding moment.
We don't want to make changes to, say, the final boss, and then have to play
for hours just to see if anything changed.

Perhaps we'll see if we can edit the starting items for the player character.
Since we don't know anything about how the code is structured, we begin
either by browsing the source files, or with a text search of the project.
For the keywords of the serach, we use some educated guesses such as
"starting_items".

After a short bit of digging, we find functions such as `static void player_outfit(void)`, and flags such as `RHF_BOW_PROFICIENCY`.

From there, following the trail of clues leads us to the files
`edit/race.txt` and `edit/house.txt`.
These are not code files as such,
but data files used to generate the starting options in the game.
This makes them extremely easy to edit - we don't even need to know anything about the C programming language.
The top of the file has a nice explanation of how its content is structured,
which is all we need to know.

Let's say that we want to give the Edain - the humans - a bit of a boost.
If your inner Tolkien scholar is shuddering at the thought, just keep in
mind that we are not concerned with balance or theme AT ALL right now:
we just want to do something stupid and see it take effect.
(Believe me, if you knew what Doom and Wolf3D modders in the 90s were
  replacing the knife sprites with, then you'd know that nothing you
  can come up with will be stupider.)

This is the stat block for Edain in `edit/race.txt`:

  ```
  N:3:Edain
  S:0:0:0:0
  I:21:15:45
  H:70:4
  W:150:12
  C:7|8|9
  E:39:0:3:3     # ~ Wooden Torches (3)
  E:80:35:5:5    # , Pieces of Dark Bread (5)
  E:23:7:1:1     # | Curved sword (1)
  ```

  (Refer back to the top of the file to understand what the different numbers are for.)
  Just for fun, let's change the starting equipment to represent a somewhat better prepared Edain:

  ```
  E:39:1:1:1     # ~ Brass Lantern (1)
  E:80:36:5:5    # , Strips of Dried Meat (5)
  E:23:17:1:1    # | Longsword (1)
  ```

  Note that the text after the # on each line does not actually matter.
  It's only a comment for human readers, and the program doesn't
  actually care about them.
  I've updated the text to reflect the changes I've made for your benefit.
  The only important part are the item codes.
  The new codes here were copied from `edit/object.txt`.

  From looking around in the other files, I noticed that the constant `MAX_START_ITEMS` is as 4.
  We only have three items so far.
  So let's add one more item to our Edain's inventory - why not!

  ```
  E:37:4:1:1     # [ Mail Corslet (1)
  ```

  Afterwards, remember to save the file.

  Next, let's take another look at the actual code. In `src/birth.c`, we can
  see a function called `static int get_start_xp(void)`.
  This determines the experience points for starting characters,
  using the constant `PY_START_EXP`.
  Searching for that constant's name brings us to `src/defines.h`, line 376:

  ```
  #define PY_START_EXP 5000 /* Starting exp */
  ```

  Just for fun, let's bump that up a little:

  ```
  #define PY_START_EXP 25000 /* Starting exp */
  ```

  After saving the file, we're finished making changes for now.


## 7. Run your modified game

Having made our modifications, we build the executable again
(making sure that the game isn't still running from last time), and launch it again:

```sh
cd src
make -f Makefile.std install
cd ..
./silx
```

And...

[image 3]

Hey presto! Our Edain character now has more appropriate provisions
for an expedition into Angband, including a longsword and some armour!
And has five times the usual amount of XP to spend.

Savour this feeling: by changing some text, you bent the machine's workings to your will,
and now see your work manifested on the screen!
Contemplate the ramifications: if you can do this, then there's nothing
within your imagination that you cannot also do.


### 8. You are now a modder!

That's it. **You've modified a game!**

All that remains is to commit the changes and push them to your git repository.
You can also try out the other compilation options, build the game for Windows,
and share packaged ready-to-play binaries with other people, but none of that
is strictly necessary.

We didn't make any complex changes to the game logic yet, as that's beyond
a single tutorial. I've helped you make a start (hopefully), but the rest
of the path you'll have to forge on your own.
Perhaps you'll build up to large
changes to the game's systems - which will require a lot of custom code.
Or maybe these edits are as complex as it gets for you, and you have
a vision for the game that
can be achieved through just with changes to data and minor code removals.

If you followed along with the changes made in this tutorial,
**it's now up to you to come up with the changes YOU want to make**.

Neither I nor anyone else can tell you what you can or cannot change about this game,
as long as you don't breach the terms of the software license.
If you want to play an Ent with flamethrower arms - well, you can make that happen,
regardless of anyone else's opinion about the idea's balance, thematic appropriateness,
or even common sense. Of course, they're not obliged to play your version of the game,
either. But if you're making something that you'll enjoy yourself, then
building an audience does not need to factor into it.

That's the fantastic thing about open software: you can shape it the way _you_ want.
You can go
as crazy as you like without worrying about stepping on anyone's toes, or
spoiling someone else's fun. (The exception being, of course, if you have
  any aspirations about merging your changes into the mainline game.)

Even if you make a game just for yourself, it would be nice if
you'll post about it to let the
community check it out!
Even if it's not something I invest time into playing,
it's always fun to read about other peoples' creative ideas
and look at the projects that made those ideas a reality.
By sharing, you're contributing to the liveliness of the larger
roguelike community, and the FOSS game modding/developing community in general.

Now, let your creativity run wild. It's time to experiment and have fun. Happy hacking!
















# List of other games to mod

The following games have open source code easily available on GitHub.

- Brogue CE (AGPL-3.0, C) - https://github.com/tmewett/BrogueCE/
- Sil-Q (Angband License, C) - https://github.com/sil-quirk/sil-q
- Angband (GPL-2.0/Angband License, C) - https://github.com/angband/angband
- Dungeon Crawl: Stone Soup (GPL-2.0, C++) - https://github.com/crawl/crawl
- Nethack (Nethack General Public License, C) - https://github.com/NetHack/NetHack

In fact, all of these games are themselves variants ('forks', 'mods') of other games. For example, _Dungeon Crawl: Stone Soup_ was forked from _Linley's Dungeon Crawl_. _Sil-Q_ was forked off of _Sil_, which itself was a heavily modified variant of _NPPAngband_, which was a variant of _Angband_. Brogue CE (Community Edition) is a fork of the original Brogue. My own variant [Brogue Lite][url-brogue-lite] is a lightly modified version of Brogue CE.

Forks range from minor modifications, to sweeping balance overhauls, to variant UI frontends, to games whose content and systems are so far removed from the original that they can be called "total conversions" or entire games of their own. For example, while _Sil_ is based on _Angband_, it changes the attributes and skill system, has a completely original combat algorithm, replaces the majority of creatures and items, and has a different game structure and goal.

The terms aren't strictly defined, but it generally seems that "mod" is used for minor changes, while "fork" is used for more games that have a more sustained period of development parallel to the original game. "Variant" is used similarly. (See also the Terminology section at the end of this document.)



# TODO - Terminology

- mod
- variant
- fork
- to fork
- to clone
- mainline
- FOSS - free and open-source software.
- free software - free as in libre, not as in gratis (free of charge).
- open software - often used interchangeably with 'open-source'. In practice, all free software is open-source, but not all open-source software is free: if source code is provided, but you are not free to make a modified version and distribute it, then the software is not free. (The games covered here tend to be free and open-source. They are typically also copyleft: the modified versions that you create must also carry the same license. I am not a lawyer; always consult the license of the particular software you are modifying.)
- license - a software license, the contract that defines your rights to use, modify, and distribute the software. There are many standard types of licenses which grant different sets of rights. It is customary to place this in a LICENSE.md or LICENSE.txt file in the git repository. GitHub offers a nice utility for viewing standard licenses: click "View license" on the right sidebar of the project page.
-


<!-- URL definitions -->

[url-brogue-lite]: https://github.com/HomebrewHomunculus/BrogueLite "Brogue Lite"
