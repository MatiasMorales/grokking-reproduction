# Experiments

All runs: 2-layer transformer (width 128, 4 heads), AdamW lr 1e-3,
betas (0.9, 0.98), batch size 512, 20,000 steps, seed 42.
"99% at" = first step where accuracy reached 99% (checked every 100 steps).

| # | Operation | Train data | Weight decay | Train 99% at | Test 99% at | Final test acc | Expected | Chart |
|---|---|---|---|---|---|---|---|---|
| 1 | add | 50% | 1 | 600 | 1,100 | 100.0% | Groks fastest, strong push toward simple rule | [chart](charts/add_frac50_wd1.png) |
| 2 | add | 50% | 0.1 | 500 | 15,700 | 100.0% | Groks, but slower than weight decay 1 | [chart](charts/add_frac50_wd0.1.png) |
| 3 | add | 50% | 0 | 400 | never | 29.5% | Slower than 0.1 or no grokking, no push toward simple rule | [chart](charts/add_frac50_wd0.png) |
| 4 | add | 70% | 1 | 700 | 900 | 97.4% | Groks sooner than 50%, more data leaves fewer rules that fit | [chart](charts/add_frac70_wd1.png) |
| 5 | add | 30% | 1 | 400 | 4,600 | 100.0% | Longer wait than 50%, few examples are cheap to memorize | [chart](charts/add_frac30_wd1.png) |
| 6 | add | 40% | 1 | 500 | 2,600 | 100.0% | Wait between 30% and 50% | [chart](charts/add_frac40_wd1.png) |
| 7 | sub | 50% | 1 | 700 | 3,100 | 100.0% | Groks later than add 50%, no symmetry shortcut | [chart](charts/sub_frac50_wd1.png) |

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

### Training data, weight decay 1: 70% vs 50% vs 40% vs 30%
- Summary: test 99% at 900 steps (70%), 1,100 (50%), 2,600 (40%), 4,600 (30%).
- Less data makes the wait grow fast. Wait = test 99% step / train 99% step:
  1.3x (70%), 1.8x (50%), 5.2x (40%), 11.5x (30%).
- Near the low end, each 10% less data roughly doubles the time to
  understand (1,100 -> 2,600 -> 4,600). This matches the paper.
- Memorizing gets slightly faster with less data (train 99% at 700, 600,
  500, 400), because there are fewer examples to memorize.
- 70%: train and test lines almost move together, so there is barely any
  grokking wait. The final test accuracy (97.4%) is from a spike at the
  last step; test accuracy was 100% most of the time.
- 30%: the classic grokking shape. Test accuracy stays near 0 until about
  step 1,500, then jumps between steps 3,500 and 4,600.
- 40%: test accuracy climbs from about 5% at step 1,000 to 99% at step 2,600.
- Spikes: 30% has a big training spike near step 1,500 (to about 52%) and a
  test spike near step 17,000 (to about 33%). 40% has a training dip near
  step 900 (to about 85%), big test spikes near steps 4,800 (to about 16%)
  and 5,700 (to about 21%), and smaller ones near 11,000 and 16,000. All of
  them recover right away.

### Subtraction vs addition, 50% data, weight decay 1
- Summary: test 99% at 3,100 steps (sub) vs 1,100 (add), about 2.8x later.
  Train 99% at 700 (sub) vs 600 (add).
- This is between addition with 40% data (2,600) and 30% data (4,600).
- Why: addition is symmetric. Seeing 3 + 5 = 8 also tells the model
  5 + 3 = 8. Subtraction is not: 3 - 5 = 95 but 5 - 3 = 2.
- Rough estimate: with addition at 50%, a pair {a, b} is covered if either
  order is in the training data, so about 1 - 0.5 x 0.5 = 75% of the table
  is covered. To cover only 50% like subtraction does, addition would need
  about 29% data (1 - (1 - f)^2 = 0.5). So subtraction at 50% should act
  like addition at about 30%. The real result (3,100) is a bit faster than
  addition at 30% (4,600), but in the same area. One seed only.
- Shape: test accuracy stays near 0 until about step 2,200, then jumps very
  sharply, from about 10% at step 2,500 to 100% at step 3,100. Sharper than
  addition at 30%, which took from about 3,500 to 4,600.
- Training accuracy dips to about 78% near step 2,500, right before the jump.
- Fewer spikes after grokking than the addition runs with weight decay 1.
