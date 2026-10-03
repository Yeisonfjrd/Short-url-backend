# Short URL · backend

The API behind [the URL shortener](https://github.com/Yeisonfjrd/Short-url-frontend). Go, Gin and PostgreSQL through GORM, deployed on Railway with Docker.

```
POST /api/shorten            {"url": "https://..."}  ->  {"short_url": "https://.../Ab3x..."}
GET  /:shortCode             301 to the original URL, counts the visit
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

## Things I'd change

- **The redirect is a 301.** Browsers cache it, so after someone's first click the request never reaches the server again and the visit counter undercounts. A 302 would make the stats honest.
- **Visits are read, incremented and written back.** Two clicks at the same time can count as one. `UPDATE ... SET visits = visits + 1` (`gorm.Expr`) fixes it.
- **The code keeps base64's `=` padding.** It works, but `RawURLEncoding` would give a cleaner 11-character code.
- **No URL validation.** Anything non-empty gets stored, including strings that aren't URLs.

`server.js` is an Express version of the same API. The Go one is what the Dockerfile builds and Railway runs.
