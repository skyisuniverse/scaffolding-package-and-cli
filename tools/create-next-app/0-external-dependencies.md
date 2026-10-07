# 0 external (Production) dependencies

> You said that: 
>
> > 1. "Unlike many other modern scaffolding utilities (which often pull down repositories using tools like `degit` or rely on third-party generators like `Yeoman`), `create-next-app` is engineered to have **zero external dependencies** to guarantee speed, safety, and reliability." 
> 
> [but in their package.json they have 18 devDependencies](https://github.com/vercel/next.js/blob/canary/packages/create-next-app/package.json#L29): 
>
> 2. You said that `create-next-app` uses `picoinquirer`
>
> [but in their package.json it is absent](https://github.com/vercel/next.js/blob/canary/packages/create-next-app/package.json).

You caught two very important technical details here, and you are **100% correct** to point them out. Let's clarify what is actually happening with `create-next-app`'s architecture and correct the mistakes from my previous response.

### 1. The "Zero Dependencies" vs. 18 DevDependencies Paradox

You are completely right that the [package.json](https://github.com/vercel/next.js/blob/canary/packages/create-next-app/package.json) file explicitly lists **18 devDependencies**

The distinction here is between **production dependencies** (`dependencies`) and **development dependencies** (`devDependencies`):

- **Zero Production Dependencies**: If you look at the `package.json`, the `"dependencies"` block is completely missing. When you run `npx create-next-app`, your package manager downloads **zero nested external node_modules** over the network for the CLI itself. It executes instantly.

- **Why the devDependencies exist**: Look closely at their `"build"` script line. They use [@vercel/ncc](https://github.com/vercel/ncc). `ncc` is a build tool that compiles a Node.js project and **bundles all of its external dependencies directly into a single, self-contained JavaScript file** (`dist/index.js`).

So, while the developers use packages like [prompts](https://github.com/terkelg/prompts) and [commander](https://github.com/tj/commander.js) to write the tool, `ncc` **compiles and bakes all of that code directly into the final distributed file**. When you run it, it acts as a **standalone utility with zero external package installations required at runtime**.

### 2. Correction: What it actually uses for Interactive Prompts

I was wrong about `picoinquirer`. As you rightfully pointed out by checking the source code, `picoinquirer` is **completely absent** from their `package.json`.

Instead, the `package.json` reveals the actual tools it relies on for terminal interaction:
- **Interactive Prompts**: It uses the package **[prompts](https://github.com/terkelg/prompts)** (and its TypeScript types `@types/prompts`) to build the terminal questionnaire layout.
- **Terminal Arguments**: It uses **[commander](https://github.com/tj/commander.js)** to parse any command-line flags you pass in (like `--ts` or `--tailwind`).
- **Terminal Colors**: It does, in fact, use **[picocolors](https://github.com/alexeyraspopov/picocolors)** for text coloring, which is bundled in via ncc.

## How this affects your own Scaffolding Project

This is an incredibly smart architectural pattern for you to copy. If you want to build your custom scaffolding tool:

1. You can freely use easy, popular libraries like `prompts` (or `clack`) and `giget` as `devDependencies` to make your tool feature-rich.
2. You can use a compiler like `@vercel/ncc` to **bundle your script into a single** `dist/index.js` file before publishing it to npm.

This gives you the best of both worlds: writing code easily with great community tools, while providing your users with a lightning-fast CLI that has zero runtime dependencies.

Would you like to see how to configure `@vercel/ncc` in your own project to bundle your scaffolding script into a single standalone file just like Next.js does?
