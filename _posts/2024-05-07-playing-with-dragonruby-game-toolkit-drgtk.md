---
layout: post
title: Playing with DragonRuby Game Toolkit (DRGTK)
date: 2024-05-07 20:26 -0300
---

Last week I was working on a very simple game to test [DragonRuby](https://dragonruby.org/toolkit/game) Game Toolkit and learn its API.

This Ruby implementation is based on [mruby](https://mruby.org/) and [LLVM](https://llvm.org/) and it’s commercial software but cheap.

The game toolkit has a simple API to handle sprites with animations, sound/music, inputs, basic drawing primitives like lines or rectangles, allows for HTTP requests  and other niceties. It also has a fixed resolution and 60 times a second game loop.

The engine has a game loop that calls to a **#tick** method where you draw, check inputs and update game logic.

The code is structured in a couple of classes, the main one is the MyGame, that contains other classes like the Player, the list of FloorFires and the Coin to collect.

When it detects a collision it plays a sound. The game is paused when the window loses focus.

For collision detection, it uses some builtin rectangle intersection primitives.

For Input handling, the keyboard, arrows/WASD are handled the same.

And that’s pretty much it. There are a lot to improve the game but I think I’ll start a new project with a better idea to start doing something funnier and more polished.

Besides [the official docs](https://docs.dragonruby.org/static/docs.html), I learnt a lot from [this post series](https://dev.to/presidentbeef/series/16166), kudos to Justin!, and also the [official discord](https://discord.com/channels/608064116111966245/) is super friendly and helpful when you get stuck on something.

#### The start screen:

![start screen](https://i.ibb.co/hs791w4/clideo-editor-3c8662f134954b7885542cdacc289741.gif)

#### In-game:

![in-game animation](https://i.ibb.co/XjCPhQ5/clideo-editor-4a7788b1097a4da2a8b419cc1fa6f7fa.gif)

The source is about 360 lines and available [on GitHub](https://github.com/megatux/dragonruby_the_fire/). The assets are free.

You can download or play it online [here](https://megatux.itch.io/the-fire) at Itch.io (DRGTK includes a great tool to deploy to Itch.io)

Happy hacking!
