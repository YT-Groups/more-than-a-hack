# Team YT: More Than a Hack 2027, Phase 1

Who has the ball, frame by frame, in Barcelona vs Athletic Club: `H` (home in possession), `A`
(away in possession), `D` (dead ball) or `X` (ungraded), for all 199,319 frames of the tactical
video. The labels are produced by a rule-based pipeline from the provided automatic tracking and
Smart Tagging. No model is trained and no labels are used.

| File | What it is |
|---|---|
| `YT.csv` | the submission |
| `docs/YT.md`, `docs/YT.pdf` | the technical document: approach, possession rules, evaluation, limitations |
| `reports/ablation.csv` | every version, its settings and its label-free checks |
| `reports/gaps.md` | where and when the tracking loses the ball |

## Regenerate the submission

Python 3.13.

```bash
pip install -r requirements.txt
```

Unpack the competition data into `data/tactical/` (see `data/README.md`), then:

```bash
python run_pipeline.py --out YT.csv
```

This builds the file, validates it with the organisers' checker, and prints the public-window
scores and the full-match checks. It takes about 20 s, and the first run caches the parsed tracking
in `data/cache/`.

To confirm a fresh clone rebuilds exactly the submitted file (same SHA-256):

```bash
MTAH_DATA_DIR=/path/to/data scripts/clean_clone_check.sh YT.csv
```

## Tests and scoring

```bash
python -m unittest discover tests
```

```bash
python evaluation/run_evaluation.py --results <folder containing YT.csv>
```

## How it works, in brief

- **Dead ball:** a sustained drop in player running speed marks a stoppage. Its edges are moved
  to the ball leaving the pitch, the ball being lost, or the restart kick. Laws of the Game rules
  handle kick-offs and goals.
- **Control:** a team has the ball when one of its players is close to it, unchallenged, and the
  ball moves with that player for 0.4 s.
- **State machine:** possession changes only on confirmed control by the other team, and is never
  carried across a dead ball.

Details, the variants compared and every threshold with its source are in `docs/YT.md`.

## Credits and licence

Our code is released under the MIT Licence (see `LICENSE`). `games.py`, `evaluation/` and
`starter/load_smart_tagging.py` come from the organisers' repository,
[metrica-sports/more-than-a-hack-2027](https://github.com/metrica-sports/more-than-a-hack-2027),
under its MIT Licence. The match data is not included and is not covered by either licence.
