# Short URL · backend

The API behind [the URL shortener](https://github.com/Yeisonfjrd/Short-url-frontend). Go, Gin and PostgreSQL through GORM, deployed on Railway with Docker.

```
POST /api/shorten            {"url": "https://..."}  ->  {"short_url": "https://.../Ab3x..."}
GET  /:shortCode             302 to the original URL, counts the visit
GET  /api/stats/:shortCode   visits, last visit, created at
GET  /health                 used by Railway's health check
```

## How it works

The short code is the first 8 bytes of the URL's SHA-256, base64url encoded. The same URL always produces the same code, so before inserting I look the URL up and return the existing code if it's already there. `short_code` has a unique index, so a collision fails loudly instead of overwriting someone else's link.

## Running it

```bash
export DATABASE_URL="postgres://user:pass@localhost:5432/shortener"
export BASE_URL="http://localhost:8080"
go run ./src
```

GORM creates the table on startup. With Docker: `docker build -t short-url . && docker run -p 8080:8080 -e DATABASE_URL=... short-url`.

## Decisions

- **302, not 301.** A 301 gets cached by the browser, so after the first click the request never reaches the server and the visit counter undercounts. It was a 301 at first.
- **The visit count goes up in the database.** It used to read `visits`, add 1 in Go and write it back, so two clicks at the same time could count as one. Now it's `UPDATE ... SET visits = visits + 1` through `gorm.Expr`.
- **Codes have no base64 padding** (`RawURLEncoding`): 11 characters, no trailing `=`.

## Still missing

- **No URL validation.** Anything non-empty gets stored, including strings that aren't URLs.
