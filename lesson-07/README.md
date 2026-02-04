# Lesson 07: Database Connection

In this lesson you will connect a MySQL database to your full-stack app (Express + React) and insert data from your React input fields into your MySQL table.

Lesson plan reference: `/workspaces/c2c-full-stack-102/lesson_material/lesson-7__lesson_7.txt`

## Learning Objectives (SWBAT)

- Connect a MySQL database to a React app using a connection pool (`mysql.createPool()` / `mysql2.createPool()`)
- Insert data from input fields in React directly into a MySQL database

## Step 1: Sync Your Fork (GitHub UI)

Because you are working from a fork, sync it first so you have the latest lesson materials.

1. Open your fork of this repo on GitHub.
2. Click **Sync fork** (or **Fetch upstream**).
3. Complete the prompts to update your fork.

## Step 2: Copy the Client Directory from Lesson 06 → Lesson 07

Lesson 07 includes a server starter in `/workspaces/c2c-full-stack-102/lesson-07/app/server`. You only need to bring forward the React client from Lesson 06.

1. Make sure this folder exists: `/workspaces/c2c-full-stack-102/lesson-07/app/`
2. Copy only the `client` directory from Lesson 06 into Lesson 07.
3. Confirm you now have `/workspaces/c2c-full-stack-102/lesson-07/app/client/`.

Suggested command (run from anywhere):

```bash
mkdir -p /workspaces/c2c-full-stack-102/lesson-07/app
cp -r /workspaces/c2c-full-stack-102/lesson-06/app/client /workspaces/c2c-full-stack-102/lesson-07/app/
```

## Requirements / Resources

- MySQL INSERT reference (w3schools): https://www.w3schools.com/sql/sql_insert.asp

## Agenda

- Do Now: practice `INSERT INTO` in MySQL Workbench
- Section 1.1: MySQL database connection (server)
- Section 1.2: Test insert via a server route
- Section 1.3: Create a submit function (React → API)
- Section 2.1: Create `POST /api/insert` (server)
- Section 2.2: Capture input values with `onChange` (React)
- Section 2.3: Test end-to-end (React → Express → MySQL)

## Do Now (MySQL Insert)

In MySQL Workbench, create a small example table for customers and insert one row.

1. Create a database + table:

```sql
CREATE DATABASE IF NOT EXISTS do_now;
USE do_now;

CREATE TABLE IF NOT EXISTS customers (
   first_name VARCHAR(50),
   last_name VARCHAR(50),
   email VARCHAR(100),
   dob VARCHAR(20)
);
```

2. Insert sample data:

```sql
INSERT INTO customers (first_name, last_name, email, dob)
VALUES ('John', 'Doe', 'johndoe@example.com', '03-13-90');
```

## Section 1.1: MySQL Database Connection (server)

Open your server entry file (created in Lesson 06):

- `/workspaces/c2c-full-stack-102/lesson-07/app/server/index.js`

Install dependencies (add what you don’t already have):

```bash
cd /workspaces/c2c-full-stack-102/lesson-07/app/server
npm install mysql2 cors
```

Then create your connection pool near the top of `index.js`:

```js
const mysql = require("mysql2");

// connection pool to the MySQL database
const db = mysql.createPool({
  host: "localhost",
  user: "root",
  password: "password",
  database: "YOUR_DATABASE_NAME",
});
```

Make sure `database` matches the schema you created in MySQL.

## Section 1.2: Test the Connection with a Direct Insert

Edit your existing route in `/workspaces/c2c-full-stack-102/lesson-07/app/server/index.js` so it runs an INSERT.

Example (update schema/table/column names to match yours):

```js
app.get("/", (req, res) => {
  const sqlInsert =
    "INSERT INTO pkm_info (name, type) VALUES ('Pikachu', 'electric');";

  db.query(sqlInsert, (err, result) => {
    if (err) {
      console.log(err);
      return res.status(500).send("DB insert failed");
    }

    res.send("Inserted a test row");
  });
});
```

Run your server and hit `http://localhost:3001/` to confirm the row gets added.

## Section 1.3: Submit Function (React → API)

Open your React app file:

- `/workspaces/c2c-full-stack-102/lesson-07/app/client/src/App.js`

Install Axios:

```bash
cd /workspaces/c2c-full-stack-102/lesson-07/app/client
npm install axios
```

In `App.js`:

- Create two state variables (example: `pkmName` and `pkmType`)
- Create a submit function (example: `submitPokemon`) that does a POST to `http://localhost:3001/api/insert`

Example shape:

```js
const submitPokemon = () => {
  Axios.post("http://localhost:3001/api/insert", {
    pkmName: pkmName,
    pkmType: pkmType,
  }).then(() => {
    alert("successful insert");
  });
};
```

## Section 2.1: Handling the Data Insert (server `POST /api/insert`)

In `/workspaces/c2c-full-stack-102/lesson-07/app/server/index.js`:

1. Make sure you have middleware:

```js
app.use(cors());
app.use(express.json());
app.use(bodyParser.urlencoded({ extended: true }));
```

2. Add a POST route that pulls values from `req.body` and runs a parameterized INSERT:

```js
app.post("/api/insert", (req, res) => {
  const pkmName = req.body.pkmName;
  const pkmType = req.body.pkmType;

  const sqlInsert = "INSERT INTO pkm_info (name, type) VALUES (?, ?)";

  db.query(sqlInsert, [pkmName, pkmType], (err, result) => {
    if (err) {
      console.log(err);
      return res.sendStatus(500);
    }

    res.sendStatus(200);
  });
});
```

Update table/column names to match your database.

## Section 2.2: Grab Input Data with `onChange` (React)

In `/workspaces/c2c-full-stack-102/lesson-07/app/client/src/App.js`, update each `<input>` so it sets state:

```js
<input
  type="text"
  name="pkmName"
  onChange={(event) => {
    setPkmName(event.target.value);
  }}
/>
```

Do the same for your type field.

## Section 2.3: Testing (end-to-end)

1. Start the server (port 3001):

```bash
cd /workspaces/c2c-full-stack-102/lesson-07/app/server
npm run devStart
```

2. Start the client (port 3000):

```bash
cd /workspaces/c2c-full-stack-102/lesson-07/app/client
npm start
```

3. Submit a few entries in the UI, then verify the rows appear in MySQL Workbench.

---

Tip: If inserts fail, check the server terminal logs first (SQL errors + schema/table/column mismatches are the most common issue).
