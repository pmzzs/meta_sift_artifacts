# Meta-Sift: A PyTorch Implementation
This is the artifacts evaluation of the USENIX'23 paper [Meta-Sift: How to Sift Out a Clean Subset in the Presence of Data Poisoning?](https://arxiv.org/abs/2210.06516)
This GitHub repo focuses on road mapping and reproduces the results to support the three main claims we developed in the "Meta-Sift" paper:
1. Defense performance is sensitive to the purity of the
base set (**Takeaway \#1 in Section 1**).
2. Both existing automated methods and human inspection fail to identify a clean subset with high enough precision (**Takeaway \#2 in Section 1**).
3. Our proposed solution, Meta-Sift, can obtain a stable subset in many poison situations (**Takeaway \#3 in Section 1**).

# A quick start for reproducing one experiment of each claim
The file `quick_start.ipynb` file contains a simple implementation to verify our three points of view, and the following part is guidance on how to run it:

1. Download dataset `gtsrb_dataset.h5` form this [link](https://drive.google.com/file/d/1SKYMwrnjEyFjjc7UWTdAyAjFI_demNtD/view?usp=sharing) and put it on './dataset' folder.

2. Use `pip install -r requirements.txt` to install the required packages.

3. Run the `quick_start.ipynb`; more detailed comments are in the file!

# Human inspection experiment

Our paper makes an important claim: humans only have limited capability in identifying the poisoned samples in a given dataset, especially on clean-label backdoor attacks, as we found in our human study experiments. In this artifact, we made our labeling tools (html file) and poisoned dataset (the Narcissus clean-label backdoor poisoning 10% of samples) open-source and available for evaluation. To reproduce the human results, refer to the `./human_exp` folder, and further instructions can be found in `./human_exp/README.md`.


# More evaluation results for Meta-Sift on the GTSRB dataset

Below we list more experiments to demonstrate Meta-Sift's effectiveness against different poisoning methods. The provided code implements three representative or state-of-the-art poisoning attacks from each category: `Targeted Label Flipping` for `Label-only attacks,` `Narcissus Clean-label Backdoor` for `Feature-only attacks,` and `Badnets One-Tar` for `Label-Feature attacks.` The `main.py` first loads and passes the poisoned dataset to our Meta-Sift implementation, which then evaluates the proposed method and outputs the Normalized Corruption Rate (NCR) while saving the selected index (the clean base set). Here is the start command: 

For `Targeted Label Flipping` from class 2 to class 38 with 16.67% in-class poison ratio:  
```console
python main.py --corruption_type targeted_label_filpping --corruption_ratio 0.1667
```


For `Narcissus Clean-label Backdoor` in class 38 with 10% in-class poison ratio:  
```console
python main.py --corruption_type narcissus --corruption_ratio 0.1
```


For `Badnets One-Tar` in class 38 with 33% in-class poison ratio:  
```console
python main.py --corruption_type badnets --corruption_ratio 0.33
```

# Make Meta-Sift plug-in?
```python
from meta_sift import *
class Args:
    num_classes = 43
    tar_lab = 38
    repeat_rounds = 5
    res_epochs = 1
    warmup_epochs = 1
    batch_size = 128
    num_workers = 16
    v_lr = 0.0005
    meta_lr = 0.1
    top_k = 15
    go_lr = 1e-1
    num_act = 4
    momentum = 0.9
    nesterov = True
    random_seed = 0
args=Args()
clean_idx = meta_sift(args, dataset, total_pick=1000)
```
This is a standard Meta-Sift code block that can be plug in with any PyTorch standard dataset.
Change the parameter in `args` and change the `dataset` as your poisoned dataset and run it, the return are at least `total_pick` clean sample indices from the `dataset`. You can use `torch.utils.data.Subset(dataset, clean_idx)` to get the base set dataset after siting.



