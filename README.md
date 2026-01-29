# configloader

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

## Error Reporting

Reports all missing or invalid variables at once:

```
failed to load main.Config:
  error processing field Host (string): environment variable HOST not found
  error processing field Port (int): strconv.ParseInt: parsing "abc": invalid syntax
```

## Installation

```bash
go get github.com/hadrienk/configloader
```

Requires Go 1.23+.

## License

MIT
