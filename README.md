# Install Environment
## 1.create a new conda environment
```bash
conda create -n ${env_name} python=3.7 
conda activate ${env_name}
```

# Install dependencies

```bash
bash install.sh
```
```bash
pip install lmdb
pip install tmtools
pip install Levenshtein
```

# Data
## Download the preproceesd CATH datasets
CATH 4.2 dataset provided by [Generative Models for Graph-Based Protein Design](https://papers.nips.cc/paper/2019/hash/f3a4ff4839c56a5f460c88cce3666a2b-Abstract.html)

CATH 4.3 dataset provided by [Learning inverse folding from millions of predicted structures](https://www.biorxiv.org/content/10.1101/2022.04.10.487779v1)

```bash
bash scripts/download_cath.sh
```

# Training
Training NAR ProteinMPNN with conditional masked language modeling (CMLM)


```bash
export CUDA_VISIBLE_DEVICES=0
# or use multi-gpu training when you want:
# export CUDA_VISIBLE_DEVICES=0,1

exp=fixedbb/protein_mpnn_cmlm  
dataset=cath_4.2
name=fixedbb/${dataset}/protein_mpnn_cmlm

python ./train.py \
    experiment=${exp} datamodule=${dataset} name=${name} \
    logger=tensorboard trainer=ddp_fp16
```


If using PiFold model or other datasets to replace exp and dataset, it is sufficient

Some flags for training:

| Argument             | Usage                                                                                     |
|----------------------|--------------------------------------------------------------------------------------------|
| `experiment`         | experiment config. see `project/configs/experiment/` folder                                 |
| `datamodule`         | dataset config. see `project/configs/datamodule/` folder                                    |
| `name`               | experiment name, deciding the directory path your experiment saving to, e.g., `/root/research/project/run/logs/${name}` |
| `logger`             | config of which ml experiment logger to use, e.g., tensorboard                             |
| `train.force_restart`| set to `true` to force retrain the experiment under `${name}`, otherwise will resume training from the last checkpoint |

# Example
```python
pdb_path = "/root/research/projects/data/pdb_samples/5izu_proc.pdb"
designer.set_structure(pdb_path)

start_ids = [1, 50]
end_ids = [10, 100]

for i in range(5):
    out, ori_seg, designed_seg = designer.inpaint(
        start_ids=start_ids, end_ids=end_ids, 
        generator_args={'temperature': 1.0}
    )
    print(designed_seg)

print('Original Segments:')
print(ori_seg)
```

