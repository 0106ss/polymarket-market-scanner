# Architecture

`ScannerRuntime` owns one shared async HTTP pool, the three public REST clients, one Market WebSocket, in-memory current market/book state, and a single SQLite database object. Lifespan startup initializes storage, performs bounded public checks, then starts exactly one market refresh loop, REST fallback loop, and WebSocket loop. Shutdown cancels all tasks, closes sockets/HTTP, and disposes the database engine.

Market normalization is separated from network code. Orderbooks are normalized and sorted before the depth calculator sees them. The calculator is a pure Decimal function; the scanner rule layer decides whether a result is valid. Persistence and FastAPI presentation consume immutable calculation results.

The browser never calls Polymarket directly. It only consumes local FastAPI JSON and rendered templates.
