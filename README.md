# DBMS_07 – From Database to API

**Module:** Databases
**Topic:** Python, `psycopg2`, PostgreSQL, FastAPI, and REST APIs

This repository contains my work for DBMS_07.
The exercise shows how to move from basic Python usage to database access and finally to a small REST API using FastAPI.

---

## Learning Goals

In this exercise, I learned how to:

* use the Python REPL
* write and run Python scripts
* use standard library modules such as `math`
* work with virtual environments using `uv`
* install and manage third-party packages
* connect Python to PostgreSQL using `psycopg2`
* build a minimal FastAPI application
* expose database data through HTTP endpoints
* understand why an API is useful as an abstraction layer over a database

---

## Prerequisites Check

The following commands were used to check the required tools:

```bash
python3 --version
git --version
pg_isready
```

All commands should work before starting the exercise.
If PostgreSQL is not running, it can be started with:

```bash
sudo systemctl start postgresql
```

### Screenshot 1

![Screenshot 1](screenshots/screenshot_01_prerequisites.png)

---

# 1. The Python REPL

The Python REPL is an interactive environment where Python commands can be tested directly.
It is useful for quick experiments because the result is shown immediately.

Examples used in the REPL:

```python
7 + 3
144 / 12
2 ** 10
```

With `print()`:

```python
print(7 + 3)
print(144 / 12)
print(2 ** 10)
```

Example using an f-string:

```python
pages = 312
price = 18.90
print(f"Das Buch hat {pages} Seiten und kostet {price:.2f} Euro.")
```

### Screenshot 2

![Screenshot 2](screenshots/screenshot_02_repl.png)

---

## Questions for Section 1

### Question 1.1

**In the REPL, typing `2 ** 10` without `print` still shows `1024`. Why does this work in the REPL but not in a script file?**

In the REPL, Python automatically displays the result of an expression because it is interactive.
So when I type `2 ** 10`, the REPL evaluates it and directly shows `1024`.

In a script file, Python also calculates the value, but it does not display it automatically.
To show something in a script, I need to use `print()`.

---

### Question 1.2

**The f-string format specifier `:.2f` controls how `price` is displayed. What does it mean, and what would `:.4f` produce for `18.9`?**

The format specifier `:.2f` means that the number is shown with two digits after the decimal point.
For example:

```python
18.9
```

is displayed as:

```text
18.90
```

If I use `:.4f`, then `18.9` would be displayed as:

```text
18.9000
```

---

# 2. Importing a Standard Library Module

Python already includes many modules in its standard library.
These modules do not need to be installed separately.

Example with the `math` module:

```python
import math

math.sqrt(144)
math.floor(3.7)
math.ceil(3.2)
math.pi
print(f"Der Umfang eines Kreises mit r=5 beträgt {2 * math.pi * 5:.4f}")
```

Using `math.` before the function name makes it clear that the function comes from the `math` module.

---

## Questions for Section 2

### Question 2.1

**Python also allows `from math import sqrt`, after which you can write `sqrt(144)` without the `math.` prefix. What is the drawback of this style compared to `import math`?**

The drawback is that it is less clear where the function comes from.
When I write:

```python
math.sqrt(144)
```

I can immediately see that `sqrt` belongs to the `math` module.

But if I only write:

```python
sqrt(144)
```

it may be unclear where `sqrt` was imported from.
It can also cause name conflicts if another function with the same name exists in the program.

---

### Question 2.2

**The standard library is always available — it requires no installation. Name two other standard library modules and describe in one sentence what each one is used for.**

One example is `datetime`.
It is used to work with dates and times, for example the current date or a birthday.

Another example is `os`.
It is used to interact with the operating system, for example file paths, folders, or environment variables.

---

# 3. A Python Script File

A Python script is useful when the code should be saved and executed again later.

The project directory was created with:

```bash
mkdir ~/dbms07_intro
cd ~/dbms07_intro
git init
```

The script file is called:

```text
berechnung.py
```

Example content:

```python
import math

def kreisflaeche(r):
    return math.pi * r ** 2

def main():
    radius = 7
    flaeche = kreisflaeche(radius)
    print(f"Radius:  {radius}")
    print(f"Fläche:  {flaeche:.4f}")
    print(f"Wurzel:  {math.sqrt(radius):.4f}")

if __name__ == "__main__":
    main()
```

