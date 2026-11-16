# When Is Conformal Calibration Reusable After Compression?

Code and raw outputs for the paper *"When Is Conformal Calibration Reusable After Compression? An Empirical Study with a Label-Free Baseline"* (draft, November 2026).

The study asks when a split-conformal threshold calibrated on an fp32 model can be reused after real INT8 quantization, 50% pruning, or fine-tuning. It uses DermaMNIST and BloodMNIST, a small ResNet and a plain CNN, and the LAC, APS and RAPS-style scores at 90% target coverage. It includes a label-free baseline, `sizematch`, which has no finite-sample guarantee.

This is an empirical study of small models on two datasets. It is not a general statement about compression, and nothing here is a clinical tool.

## Layout

```
notebooks/     one Colab/Jupyter notebook per experiment step
results/       raw console output pasted from each run (the source of every number in the paper)
exploratory/   early pilots, kept for transparency; not used for any reported number
requirements.txt
LICENSE        MIT (code only)
```

## Which notebook produces which result

| Paper item | Notebook | Raw output |
|---|---|---|
| Table 1, Section 4 averages, worst-class values | `notebooks/step6_second_arch.ipynb` | `results/step6_second_arch_output.txt` |
| INT8 checks | `notebooks/step5_real_int8.ipynb` | `results/step5_output.txt` |
| Tables 2 and 3, intensity sweep | `notebooks/step7_mechanism.ipynb` | `results/step7_mechanism_output.txt` |
| Corrected BatchNorm re-estimation (shuffled batches) | `notebooks/step7b_adabn_fix.ipynb` | `results/step7b_adabn_fix_output.txt` |
| Earlier development steps (not in the paper's tables) | `step1` to `step4` notebooks | `results/step1_*`, `results/step2_*` |

TODO before publishing: confirm each row against the paper, and add the outputs for steps 3 and 4 if any number in the paper depends on them.

## Known issue, fixed in step 7b

The re-estimation arm in `step7_mechanism.ipynb` (`adabn`) passed class-sorted data through the model without shuffling, which lowers accuracy and distorts the variances. Its results were discarded. `step7b_adabn_fix.ipynb` reruns it with shuffled batches and keeps a class-sorted diagnostic arm (`seen:adabn_sorted`) that reproduces the damage. Do not use the `adabn` rows of the step 7 output.

## Running

1. Open a notebook in Colab with a GPU runtime (the INT8 notebook also needs CPU for FX quantization).
2. Install the packages in `requirements.txt` (versions to be filled from the Colab runs).
3. Run all cells. Datasets download automatically through the `medmnist` package.

Seeds: 5 training seeds in the main experiments, 10 in the mechanism study, and 40 random calibration/evaluation splits per seed. Exact seed lists are in each notebook.

## Data and licenses

- Code: MIT, see `LICENSE`.
- Data are not included. MedMNIST is CC BY 4.0 except DermaMNIST, which is CC BY-NC 4.0 (non-commercial use only). See the [MedMNIST v2 record on Zenodo](https://zenodo.org/records/6496656).
- DermaMNIST has repeated lesions across partitions (Abhishek, Jain and Hamarneh, arXiv 2401.14497), so leakage cannot be excluded.
- Please cite MedMNIST v2 (Yang et al., Scientific Data, 2023) and v1 (Yang, Shi and Ni, ISBI 2021), and the source datasets, if you use the data.

## Limitations

See Section 5 of the paper: no guarantee for `sizematch`, simulated pruning without retraining, two small convolutional models, small seed counts, and unexplained effects after fine-tuning.

## Citation

TODO: add the arXiv identifier after the preprint is posted.
