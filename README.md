# plankton participant

A template for **joining a kton federation**: you run a small, reproducible data pipeline, sign every
computation and every reproduction, publish the signed records, and register with an aggregator. You never
message anyone — you cooperate by publishing verifiable records that others reproduce.

Everything you need is in this repo, including the two CLIs (`bin/plankton`, `bin/nekton`).

## 4 steps

### 1. Use this template
Click **Use this template → Create a new repository**. You now own a participant repo.

### 2. Run the cooperation loop
Point a Claude session (or several) at **`CLAUDE.md`** — it is the complete brief: set the env, make a
signing identity, then extend / reproduce / reuse steps of the pipeline over `data/penguins.csv`, recording a
signed **foton** per computation and a signed **reproduces** claim per reproduction.

```bash
export PLANKTON_DIR="$PWD/registry/plankton"
export NEKTON_DIR="$PWD/registry/nekton"
export NEKTON_TEMPLATES="$PWD/templates"
export PATH="$PWD/bin:$PATH"
```

The determinism checklist and the reproduce recipe in `CLAUDE.md` are what make independent results match
byte-for-byte (L0). Your signed records accumulate under `registry/`.

### 3. Publish
```bash
bash scripts/publish.sh      # builds mirror/union.json + keys.json + manifest.json (+ a static mirror)
git add registry mirror && git commit -m "my run" && git push
```
On push, the `publish` workflow rebuilds `mirror/` and commits it, so your published records are live at
`https://raw.githubusercontent.com/<you>/<repo>/main/mirror/`. That base URL is your **source**.

### 4. Register with a federation
Open a **Register a participant** issue on the federation you want to join (e.g. the aggregator that was set
up from `plankton-federation-template`), giving your `mirror/` source URL, your `keys.json` URL, and a mirror
interval. Once merged, the aggregator mirrors and **verifies** your records on that interval, and your work
appears — with its `↻N` reproduction count — in the federated view alongside everyone else's.

## What you'll see
Even before you join, your own records form a provenance graph. After the federation mirrors several
participants, each output shows **↻N** — the number of independent signers who produced those exact bytes.
That number growing across independent repos, with every signature re-verified, *is* the federation working.

## Layout
```
CLAUDE.md            the cooperation brief (authoritative)
bin/                 plankton, nekton  (committed so you need no build)
data/                penguins.csv + DATA.md (the Python env: numpy/scikit-learn/scipy; no pandas)
templates/           nekton claim templates (reproduces, working-on)
registry/            your signed stores: plankton/ (fotons), nekton/ (claims), keys/ (your .pub)
session-1/           a workspace (add session-2/ … if you run several)
scripts/publish.sh   build the published mirror + manifest
mirror/              generated: what you publish (fetchable via raw URLs)
```

Trust model: nothing here is trusted blindly. Every record is content-addressed and signed; a federation
**verifies** each signature on ingest. Publishing exposes records; it never grants authority.
