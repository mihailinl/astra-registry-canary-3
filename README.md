# astra-registry-canary-3

**A test fixture. Nothing here is a plugin for users, and nothing here is
signed with a key any shipped Astra build trusts.** This repository is a
static source for one rehearsal, and nothing else. It has no workflows, no
secrets and no deploy keys, and it runs no bot.

## What it is for

The [Astra plugin registry](https://github.com/mihailinl/astra-registry)'s R2
exit (contract ROLL-60) needs the plugins service's publisher to serve one
rehearsal step end to end. That service runs a build that compiles the
registry's throwaway `tools/testkeys` roots: current {`root-a`, `root-b`},
next {}. The step is the first `signed` commit of a key-rotation series. The
service reads it from a branch named `signed`, fetches it from GitHub,
judges it, and serves it.

The series was cut twice before:

- at T0 2026-09-22 on
  [`mihailinl/astra-registry-canary`](https://github.com/mihailinl/astra-registry-canary);
- at T0 2026-09-26 on
  [`mihailinl/astra-registry-canary-2`](https://github.com/mihailinl/astra-registry-canary-2).

Both reached their hard end before the service's first serve. A `signed`
that has served a step can never be rewound (SERVE-18), the service compiles
the branch name `signed` in, and a first serve needs a `signed` with no
history. So the same series, cut a third time at **T0 = 2026-10-24**, needs a
repository of its own. This is that repository.

| Here | What it is |
|---|---|
| branch `signed` | The rotation line of the registry's `tools/testkeys/fixtures/rehearsal-r2c/`, one commit per step, each the exact commit the registry's signer made when the series was generated |
| branch `signed-compromise` | D10's compromise line, if it is walked: rotation steps 0-2, then `compromise/00-drop-2026a` |
| Pages | Serves branch `signed` (legacy build, root), so `https://mihailinl.github.io/astra-registry-canary-3/registry/v1/*.json` is always a step the branch carries |
| `main`'s merge of `refs/rehearsal-source/*` | The fixture generator's throwaway registry history for this cut, merged with `-s ours`. It holds the commits every step's `Source-Commit` names, so the service's TRUST-3 holds. `main`'s tree is unchanged by it |

## The rules

- **Everything on `signed` and `signed-compromise` is TEST-ONLY.** It is
  signed by keys whose private halves are public in the registry. No shipped
  Astra build trusts them.
- **Only `astra-registry`'s `tools/testkeys/rehearsal-push.mjs --step N`
  pushes there.** It pushes one fast-forward commit per step and never forces.
  It refuses this repository the other cuts' commits, and refuses the other
  canaries this cut's. The branches are never rewound. A ruleset refuses
  deletion and non-fast-forward on `main`, `signed` and `signed-compromise`,
  and a service that has accepted a step refuses a head that does not
  descend from it (SERVE-18).
- **The lists in this cut expire on 2026-10-31**, from 00:00Z for step 0 to
  11:00Z for step 5. After that the branches are a record, and nothing here is
  served again.
- **Every document is dated from 2026-10-24.** A list expires seven days after
  the signer run that made it, and that is production policy. So the only way
  to a hard end of 2026-10-31 was a T0 of 2026-10-24. Served before that, the
  documents are dated after the day they are served.
- The runbook is astra-plugins-ops `runbooks/roll-60-rehearsal.md`.

## Licence

GPL-3.0-or-later (see `LICENSE`), like the rest of the registry. Copyright
Minice.