The script was run with:

```bash
python3 berechnung.py
```

### Screenshot 3

![Screenshot 3](screenshots/screenshot_03_script_output.png)

---

## Questions for Section 3

### Question 3.1

**The script wraps its logic in a `main()` function and calls it only under `if __name__ == "__main__"`. What is `__name__` set to when the file is run directly? What is it set to when the file is imported by another module — and why does this distinction matter?**

When the file is run directly, `__name__` is set to:

```python
"__main__"
```

When the file is imported by another Python file, `__name__` is set to the module name.

This matters because the code inside:

```python
if __name__ == "__main__":
    main()
```

only runs when the script is started directly.
If the file is imported, the functions can be reused, but the main program does not start automatically.

---

### Question 3.2

**The `kreisflaeche` function could be defined without importing `math` by hard-coding `3.14159` instead of `math.pi`. Give one concrete reason why using `math.pi` is preferable.**

Using `math.pi` is better because it is more precise than typing an approximate value manually.
It also makes the code easier to understand because the reader immediately knows that the value represents π.

---

# 3.1. Shebang and File Permissions

A shebang line tells the operating system which interpreter should run the file.

The shebang line added at the top of the script is:

```python
#!/usr/bin/env python3
```

After that, the file was made executable:

```bash
chmod +x berechnung.py
```

Then the script can be started directly:

```bash
./berechnung.py
```

The command:

```bash
chmod +x berechnung.py
```

adds execute permission to the file.
Without this permission, Linux will not run the file as a program.

The command:

```bash
ls -l berechnung.py
```

can be used to check the permissions.

---

# 4. Third-Party Libraries and uv

Python has many built-in modules, but some libraries are third-party packages.
One example is `rich`, which is used for nicer terminal output.

Trying to import it before installation gives an error:

```python
import rich
```

Possible error:

```text
ModuleNotFoundError: No module named 'rich'
```

Third-party packages should not be installed system-wide with plain `pip`, because this can create version conflicts and can affect the system Python installation.

Instead, this exercise uses `uv`.

`uv` creates a virtual environment and installs project dependencies safely.

Installation check:

```bash
uv --version
```

### Screenshot 4

![Screenshot 4](screenshots/screenshot_04_uv_version.png)

---

# 4.2. Project with uv and rich

The project configuration file is called:

```text
pyproject.toml
```

Example:

```toml
[project]
name = "dbms07-intro"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "rich",
]
```

Dependencies were installed with:

```bash
uv sync
```

The script was then run with:

```bash
uv run python3 berechnung.py
```

### Screenshot 5

![Screenshot 5](screenshots/screenshot_05_rich_output.png)

---

## Questions for Section 4

### Question 4.1

**`uv sync` creates a `uv.lock` file in addition to `pyproject.toml`. What is the difference between the two files? Why should `uv.lock` be committed to version control while generated files like `.venv/` should not?**

`pyproject.toml` describes the project and lists the dependencies that the project needs.
For example, it says that the project needs the package `rich`.

`uv.lock` stores the exact versions of all installed dependencies.
This is important because it makes the project reproducible on another computer.

The `uv.lock` file should be committed to Git so that everyone uses the same package versions.
The `.venv/` folder should not be committed because it is generated automatically, can be large, and is specific to one machine.

---

### Question 4.2

**`uv run python3 berechnung.py` uses the virtual environment's Python. What would happen if you ran `python3 berechnung.py` directly without `uv run` and `rich` is not installed system-wide?**

The script would fail with an error like:

```text
ModuleNotFoundError: No module named 'rich'
```

This happens because `python3 berechnung.py` uses the normal system Python.
If `rich` is only installed inside the virtual environment, the system Python cannot find it.

Using:

```bash
uv run python3 berechnung.py
```

solves this problem because it runs the script inside the project environment.

---

# 5. Connecting to PostgreSQL from Python

In this section, Python is used to connect to the PostgreSQL database `bibliothek`.

The project was created with:

```bash
mkdir ~/dbms07_bibliothek
cd ~/dbms07_bibliothek
git init
```

The dependencies are:

```toml
[project]
name = "dbms07-bibliothek"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "psycopg2-binary",
    "rich",
]
```

