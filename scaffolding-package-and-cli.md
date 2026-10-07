Different approaches to scaffolding a new project (file structure) with a cli command:

## 1. [custom npm template package](https://dev.to/donnierich/create-your-custom-npm-template-package-1ai5)

`npm create <initializer>` or `npx create-<initializer>`

### next.js
`npm create vite@latest` or `npx create-vite`

When you run `npx create-next-app@latest`, it executes **create-next-app**, a specialized CLI tool written in TypeScript that is built and maintained directly within the official Next.js monorepo.

---

> Unlike many other modern scaffolding utilities (which often pull down repositories using tools like **degit** or rely on third-party generators like Yeoman), create-next-app is engineered to have **zero external dependencies** to guarantee speed, safety, and reliability. 
 
[what are 18 devDependencies in create-next-app's package.json???](https://github.com/vercel/next.js/blob/canary/packages/create-next-app/package.json#L29)

Looks like Gemini made a mistake, because `create-next-app` relies on **18 devDepencies**.

---

Here is exactly what it uses under the hood to manage the scaffolding process:

```
1. Interactive Prompts: picoinquirer

To handle the terminal questions (asking you about TypeScript, Tailwind CSS, App Router, etc.), Next.js uses an ultra-lightweight, internal clone or variant of terminal prompt libraries, primarily standardizing on packages like picoinquirer or optimized minimal prompt layers. This gives it a fluid, fast terminal user interface without bringing along heavy dependency chains.

**replace with prompts**
```

### 1. Interactive Prompts: prompts

...


### 2. Styling the Terminal: `picocolors`

To print terminal text in different colors (such as green text when a step succeeds or bold headings for instructions), the CLI relies on `picocolors`. This is a tiny, high-performance alternative to traditional libraries like `chalk`.

### 3. Template Engine: Pre-compiled Monorepo Directories

Rather than dynamically pulling template files over the internet for standard installations, the `create-next-app` source code contains a folder named [templates/](https://github.com/vercel/next.js/tree/canary/packages/create-next-app/templates).

- It features pre-configured base directories categorized by choices (e.g., App Router vs Pages Router, JavaScript vs TypeScript, Tailwind CSS vs vanilla CSS).

- When you finalize your CLI prompts, the tool uses Node.js's native file system capabilities ([fs.promises](https://github.com/vercel/next.js/blob/canary/packages/create-next-app/templates/index.ts)) to **recursively copy the correct template files** directly into your destination directory, swapping out placeholders (like the application name in `package.json`).

### 4. Remote Examples: tar

If you pass the `--example` flag (e.g., `npx create-next-app --example with-supabase`), the CLI shifts its behavior:

- It looks up the Vercel-maintained Next.js examples repository.
- Instead of running a full `git clone` (which copies the entire git history and slows the process down), it fetches the specific folder via the GitHub API as a compressed tarball.
- It uses the **[tar](https://github.com/isaacs/node-tar)** [npm package](https://www.npmjs.com/package/tar) to extract only the relevant example folder directly onto your machine.

### 5. Package Installation: Native CLI Spawning

Once the files are laid out, create-next-app automatically detects which package manager you used to invoke it (or defaults to standard options). It uses Node's child_process.spawn to run the installation script natively using your system's package manager (**npm**, **yarn**, **pnpm**, or **bun**) to download dependencies and generate the lockfile.
