# MEDS ML4H 2025 Tutorial
This repository contains relevant files and instructions to reproduce those files for the MEDS ML4H 2025
tutorial.

In general, this repository will not be updated after ML4H 2025, and is not expected to be
directly useful for tutorial attendees other than as a source to download files when using the Colab notebooks
containing the interactive tutorial content.

> [!NOTE]
> GitHub shows a subset of the tutorial notebooks as having an error due to missing metadata state. This is due
> to a [bug in the GitHub notebook viewer](https://github.com/orgs/community/discussions/155944) and does not
> indicate an actual problem with the notebooks themselves. The notebooks can be accessed and run without issue
> via Google Colab using the links provided both in the notebooks and below:
>   - [What is MEDS?](https://colab.research.google.com/github/Medical-Event-Data-Standard/MEDS_ML4H_2025_Tutorial/blob/main/jupyter_notebooks/What_is_MEDS.ipynb)
>   - [MEDS Extract](https://colab.research.google.com/github/Medical-Event-Data-Standard/MEDS_ML4H_2025_Tutorial/blob/main/jupyter_notebooks/MEDS_Extract.ipynb)
>   - [Extract a Prediction Task](https://colab.research.google.com/github/Medical-Event-Data-Standard/MEDS_ML4H_2025_Tutorial/blob/main/jupyter_notebooks/Extract_a_Prediction_Task.ipynb)
>   - [Build a Model (Tabular Baseline)](https://colab.research.google.com/github/Medical-Event-Data-Standard/MEDS_ML4H_2025_Tutorial/blob/main/jupyter_notebooks/Build_a_Model_(Tabular_Baseline).ipynb)
>   - [Build a Model (NN)](https://colab.research.google.com/github/Medical-Event-Data-Standard/MEDS_ML4H_2025_Tutorial/blob/main/jupyter_notebooks/Build_a_Model_(NN).ipynb)

## Files

This repository contains the following files:
  - `README.md`: This file, which provides an overview of the repository and its contents.
  - `task_config.yaml`: The final ACES task configuration file used in the tutorial.
  - `MEDS_data.zip`: A zip file containing a pre-built version of a MEDS dataset and a label file, which is
    used in the tutorial.

## MEDS Dataset Generation

To generate the labeled MIMIC-demo data we use in this tutorial, you can run the following steps:

### Step 1: Download the MIMIC-IV Demo Dataset

You can perform this step directly, from physionet, like below:

```bash
wget -r -N -c -np -q -nH --cut-dirs=3 -P MEDS_data https://physionet.org/files/mimic-iv-demo-meds/0.0.1/
```

Or through the public ETL packages:

```bash
# Install the ETL package
pip install --quiet MIMIC_IV_MEDS==0.0.6
# Download and extract the MIMIC-IV demo dataset
MEDS_extract-MIMIC_IV root_output_dir=$INTERMEDIATE_DIR do_demo=True do_copy=True

# Move the final MEDS files over.
mv $INTERMEDIATE_DIR/MEDS_cohort/data MEDS_data/
mv $INTERMEDIATE_DIR/MEDS_cohort/metadata MEDS_data/

# Remove the other intermediate files as they are not needed for the tutorial
rm -rf $INTERMEDIATE_DIR

```

### Step 2: Create the Label File

```bash
# Install ACES
pip install --quiet es-aces==0.7.1
aces-cli \
    config_path=task_config.yaml \
    cohort_name="short_LOS" \
    cohort_dir="labels" \
    data=sharded \
    data.standard=meds \
    data.root=MEDS_data/data \
    data.shard=$(expand_shards train/1 tuning/1 held_out/1) \
    -m

```


### Step 3: Zip the labels

```bash
# Zip the MEDS data directory
zip -r labels.zip labels
```

## Running the Tutorial Notebooks
The tutorial notebooks are designed for use in Google Colab. There are links via the "Open in Colab" badges at
the top of each notebook to open them in that platform. To run them locally, you will need to use python 3.13
or lower and ensure that the `tree` package is installed in your local machine via, e.g., `sudo apt-get install
tree` (for linux).
