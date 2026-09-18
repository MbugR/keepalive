# keepalive

A scheduled HTTP GET.

The target address is stored as the repository secret `WAKE_URL`. It is not written in this repository.

The schedule can turn itself off if this repository has no activity for 60 days. The weekly activity workflow updates `activity.txt` so that does not happen.
