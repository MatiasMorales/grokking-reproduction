# Experiments

All runs: 2-layer transformer (width 128, 4 heads), AdamW lr 1e-3,
betas (0.9, 0.98), batch size 512, 20,000 steps, seed 42.
"99% at" = first step where accuracy reached 99% (checked every 100 steps).

| # | Operation | Train data | Weight decay | Train 99% at | Test 99% at | Final test acc | Expected | Chart |
|---|---|---|---|---|---|---|---|---|
| 1 | add | 50% | 1 | 600 | 1,100 | 100.0% | Groks fastest, strong push toward simple rule | [chart](charts/add_frac50_wd1.png) |
| 2 | add | 50% | 0.1 | 500 | 15,700 | 100.0% | Groks, but slower than weight decay 1 | [chart](charts/add_frac50_wd0.1.png) |
| 3 | add | 50% | 0 | 400 | never | 29.5% | Slower than 0.1 or no grokking, no push toward simple rule | [chart](charts/add_frac50_wd0.png) |

## Notes

### Weight decay 1 vs 0.1 vs 0
- Summary: test 99% at 1,100 steps (wd 1), 15,700 steps (wd 0.1), never (wd 0).
- Weight decay 1 reached 99% test accuracy about 14x faster than 0.1.
- With weight decay 1 the gap between memorizing and understanding is small
  (600 vs 1,100 steps). 50% data is probably well above the minimum needed
  for addition, so there is no long flat wait.
- With weight decay 0.1 the test accuracy climbs slowly (about 10% at step
  1,000, 45% at step 10,000), then jumps to 100% between steps 12,000 and 16,000.
- Weight decay 0 never reached 99% test accuracy within 20,000 steps.
  Test accuracy rose to about 30% by step 8,000, then stayed flat.
- 30% is far above random guessing (about 1%), so the model learned
  something beyond memorizing, but got stuck.
- "Never" only means not within 20,000 steps. The paper's Figure 1 used no
  weight decay and only generalized after about 1,000,000 steps, so a much
  longer run might still grok.
- All runs show short spikes where accuracy drops and recovers right away.
  With weight decay 1 they are bigger: test accuracy drops to about 50% near
  step 7,500 and about 20% near step 10,500.
