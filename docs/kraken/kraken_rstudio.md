### How to Run Rstudio server on kraken

First, submit an interactive job to get a node:

```bash
srun -t 1600 --mem=60G -c 4 --pty bash
```