# Qwen3.8-Flash-Next Coder on Kaggle (2x T4) with Strata

Runs the **Coder IQ1_M** pack of Qwen3.8-Flash-Next on Kaggle's free 2x T4 using [Strata](https://github.com/Niko1221/Strata), then tests it and draws SVGs with a live preview, model name and tok/s.

The Coder is the coding version of the 125B model with half of the experts removed, stored at about 1-bit (IQ1_M). It is not the full 125B model.

## Files

- `qwen38_coder_strata_kaggle.ipynb` - the notebook with all cells

## Kaggle setup

1. Upload the notebook: **File > Import Notebook**
2. Accelerator: **GPU T4 x2**
3. Internet: **On**

## Cells in order

| # | Cell | What it does |
|---|------|--------------|
| 1 | Check the machine | Shows GPUs, driver, RAM |
| 2 | Install Strata | First time per session |
| 3 | Update + dry run | Prints the plan, changes nothing |
| 4 | Launch | Starts Strata in the background on port 8080 |
| 5 | Wait | Watches the log and disk use until READY |
| 6 | Test | Streams a short answer, prints first-token time and tok/s |
| 7 | SVG | Live preview with model name, state, tokens and tok/s; saves `/kaggle/working/drawing.svg` |
| 8 | Stop | Stops the server |

## Check the plan before launching

After cell 3, the plan must say:

```
Qwen3.8-Flash-Next Coder IQ1_M, 58 GB
```

If it says **Swift** or **IQ2_XS** (about 68 GB), do not launch. That pack is too big for Kaggle's disk.

The launch command needs both flags: `--model iq1_m --family coder`. With `--family coder` alone, Strata picks a default model for your machine, which was Swift IQ2_XS.

## Disk limit (important)

Kaggle shows about 1 TB free in `df`, but that is the host's disk, not your limit. Your real limit is roughly 95 GB (estimated from where an earlier run failed with `No space left on device`).

- Cell 5 prints how much Strata has written. If it passes about 85,000 MB, interrupt the cell and run `!pkill -f "strata launch"`.
- If the disk goes read-only after filling up, deleting files will fail. Restart the Kaggle session for a fresh disk.
- Everything is wiped when the session ends, so each new session repeats the download (about 58 GB).

## Memory

Kaggle gives about 31 GB of RAM. Strata's docs say the Coder wants 32 GB, so it may be slow, especially on the first answer. Run the test cell twice and use the second run's speed.

## Notes

- Do not run the launch cell twice. A second copy fights over port 8080.
- The Coder is weaker outside code and in languages other than English, since its experts were chosen on code data.
- The SVG cell counts thinking tokens in the speed. It shows the state as "thinking" until the answer starts.
- To see the log at any time: `!tail -n 30 strata.log`

## What did not work

- **DeepSeek V4.1 Flash**: Strata's DeepSeek engine supports RTX 30/40/50 series, Radeon AI PRO R9700 and Ryzen AI Max only, not the T4. The model is also about 257 GB.
- **Qwen3.8-Flash-Next IQ2_XS and the Swift pack** (68 GB): the download plus the packed copy of the experts went over the disk limit.

## Credits

- Strata: https://github.com/Niko1221/Strata
- Qwen3.8-Flash-Next: Qwen
- Coder GGUF: ISTA-DASLab
