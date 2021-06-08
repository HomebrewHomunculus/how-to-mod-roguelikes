# How to Mod Roguelikes: Creating Your Own Variant of Sil-Q

Have you ever wanted to modify a game? What about make your own game?

Modifying an existing game is the easiest way there is to make a game.
It's a fantastic exercise in both _gameplay design_ and _game programming_.
You avoid the "blank page" problem, because it's so easy to find a "what if"
question about an existing game, or an "I'd like it even more if" statement.
Also, all
the boring things like menus and savegames have already been implemented,
so you can focus on actual gameplay changes.

Many **roguelike games** are free and open source software (FOSS), available
under various licenses. This means that we are legally free to modify
the game and distribute our own variants of it - as long as we follow
the terms of the particular license provided with that game.

#### What this is

This article is a walkthrough for creating your own variant of
an open-source roguelike game. We will be using **_Sil-Q_** as an example -
but the general process is the same for many other games, such as _Brogue_
and _Angband_.
You can find a list of game ideas to mod in [GAMELIST.md](GAMELIST.md).

Roguelike fans often refer to variants of a game as "forks", from the
version control practice of creating parallel versions of a project.

Many of the popular roguelikes of today - _DCSS_, _NetHack_,
_Angband_, _Brogue CE_ - are forks of earlier games. In fact,
 _Sil-Q_ - the game we will be modifying today - is itself a fork of
_Sil_, which is a heavily modified variant of _NPPAngband_, which
is a fork of _Angband_.


#### What this isn't

This guide will follow each step
on the way towards having your own roguelike fork to play. Along the way,
I will point out some potential pitfalls, and explain my thought process
when diving into an unfamiliar codebase.

However, this article will not teach you to program in general.

This article will not
teach you what `git` is or how to use it.
Some tools will be recommended,
but you will have to find and install those tools yourself.

If you're not familiar with Sil, an introduction will not be
provided here, but you can read
[the Sil manual](https://github.com/sil-quirk/sil-q/blob/master/Sil%201.3%20Manual.pdf)
to learn more.

Also note that this guide is not legal advice - always consult the specific
license of the software you're modifying.


## Learning by doing: Create your own mod of _Sil-Q_

### To proceed with the tutorial, [click here](HOWTOMOD_SIL-Q.md).