The dependencies were installed with:

```bash
uv sync
```

The script `abfrage.py` connects to the database, runs a SQL query, and displays open loans in a table.

The script was run with:

```bash
uv run python3 abfrage.py
```

### Screenshot 6

![Screenshot 6](screenshots/screenshot_06_database_query.png)

---

## Questions for Section 5

### Question 5.1

**The script uses a cursor object to execute the SQL query. What is the role of a cursor in the database connection model? Why is one connection able to hold multiple cursors simultaneously?**

A cursor is used to send SQL queries to the database and to receive the results.
The connection is the link between the Python program and the database server.
The cursor is like a tool that works through this connection.

One connection can have multiple cursors because different queries can be handled separately while still using the same database connection.

---

### Question 5.2

**The connection parameters are written directly in the script as `DB_CONFIG`. Why is this a security problem in a real project? Name one common alternative for storing credentials outside the source code.**

Writing database credentials directly in the source code is a security problem because the password could be exposed.
For example, if the code is pushed to GitHub, other people may be able to see the username and password.

A common alternative is to store credentials in environment variables.
Another option is to use a `.env` file and make sure that this file is not committed to Git.

---

### Question 5.3

**`cursor.fetchall()` returns a list of tuples. The script accesses `row[0]`, `row[1]`, etc. by index. What is the risk of this approach, and which `psycopg2` cursor subclass would return named columns instead?**

The risk is that the code depends on the exact order of the columns in the SQL query.
If the query changes, for example if the column order changes, then `row[0]` or `row[1]` may suddenly contain a different value.

A better solution is to use:

```python
psycopg2.extras.RealDictCursor
```

This cursor returns each row as a dictionary with column names.

---

# 6. Minimal FastAPI Application

In this section, a small FastAPI application was created.

The project was created with:

```bash
mkdir ~/dbms07_api
cd ~/dbms07_api
git init
```

The dependencies are:

```toml
[project]
name = "dbms07-api"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "fastapi",
    "uvicorn[standard]",
]
```

The application file is called:

```text
main.py
```

Minimal example:

```python
#!/usr/bin/env python3

from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def root():
    return {"nachricht": "Bibliotheks-API läuft."}
```

The server was started with:

```bash
uv run uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

The endpoint was tested with:

```bash
curl http://localhost:8000/
```

Expected output:

```json
{"nachricht":"Bibliotheks-API läuft."}
```

FastAPI also provides interactive documentation at:

```text
http://localhost:8000/docs
```

### Screenshot 7

![Screenshot 7](screenshots/screenshot_07_fastapi_root.png)

---

## Questions for Section 6

### Question 6.1

**FastAPI generates interactive documentation at `/docs` automatically. What standard does it use to describe the API, and what advantage does machine-readable API documentation have over a PDF?**

FastAPI uses the OpenAPI standard to describe the API.

Machine-readable documentation is better than a normal PDF because tools can understand it automatically.
For example, developers can test endpoints directly in the browser, generate client code, and see request and response formats clearly.

A PDF is only a static document, but OpenAPI documentation can be used directly by humans and software tools.

---

### Question 6.2

**The `--reload` flag is useful during development but should not be used in production. Why?**

The `--reload` flag watches the files and restarts the server whenever the code changes.
This is helpful during development, but it should not be used in production.

In production, the server should be stable and controlled.
`--reload` uses extra resources and may restart the application unexpectedly.

---

# 7. Wiring the Database into FastAPI

In this section, the PostgreSQL database was connected to the FastAPI application.

The dependency `psycopg2-binary` was added:

```toml
[project]
name = "dbms07-api"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "fastapi",
    "uvicorn[standard]",
    "psycopg2-binary",
]
```

The following endpoints were created:

```text
GET  /buecher
GET  /ausleihen/offen
POST /mitglieder
```

The server was started with:

```bash
uv run uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

The endpoints were tested with:

```bash
curl http://localhost:8000/buecher
```

```bash
curl http://localhost:8000/ausleihen/offen
```

```bash
curl -X POST http://localhost:8000/mitglieder \
     -H "Content-Type: application/json" \
     -d '{"nachname":"Richter","vorname":"Tom","geburtsdatum":"2000-06-15","email":"tom.richter@mail.de"}'
```

