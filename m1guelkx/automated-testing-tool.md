**idea:** an automated test tool: one command that downloads the required macOS pieces from Apple, sets up the components, runs all available tests, attempts a kernel boot and generates a report.

**addition (m1guelkx):** it should support **Windows** too, which would grow the pool of people who can test by a lot.

**status:** partly covered. Aiyan's PR #2 already downloads and unpacks the KDK files, links the kext collection and builds the boot stick, in pure Rust with a Windows setup tool. Remaining: a wrapper that also runs the boot tests and writes a report, working on Windows. Good first task for a contributor (see TODO.md, "Tooling").
