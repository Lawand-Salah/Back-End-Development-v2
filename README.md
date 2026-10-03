# Back-End Development v2 — FastAPI Query & Path Parameters

A follow-on set of FastAPI learning exercises, building on the same car-sharing dataset from the v1 repo to practise query parameters, multiple filters, and path parameters.

## What's here

- **`cars.py`** — a progressive series of endpoints on the same in-memory car list, each adding one more layer of filtering via query parameters:
  - `GET /api/cars?size=` — filter by size only
  - `GET /api/cars2?size=&transmission=` — filter by size and transmission
  - `GET /api/cars3?size=&transmission=&fuel=` — adds a fuel filter
  - `GET /api/cars4?size=&transmission=&fuel=&doors=` — adds a `doors` filter, with `doors` typed as `int` so FastAPI converts and validates it automatically
  - `GET /api/cars5?size=&transmission=&fuel=&doors=` — same as above, but every parameter (including `doors`) is optional, using `int | None = None` typing
- **`cars2.py`** — a single endpoint demonstrating **path parameters** instead of query parameters: `GET /api/cars/{id}` returns one car by its id.

## Getting started

Each file is its own independent FastAPI app, run separately:

```bash
uvicorn cars:app --reload
```

or

```bash
uvicorn cars2:app --reload
```

The API will be available at `http://127.0.0.1:8000`, with interactive docs at `http://127.0.0.1:8000/docs`.

## A repo housekeeping note

As with the v1 repo, the Python virtual environment (`Virtual_environ/`) is committed alongside the real source files rather than excluded via `.gitignore`. Of the files in this repo, only `cars.py` and `cars2.py` are actually project code — everything else under `Virtual_environ/Lib/` is a third-party package. Adding a `.gitignore` entry for the venv folder (and `git rm -r --cached Virtual_environ`) would clean this up.

## Known issues

- **`cars.py` reuses the function name `get_cars` for all five endpoints.** This doesn't break anything functionally — each `@app.get(...)` decorator binds its route to the specific function object that exists at the moment it runs, so all five routes work correctly even though the name `get_cars` gets overwritten five times at module level. That said, it's worth naming them distinctly (`get_cars`, `get_cars_v2`, etc.) going forward: as written, only the last definition is reachable if anything tried to import and call `get_cars` directly (for a unit test, for example), and some linters will flag the redefinition.
- **`cars2.py` doesn't handle a missing id.** `car_by_id` does `result[0]` on whatever matches the filter; if no car has the given id, `result` is an empty list and `result[0]` raises an unhandled `IndexError`, which FastAPI turns into a generic 500 Internal Server Error rather than a clean 404. A `if not result: raise HTTPException(404, "Car not found")` before the return would fix this.

## Author

Lawand Salah