Posting the same e-mail twice should return a conflict error with status code `409`.

### Screenshot 8

![Screenshot 8](screenshots/screenshot_08_api_endpoints.png)

---

## Questions for Section 7

### Question 7.1

**Endpoint 3 uses parameterized queries. What would be the security risk of building the SQL string by concatenation? Name the attack this prevents.**

If SQL strings are built by concatenating user input, an attacker could insert malicious SQL code into the query.

This attack is called SQL injection.

Parameterized queries prevent SQL injection because user input is treated as data, not as part of the SQL command.

---

### Question 7.2

**The `RealDictCursor` in endpoints 1 and 2 returns each row as a dictionary instead of a tuple. Why does this make the API response more useful to a client that receives the JSON output?**

`RealDictCursor` makes the API response easier to understand because each value has a name.

Instead of receiving only a list of values like:

```json
["Müller", "Anna", "Datenbanken"]
```

the client receives a clear object like:

```json
{
  "nachname": "Müller",
  "vorname": "Anna",
  "titel": "Datenbanken"
}
```

This makes it easier for the frontend or another client to use the data correctly.

---

### Question 7.3

**A caller of `GET /ausleihen/offen` receives a list of open loans without knowing anything about the underlying table structure, join logic, or database credentials. Name two concrete advantages this abstraction provides compared to giving every caller direct database access.**

First, the caller does not need to understand the database structure or the SQL JOIN logic.
They only need to call one URL.

Second, it is more secure because the database credentials stay inside the backend application.
The API can control which data is returned and what actions are allowed.

---

# 8. Reflection and Outlook

## Question A – Separation of concerns

**The API now hides a four-table JOIN behind a single URL. A frontend developer can call `/ausleihen/offen` without knowing SQL. What is the general software engineering principle behind this, and where else in a typical application stack does the same principle appear?**

The principle is called separation of concerns.

It means that every part of the application has its own responsibility.
For example, the frontend is responsible for displaying data, the backend is responsible for logic, and the database is responsible for storing data.

The same principle also appears in layered architectures, such as:

```text
Frontend → Backend/API → Database
```

Each layer hides its internal complexity from the other layers.

---

## Question B – Stateless HTTP vs. database connections

**Each endpoint opens a new database connection and closes it after the query. In a production system with hundreds of simultaneous requests this would be inefficient. What is the standard solution, and which Python library provides it for `psycopg2`?**

The standard solution is connection pooling.

With connection pooling, the application does not open and close a new database connection for every request.
Instead, it reuses existing database connections from a pool.

For `psycopg2`, this can be done with:

```python
psycopg2.pool
```

Examples are:

```python
SimpleConnectionPool
ThreadedConnectionPool
```

---

## Question C – Authentication

**The API currently has no access control. Two common approaches are JWT tokens and Keycloak. What is the main operational difference between the two approaches?**

With JWT tokens, the API itself usually validates the token.
This is lightweight, but the application has to handle more authentication logic itself.

With Keycloak, authentication is handled by an external identity provider.
Keycloak manages users, login, roles, and permissions centrally.

The main difference is that JWT can be handled directly by the API, while Keycloak is a separate authentication system that manages identity for one or more applications.

---

## Question D – The abstraction chain

**You have now built a complete chain: raw data in PostgreSQL → SQL query in Python → JSON response from FastAPI → curl client. Describe in two sentences what each link in this chain contributes and why removing any one of them would make the system harder to use or maintain.**

PostgreSQL stores the raw data, SQL selects the needed information, Python executes the query, and FastAPI returns the result as JSON through an HTTP endpoint.
The curl client can request the data without knowing the database structure, which makes the system easier to use, test, and maintain.

If one part is removed, the system becomes harder to work with.
For example, without the API, every client would need direct database access and would need to know the SQL logic.

---

# Git Commands Used

Example commands for committing the work:

```bash
git add README.md
git commit -m "docs: add DBMS07 exercise answers"
git push
```

If screenshots are included in a folder:

```bash
git add README.md screenshots/
git commit -m "docs: add DBMS07 documentation and screenshots"
git push
```

---

# Conclusion

This exercise shows how a database can be accessed from Python and then exposed through a REST API.
The main idea is that the API hides the database complexity and gives clients a simple and safe way to access data.
