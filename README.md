# Tracing a Response from `w.Write()` to the Wire

## Teryn Guzman & Kelvin Gordon

## Overview

This repository contains the Go code and lab document for exploring how HTTP responses travel from an application's `http.ResponseWriter` to the network socket.

The lab demonstrates how response buffering, HTTP headers, response flushing, and handler execution affect when a client receives data.

## Learning Objectives

- Understand how Go's `net/http` package handles HTTP responses.
- Observe the difference between writing data to a response writer and sending it through a socket.
- Investigate response buffering and `Flush()`.
- Understand when HTTP status codes and headers become fixed.
- Compare normal error handling with errors that occur after a response has started.

## Requirements

- Go 1.22 or later
- `curl`
- A terminal

## Project Structure

- `main.go` — Go HTTP server and logging instruments.
- `go.mod` — Go module definition.
- `docs/tracing-response-to-wire-lab.pdf` — Original lab instructions.
- `.gitignore` — Files excluded from Git.

## Setup and Run

Clone the repository:

```bash
git clone https://github.com/Teryn-Guzman/tracing-response-to-wire.git
cd tracing-response-to-wire
```

Check your Go installation:

```bash
go version
curl --version | head -1
```

Build and validate the project:

```bash
go vet ./...
go build -o wirelab .
```

Start the server:

```bash
./wirelab
```

Keep the server running and open a second terminal to send requests.

## Lab Endpoints

| Endpoint | Purpose |
|---|---|
| `/hello` | Observe a small HTTP response. |
| `/drip` | Observe how delayed writes are buffered. |
| `/pieces?n=100` | Examine many small writes. |
| `/big?kb=64` | Examine a large response. |
| `/hold?flush=0&pause=1500` | Observe a delayed response without flushing. |
| `/hold?flush=1&pause=1500` | Compare the response with flushing enabled. |
| `/late-header` | Observe what happens when a header is set too late. |
| `/late-error` | Examine an error after a response has started. |
| `/safe-error` | Compare safer error handling. |

## Testing

Use `curl` to inspect response headers and bodies:

```bash
curl -si http://localhost:8080/hello
curl -si http://localhost:8080/late-header
curl -si http://localhost:8080/late-error
curl -si http://localhost:8080/safe-error
```

For the delayed response experiments:

```bash
curl -si "http://localhost:8080/hold?flush=0&pause=1500"
curl -si "http://localhost:8080/hold?flush=1&pause=1500"
```

## Key Takeaway

Calling `Write()` does not necessarily mean the response has already reached the client. Buffering, flushing, and the lifecycle of the HTTP handler influence when response bytes reach the network.

## Reference

See the lab PDF in the `docs/` directory for the full instructions and experiments.