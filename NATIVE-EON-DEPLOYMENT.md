# Native E.ON dependency image

Version 1.7.2 installs `pycognito==2024.5.1` at build time. Rebuilding the
add-on now retains the dependency required by the native E.ON Optimise client.
The build fails if the Cognito import or pinned version check fails. CI builds
and smoke-tests the amd64 image without any HA or energy-account credentials.

The launcher no longer prints the Supervisor token or passes it as a process
argument. Predbat reads the inherited `SUPERVISOR_TOKEN` environment variable.

This image packages dependencies only. It does not install the customised
AlphaESS Predbat source or change the planner configuration. Restore the
versioned runtime source and private configuration before starting a new
installation: an empty configuration otherwise bootstraps upstream Predbat.

For the existing installation, take an add-on backup first and preserve its
entire configuration directory. Installing this fork as a different HA add-on
creates a different configuration directory and identity; do not run both
controllers simultaneously. Stop the previous instance before restoring and
starting the replacement. Verify native price freshness, a newly published
plan, controller health and the configured discharge floor before retiring
the previous installation. Retain its backup for rollback.

The Cognito package is pinned; the complete OS and transitive Python dependency
graph is not hash-locked. This removes the manual in-container installation
requirement but is not a bit-for-bit reproducible image specification.
