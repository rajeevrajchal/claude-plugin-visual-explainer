# Other Mermaid types (use sparingly)

Use one of these only when the content really is this shape. When in doubt, a flowchart or a table is clearer.

## ER diagram: data model / schema relationships

Build it from the actual schema or migrations when available. Show keys and relationships, not every column.

```mermaid
erDiagram
    USERS ||--o{ PROJECTS : owns
    PROJECTS ||--o{ TASKS : contains
    USERS ||--o{ SESSIONS : has
    USERS {
        uuid id PK
        text email
    }
    PROJECTS {
        uuid id PK
        uuid owner_id FK
    }
```

## Class diagram: type/interface relationships in OO code

```mermaid
classDiagram
    class AuthProvider {
        <<interface>>
        +signIn(email, password) Session
        +signOut() void
    }
    class SupabaseAuth
    class MockAuth
    AuthProvider <|.. SupabaseAuth
    AuthProvider <|.. MockAuth
```

## Timeline: release or migration phases with dates

```mermaid
timeline
    title Auth migration
    Week 1 : Auth service behind flag
    Week 2 : Middleware on read routes
    Week 3 : All routes, remove old auth
```

## Mindmap: breaking down an open question or option space

```mermaid
mindmap
  root((Auth options))
    Managed
      Supabase Auth
      Auth0
    Self-hosted
      Lucia
      Custom JWT
```

ASCII fallback for all of these is an indented list or a two-column table. Don't try to draw them as ASCII art.
