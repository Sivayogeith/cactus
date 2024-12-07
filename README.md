## What is Cactus?
I created a UI for designing Game Acts or Events in the Murder Games—tailored for non-programmers!  

A Game Act defines an event in the Murder Games. Here’s a simple example:
```js
new Game.act(0.6, ["AED|animal_ken annoying"], "[1] is ded by cats")
```

#### Things can get pretty complex!
Check out this more advanced example:

```js
new G.act(
  newItemMult * 0.03,
  [
    'scrappy%0.5|vigilant%0.2|kind%0.2|bulky%0.2|lucky_coin%0.2|survivalist%0.2|any%0.5 !kite_shield'
  ],
  `[BLUE][1] somehow finds a knightly kite shield.<br>[1] practices fending off attackers with their new shield.`,
  p => {
    p[1].gain('kite shield');
  }
);
```
#### Meet Cactus

With Cactus (Cats + Acts), you can easily create and understand these acts—even if you don't know coding!

Special thanks to @CursedSliver for the idea to make this!
And thanks to ChatGPT (Nip) for helping me a lot!
## Developing

Once you've created installed dependencies with `npm install` (or `pnpm install` or `yarn`), start a development server:

```bash
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

## Building

To create a production version of the app:

```bash
npm run build
```

You can preview the production build with `npm run preview`.