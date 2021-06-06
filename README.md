# How to Mod Roguelikes

A walkthrough on creating your own modifications and variants of free and open-source roguelike games.


# List of games

The following games have open source code easily available on GitHub.

- Brogue CE (AGPL-3.0, C) - https://github.com/tmewett/BrogueCE/
- Sil-Q (Angband License, C) - https://github.com/sil-quirk/sil-q
- Angband (GPL-2.0/Angband License, C) - https://github.com/angband/angband
- Dungeon Crawl: Stone Soup (GPL-2.0, C++) - https://github.com/crawl/crawl
- Nethack (Nethack General Public License, C) - https://github.com/NetHack/NetHack

In fact, all of these games are themselves variants ('forks', 'mods') of other games. For example, _Dungeon Crawl: Stone Soup_ was forked from _Linley's Dungeon Crawl_. _Sil-Q_ was forked off of _Sil_, which itself was a heavily modified variant of _NPPAngband_, which was a variant of _Angband_. Brogue CE (Community Edition) is a fork of the original Brogue. My own variant [Brogue Lite][url-brogue-lite] is a lightly modified version of Brogue CE.

Forks range from minor modifications, to sweeping balance overhauls, to variant UI frontends, to games whose content and systems are so far removed from the original that they can be called "total conversions" or entire games of their own. For example, while _Sil_ is based on _Angband_, it changes the attributes and skill system, has a completely original combat algorithm, replaces the majority of creatures and items, and has a different game structure and goal.

The terms aren't strictly defined, but it generally seems that "mod" is used for minor changes, while "fork" is used for more games that have a more sustained period of development parallel to the original game. "Variant" is used similarly. (See also the Terminology section at the end of this document.)


# TODO - Example: Creating your own mod of _Sil-Q_

Prerequisite knowledge:

1. a
2. b
3. c
4. d
  1. (you may need to install tools such as `gcc` and `make` if they were not included with your system)

  2. Make begins to build the project, but it runs into an error:

    ```output
    gcc -Wall -O1 -pipe -g -D"USE_X11" -D"USE_GCU"   -c -o main-gcu.o main-gcu.c
    main-gcu.c:64:10: fatal error: curses.h: No such file or directory
       64 | #include <curses.h>
          |          ^~~~~~~~~~
    compilation terminated.
    make: *** [<builtin>: main-gcu.o] Error 1
    ```

  3. This suggests that we are missing a library called `curses`. `curses` is a terminal library.At this point, we can either attempt to acquire the missing library, or we can attempt another build option. Since X11 is a GUI window system, perhaps it doesn't depend on terminal libraries, so let's attempt to use that option.

  4. As advised in the `README.md` and the makefile:

    > Some "examples" are given below, they can be used by simply
    > removing the FIRST column of "#" signs from the "block" of lines
    > you wish to use, and commenting out "standard" block below.

    Thus, in `src/Makefile.std`, we comment out lines 115-116 (with the `#` symbol):

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

    You'll note that these options don't mention `curses`, which is promising.

  5. We save the file, and then run the `make` command again:

    ```sh
    make -f Makefile.std install
    ```

    The command now seems to complete without errors, at least on my system, which has some C build tools already installed. If it does not on your system, and you get more errors about missing dependencies, then you'll have to figure out how to install them. You're going to have to use your own initiative and web searching prowess for this stage.

  6. A new executable file has now appeared in the repository root: `sil`. (We can see from its timestamp that it is a newly modified file; also, the last line printed during the build was `cp sil ..`, which is the command to "copy the file named sil into the parent directory". Since the build was run from the `src` directory, its parent is the root directory of the project.)

    We can now run the game:

    ```sh
    cd ..
    ./sil
    ```

    It runs! Woohoo!

    [image1]

    However, this is not the X11 window version we wanted. In fact, the other two executables in this directory, `silg` and `silx`, are simply scripts that launch the main sil executable with different options. They are for graphical tiles mode, and X11 mode, respectively. So we start the windowed mode:

    ```sh
    ./silx
    ```

    [image2]

    Now that's more like it!

  7. If you got this far, congratulations! Setting up a development environment and troubleshooting build tools is some of the most tedious work in software development.

    - You may be feeling drained after doing all that, depending on how many errors you ran into. But here's the good news: the fun part can now begin! Now that we've confirmed that the game compiles and runs correctly without modifications, we can begin modifying it.

    - After each change we make to the source files, we build the game again, and launch it. If either of those steps fails, then we'll know that the error was introduced by our modifications, not something wrong with the original source or our tools.

  8. Great, now let's jump into the actual source files.

      - For our very first change, we want to start small. Think of a change that's very tiny, yet immediately visible within the game. We don't want to make changes to the final boss and have to play for hours to see if anything changed.

      - Perhaps we'll see if we can edit the starting items for the player character. After doing a bit browsing the source files and keyword searches, we find functions such as `static void player_outfit(void)`{:.c}, and flags such as `RHF_BOW_PROFICIENCY`.

      - A further search leads us to the files `edit/race.txt` and `edit/house.txt`. These are not code files, as such, but data files used to generate the starting options in the game. This makes them extremely easy to edit - we don't need to know anything about C. The top of the file has a nice explanation of how its content is structured.

      - Let's say we want to give the Edain, the humans, a bit of a boost. (Remember, we are not concerned with balance or theme AT ALL right now, we just want to do something stupid and see it take effect.) This is the stat block for Edain in `edit/race.txt`:

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

        Refer back to the top of the file to understand the different numbers. Now, just for fun, let's change the starting equipment to represent a somewhat better prepared Edain:

        ```
        E:39:1:1:1     # ~ Brass Lantern (1)
        E:80:36:5:5    # , Strips of Dried Meat (5)
        E:23:17:1:1    # | Longsword (1)
        ```

        Note that the text after the # on each line does not matter. It's only comments for human information, and the game doesn't actually care about them. I've updated the text to reflect the changes I've made for your benefit. The only important part are the item codes. The new codes were copied from `edit/object.txt`.

        Looks like the constant `MAX_START_ITEMS` is defined elsewhere as 4. So let's add one more item to our Edain, why not!

        ```
        E:37:4:1:1     # [ Mail Corslet (1)
        ```

        Remember to save the file. Let's take one more look at the actual code. In `src/birth.c`, we can see a function `static int get_start_xp(void)` that uses the constant `PY_START_EXP`. Searching for that name brings us to `src/defines.h`, line 376:

        ```
        #define PY_START_EXP 5000 /* Starting exp */
        ```

        Just for fun, let's bump that up a little:

        ```
        #define PY_START_EXP 25000 /* Starting exp */
        ```

        And, after saving the file, we're finished for now.

  9. Having made our changes, we build the executable again (making sure that the game isn't still running), and launch it again:

      ```sh
      cd src
      make -f Makefile.std install
      cd ..
      ./silx
      ```

      And...

      [image 3]

      Hey presto! Our Edain character now has more appropriate provisions for an expedition into Angband, including a longsword and some armour! And has five times the usual amount of XP to spend!

      Savour this feeling: you changed some text, bent the machine's workings to your will, and now see your work manifested on the screen! Contemplate the vast ramifications of this single tiny moment: if you can do this, then there's nothing you cannot also do if you can imagine it.

  10. That's it. **You've modified a game!**

    - All that remains is to commit the changes and push them to your git repository. You can also try out the other compilation options, build the game for Windows, and share packaged ready-to-play binaries with other people, but none of that is strictly necessary.

    - If you followed along with the changes made in this tutorial, **it's now up to you to come up with the changes YOU want to make**. Neither I nor anyone else can tell you what you can or cannot change about this game, as long as you don't breach the terms of the license. If you want to play an Ent with flamethrower arms - well, you can make that happen, regardless of anyone else's opinion about the idea's balance, thematic appropriateness, or even common sense. Of course, they're not obliged to play your version of the game, but if you're making something that you'll enjoy yourself, then there's no need to spare a thought for any potential audience.

        That's the fantastic thing about open software: you can shape it the way _you_ want, and unless you aspire to merge your changes into the original codebase, you can go as crazy as you want without worrying about stepping on anyone's toes.

        Still, it would be nice if you'll let the community know about your mod when it's made available. Even if I won't personally invest time into playing it, it's always fun to read about other peoples' creative ideas and check out their projects! By sharing, you're contributing to the liveliness of the larger roguelike community, and the FOSS game modding/developing community in general.

        Now, let your creativity run wild. It's time to experiment and have fun. Happy hacking!







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
