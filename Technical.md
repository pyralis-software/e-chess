# Technical Details

ε Chess is an app built using React Native via Expo and runs in any browser as well as a "real" iOS and Android app.
It is a version of chess where the pieces can move fractional amounts, and was inspired by [AnalogChess](https://github.com/ehulinsky/AnalogChess).
The core technologies should allow us to run on MacOS and Windows as well, though some of the tooling is not quite
there yet. We are using the following high level things:

* [React Native](https://reactnative.dev/) is a cross platform framework that allows you to write 
  code in Javascript that uses native UI components on a number of platforms.
* [Typescript](https://www.typescriptlang.org/) is a "flavor" of Javascript with support for rich typing and thus
  type safety. I am not sure I will ever write a line of plain Javascript again. Typescript is transpiled down to
  normal Javascript, so even if your runtime doesn't support Typescript, you can use it. It will catch a bug or 10.
  I resisted Typescript originally, and I also thought Flow would win, but Typescript has become an incredible meta-language
  over the last 5 years. It's like having a runtime environment for your types, which turns out to be really useful
  in modern software development.
* [Expo](https://expo.dev/) is a development platform for react-native that provides a bunch of tooling to 
  make the process easier (common test clients, curated native components, upgrade tools, and etc), as well as
  a build infrastructure for continuous integration and deployment. This does not make the process of actually
  shipping apps *easy* because the App Stores make that a terrible experience. But it makes it a great deal simpler.
* [Firebase](https://firebase.google.com/) - is a set of tools from Google that have react-native and web support, 
  and their APIs are fairly similar so it's easy to write code that can work in both environments. Firebase has a number of
  available components, but we are using:
  * Analytics - not that there are any users, but it's nice to know when there are and what they use
  * Crashlytics - for the mobile apps, it is possible - nay probable, that they will crash. Crashlytics gives you
   analytics and additional information about why they crash.
  * Dynamic Links - Since we are building a multiplayer game, people will need to send around links to get people
    to join them in a game. Those users may or may not have the app installed. Dynamic links allow these links to go to
    the relevant app store on mobile, and then theoretically continue at the right place in the app after install.
    On desktop, they take the user to the web version of the game. This is actually immensely complicated to get right,
    and Google does it for free.
  * Remote Config - Sometimes you might want to switch features on and off depending on a number of factors. Multiplayer
    has some potential of costing us money, so if the game got a lot of usage, we might need to turn off that feature.
    Remote config makes this as simple as clicking a few buttons in the Firebase UI.
  * Realtime Database - I saved the best for last. Realtime Database is a document oriented database that can synchronize
    changes between multiple clients. This is a perfect match for our game. When one player makes a move, it simply
    updates a value on a shared document and the other player receives an event and can update their state. Chess is
    well suited for this mechanic because the legal moves can be easily understood by both sides.
* [legendapp/state](https://www.legendapp.com/open-source/state/) - All React applications need to carefully manage
  shared state and their impact on rendering. There are all sorts of [unholy](https://redux.js.org/) packages you can
  use for this, but legendapp is my new go-to for this problem. At my real job we chose Mobx State Tree, and it is also
  good, but the performance and simplicity of legendapp state wins for me. I would even suggest that we should've used
  it for my real job too, but it didn't exist at the time. It was a little tricky to get it working with expo+Typescript,
  mostly because everyone is trying to figure out what to do with [ESM](https://en.wikipedia.org/wiki/ECMAScript#6th_Edition_%E2%80%93_ECMAScript_2015).
  I hope they figure it out soon because right now it is the biggest threat to the success of the Javascript ecosystem.
* [Jest](https://jestjs.io/) - Since this is a father and son project, I wanted to teach good testing habits. Jest
  is a simple testing framework that (once you get it setup) generally gets out of the way and lets you write few lines
  to test your components. We tried to keep the chess logic very separated from the UI (obviously a good practice) so Jest
  allowed the junior developer to get working on things before there was a UI to create all the necessary situations.
* [GitHub Copilot](https://github.com/features/copilot) - is an AI that becomes your programming partner. One of us
  used it and one of us didn't, and it was stunning how good it was and how big a difference it made. It doesn't solve
  the problems for you, and it's not always right, but it's like having a medium developer sitting next to you and suggesting
  10 lines of code for every 1 you write. And the truth is in most code, that ratio is similar to the ratio of important or difficult
  lines to simple ones.
* [React Navigation](https://reactnavigation.org/) is a module for managing screens and transitions. This app is pretty simple, but it 
  made it easy to reorganize things without much code impact.
* [React Native Elements](https://reactnativeelements.com/) is a UI framework that gives you the things that React Native did not.
  Pretty buttons, lists, inputs, sliders - all the stuff we take for granted on the web but rewritten to work across platforms.

Here's a snapshot of the size and complexity of this app as of 2/6/2023. Lines of code is a debatable perspective, 
but it's useful to see how much work might be ahead of you if you're planning on building a production app with
similar complexity. It's also useful to see the amount of code in TSX (UI elements) vs TS (not UI) which is
about 50/50. That may say more about our style than the possibilities, but is at least useful for comparison.

```
===============================================================================
 Language            Files        Lines         Code     Comments       Blanks
===============================================================================
 JavaScript              4           49           36            6            7
 JSON                    6          273          273            0            0
 Markdown                1           13            0            7            6
 SVG                     1           70           66            0            4
 TSX                    19         1904         1738           14          152
 TypeScript             35         2062         1711          147          204
-------------------------------------------------------------------------------
 HTML                    3         4097         4086            0           11
 |- CSS                  3          127          109            0           18
 |- JavaScript           1            6            0            6            0
 (Total)                           4230         4195            6           29
===============================================================================
 Total                  69         8468         7910          174          384
===============================================================================
```
