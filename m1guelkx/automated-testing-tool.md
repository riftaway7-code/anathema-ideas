**idea:** an automated test tool: one command that downloads the required macOS pieces from Apple, sets up the components, runs all available tests, attempts a kernel boot and generates a report.

**status:** partly covered. Aiyan's PR #2 already downloads and unpacks the KDK files, links the kext collection and builds the boot stick. Remaining: a wrapper that also runs the emulator boot tests and writes a report. Good first task for a contributor (see TODO.md, "Tooling").
