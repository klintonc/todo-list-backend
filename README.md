# Todo List — Backend

REST API for the [Todo List frontend](https://github.com/klintonc/todo-list-frontend). Built with **Express**, **TypeScript**, **Prisma** and **PostgreSQL**. Each todo can have subtasks.

## Tech stack

| Layer      | Tool                         |
| ---------- | ---------------------------- |
| Runtime    | Node.js 14.x                 |
| Framework  | Express 4                    |
| Language   | TypeScript                   |
| ORM        | Prisma 2                     |
| Database   | PostgreSQL                   |
| Validation | Joi                          |
| Testing    | Jest + Supertest             |

## Getting started

### Prerequisites

- Node.js 14.x
- Yarn 1.x
- A PostgreSQL database

### 1. Install dependencies

```bash
yarn install
```

### 2. Configure environment variables

Copy the example file and fill in your own database connection string:

```bash
cp .env.example .env
```

| Variable   | Description                         | Example                                              |
| ---------- | ----------------------------------- | ---------------------------------------------------- |
| `PORT`     | Port the server listens on          | `4000`                                               |
| `PSQL_URI` | PostgreSQL connection string        | `postgres://USER:PASSWORD@HOST:5432/DATABASE`        |

> `.env` is git-ignored. Never commit real credentials.

### 3. Set up the database

```bash
npx prisma db push --preview-feature   # create the tables from prisma/schema.prisma
npx prisma generate                    # generate the Prisma client
```

### 4. Run the server

```bash
yarn build   # compile TypeScript to dist/
yarn dev     # run dist/server.js with nodemon
# or
yarn start   # run dist/server.js with node
```

The server runs at [http://localhost:4000](http://localhost:4000).

Tip: run `yarn watch` in a second terminal so `dist/` rebuilds on every change while `yarn dev` is running.

## Scripts

| Script               | Description                         |
| -------------------- | ----------------------------------- |
| `yarn build`         | Compile TypeScript into `dist/`     |
| `yarn watch`         | Compile in watch mode               |
| `yarn dev`           | Run the compiled server with nodemon|
| `yarn start`         | Run the compiled server             |
| `yarn test`          | Run the Jest test suites            |
| `yarn test:coverage` | Run the tests with a coverage report|

## API

Base URL: `http://localhost:4000/api/v1`

The only valid `status` values are `pending` and `completed`.

### Todo

| Method   | Endpoint     | Body                   | Description                           |
| -------- | ------------ | ---------------------- | ------------------------------------- |
| `GET`    | `/todo`      | —                      | List all todos with their subtasks    |
| `POST`   | `/todo`      | `{ "title": string }`  | Create a todo                         |
| `PATCH`  | `/todo/:id`  | `{ "status": string }` | Update a todo's status                |
| `DELETE` | `/todo/:id`  | —                      | Delete a todo and all its subtasks    |

### Subtask

| Method   | Endpoint        | Body                                     | Description               |
| -------- | --------------- | ---------------------------------------- | ------------------------- |
| `POST`   | `/subtask`      | `{ "todo_id": string, "title": string }` | Create a subtask for a todo |
| `PATCH`  | `/subtask/:id`  | `{ "status": string }`                   | Update a subtask's status |
| `DELETE` | `/subtask/:id`  | —                                        | Delete a subtask          |

### Responses

| Status | Meaning                                   |
| ------ | ----------------------------------------- |
| `200`  | Success                                   |
| `404`  | Todo or subtask not found                 |
| `422`  | Validation failed (details in `error`)    |
| `500`  | Server error                              |

Example:

```bash
curl -X POST http://localhost:4000/api/v1/todo \
  -H "Content-Type: application/json" \
  -d '{"title": "Buy groceries"}'
```

```json
{
  "message": "Created todo successfully",
  "todo": {
    "id": "3f2b...",
    "title": "Buy groceries",
    "status": "pending",
    "created_at": "2021-05-11T00:00:00.000Z",
    "subtasks": []
  }
}
```

## Data model

```
Todo                         Subtask
├── id          (PK)         ├── id          (PK)
├── title                    ├── title
├── status                   ├── status
├── created_at               ├── created_at
└── subtasks ──────────────▶ └── todo_id     (FK → Todo.id)
```

## Testing

The tests make real calls to the database set in `PSQL_URI`, so use a development or test database.

```bash
yarn test
```

## Prisma tools

```bash
npx prisma studio   # browse and edit data in the browser
```

## Project structure

```
src/
├── app.ts            # Express app setup and middleware
├── server.ts         # Starts the HTTP server
├── routes/           # Route definitions
├── controller/       # Request handlers (todo, subtask)
├── validations/      # Joi input validation
└── utils/            # Prisma client, UUID helper
prisma/schema.prisma  # Database schema
test/                 # Jest + Supertest suites
```

## License

MIT
