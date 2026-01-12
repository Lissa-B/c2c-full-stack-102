# Lesson 1 — IDE Work

## Create a React app (Codespaces)

1. Open **Terminal** in your Codespace.
2. (Optional) Verify Node/npm are available:
   ```bash
   node -v
   npm -v
   ```
3. Navigate to the Lesson 1 directory:
   ```bash
   cd /workspaces/c2c-full-stack-102/lesson-1
   pwd
   ```
4. Create the React app inside this directory (this will create a new subfolder called `my-app`):
   ```bash
   npx create-react-app my-app
   ```
5. Move into the new app folder:
   ```bash
   cd my-app
   ```
6. Start the development server:
   ```bash
   npm start
   ```
7. View the running app:
   - In Codespaces, open the **Ports** tab and open the forwarded port (typically `3000`) in the browser.

## Independent work: update the page content

1. In VS Code Explorer, open:
   - `my-app/src/App.js`
2. Inside `App.js`, find the element that looks like this:

   ```jsx
   <div className="App">...</div>
   ```

3. Replace everything inside `<div className="App">` with an `<h1>` element.
4. Remove any unneeded imports at the top of the file.
5. Add a list of the top 3 snacks you’d recommend to a friend.
6. Add a title above your snack list and use inline styling to make it red.

If `npm start` is still running, save your file and refresh the browser preview to see your changes.
