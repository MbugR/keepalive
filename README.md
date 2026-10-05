# keepalive

A scheduled HTTP GET.

The target address is stored as the repository secret `WAKE_URL`. It is not written in this repository.

Each run first queues the next run with the built-in `GITHUB_TOKEN`, then sends the GET and waits about 200 seconds. The queued run stays pending until the current one ends, so runs go one after another about every 3.5 minutes. No personal token is needed; the old `DISPATCH_TOKEN` secret can be deleted.

The next run is queued before anything else, so the chain survives a run that dies halfway. On 2026-10-05 the wait got SIGTERM (exit code 143) before the next run was queued, and the old order (wait first, queue after) lost the chain. At the end each run checks that the next one is queued and queues it again if the first try failed. Runs always queue the next one on `main`, so a run started from another branch hands the chain back to `main`.

The `concurrency` group allows one running run and one pending run, so there is never a second chain. A cron run that arrives while a run is pending replaces it, and the replaced run shows as cancelled. That is normal.

## Is the chain alive?

It is alive if the newest run is in progress or queued, or finished less than 5 minutes ago. A failed or cancelled run is harmless when a newer run follows it.

If the newest run has finished and nothing new appears within 10 minutes, the chain has stopped. Start it by hand: Actions → keepalive → Run workflow (branch `main`), then check that the new run shows "In progress". If it stays "Queued" and later fails with "The job was not acquired by Runner of type hosted", GitHub had no runner for it (that happened on 2026-10-05 too); start it again once GitHub works.

The cron is only a weak backup: GitHub starts it a few times a day (37 times in the week to 2026-10-05), so do not wait for it.

## Stop and start

To stop the chain, disable the workflow (Actions → keepalive → ⋯ → Disable workflow). Cancelling a run does not stop it any more: the next run is already queued. After disabling, the run in progress and the queued one can still go for up to about 10 minutes; they cannot queue another and end as failed, which is expected. To stop at once, disable the workflow, then cancel the queued run and then the running one.

To start again, enable the workflow and run it by hand.

The schedule can turn itself off if this repository has no activity for 60 days. The weekly activity workflow updates `activity.txt` so that does not happen.
