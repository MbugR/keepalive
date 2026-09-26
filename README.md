# keepalive

A scheduled HTTP GET.

The target address is stored as the repository secret `WAKE_URL`. It is not written in this repository.

Each run sends the GET, waits about 200 seconds, and starts the next run with the built-in `GITHUB_TOKEN`. No personal token is needed; the old `DISPATCH_TOKEN` secret can be deleted. The `concurrency` group keeps a single chain, so the backup cron cannot start a second one.

To stop the chain, cancel the running run. To start it again, run the workflow by hand or wait for the backup cron.

The schedule can turn itself off if this repository has no activity for 60 days. The weekly activity workflow updates `activity.txt` so that does not happen.
