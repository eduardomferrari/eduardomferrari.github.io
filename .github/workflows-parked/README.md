Workflows parked on 2026-10-07 (rollback of the activation in PR #3).
Reason: the first scheduled cycle never fired at this repository (zero event=schedule runs in 2h20m),
so the platform schedules were returned to the corporate repository, where they ran reliably.
Only deploy-pages.yml stays active here. Never run the same workflow in both repositories:
move files back to .github/workflows/ in the same commit/window that parks the corporate copies.
