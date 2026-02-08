# configloader

[![Go Reference](https://pkg.go.dev/badge/github.com/hadrienk/configloader.svg)](https://pkg.go.dev/github.com/hadrienk/configloader)
[![Go Report Card](https://goreportcard.com/badge/github.com/hadrienk/configloader)](https://goreportcard.com/report/github.com/hadrienk/configloader)
[![codecov](https://codecov.io/gh/hadrienk/configloader/branch/main/graph/badge.svg)](https://codecov.io/gh/hadrienk/configloader)

Load configuration from environment variables into Go structs.

```go
type Config struct {
    Host string
    Port int
}

var cfg Config
if err := configloader.Load(&cfg); err != nil {
    log.Fatal(err)
}
// Reads HOST and PORT from environment
```

# Working with Errors

No more frustrating debug cycles where you fix one missing environment variable only to discover another.

Unlike libraries that fail on the first error, configloader validates your entire configuration upfront and reports every issue in a single, clear error message:

```
failed to load main.Config:
  error processing field Host (string): environment variable HOST not found
  error processing field Port (int): strconv.ParseInt: parsing "abc": invalid syntax
  error processing field Database.URL (*url.URL): environment variable DATABASE_URL not found
  error processing field Database.MaxConns (int): environment variable DATABASE_MAX_CONNS not found
```

Errors returned by `Load()` implement the standard library's multi-error interface (`Unwrap() []error`), allowing you to programmatically inspect individual errors:

```go
if err := configloader.Load(&cfg); err != nil {
    // Print the complete error message
    fmt.Println(err)
    
    // Or inspect individual errors using errors.As
    var loadErr *configloader.ConfigLoadError
    if errors.As(err, &loadErr) {
        for _, e := range loadErr.Errors {
            var missingErr configloader.MissingEnvError
            if errors.As(e, &missingErr) {
                fmt.Printf("Missing required variable: %s\n", missingErr.Key)
            }
        }
    }
}
```

## Installation

```bash
go get github.com/hadrienk/configloader
```

Requires Go 1.23+.

## License

MIT
