# EEG denoising methods

## Introduction
In this repository I compare several approaches to cleaning EEG recordings:
- 1D-ResCNN [1]
- IC-U-Net [2]
- EEGDnet [3].

Since I couldn't find githubs for 1D-ResCNN and EEGDnet, they were implemented from scratch. I also discovered inconsistencies between the architecture description and the actual implementation of IC-U-Net, so I made it the same as described in the article.

The models were trained and evaluated on EEGdenoiseNet [4], following the authors' approach. 

## Results
The final figures for the test subsets are as follows (CC stands for Pearson correlation coefficient between clean and denoised signal):
- Ocular artefact subset:

| model |  MSE | SNR, Db | CC |
|-------|------|---------|----|
| 1D-ResCNN | 40.643 | 11.40 | 0.965 |
| IC-U-Net | 5.791 | 20.523 | 0.995 |
| EEGDnet transformer | 22.236 | 14.467 | 0.979 |

- Muscular artifact subset:

| model |  MSE | SNR, Db | CC |
|-------|------|---------|----|
| 1D-ResCNN | 78.022 | 8.369 | 0.929 |
| IC-U-Net | 17.296 | 14.947 | 0.986 |
| EEGDnet transformer | 57.739 | 9.861 | 0.943 |

WandB links:
- ocular artifact subset: https://wandb.ai/dmitriykornilov_team/EEGdenoiseNet%2C%20EOG?workspace=user-dmitriykornilov
- muscular artifact subset: https://wandb.ai/dmitriykornilov_team/EEGdenoiseNet%2C%20EMG?workspace=user-dmitriykornilov
  
More details can be found in "EEG denoising.pdf" in the presentation directory.

## Usage
To reproduce the results, run EEG_denoising.ipynb. It includes preliminary data analysis, model implementation, training and evaluation. 
1. Change `PROJECT_DIR = '/kaggle/working/'` to the desired directory.
2. To evaluate *ocular artifact removal*, uncomment the following lines in the **Generation** section:
```
data_length = min(len(clean_all), len(noisy_eog))

clean_all_truncated = clean_all[:data_length]
noisy_eog_truncated = noisy_eog[:data_length]

clean_eog, contaminated_eog = generate_contaminated_data(clean_all_truncated, noisy_eog_truncated)
print(clean_eog.shape, contaminated_eog.shape)
```
And also line in the **Dataloader** section:
```
clean_train, clean_val_test, noisy_train, noisy_val_test = train_test_split(clean_eog, contaminated_eog, test_size=0.2, random_state=SEED, shuffle=True)
```

To evaluate *muscular artifact removal*, uncomment neighbouring  lines:
```
data_length = min(len(clean_all), len(noisy_emg))

clean_all_truncated = clean_all[:data_length]
noisy_emg_truncated = noisy_emg[:data_length]

clean_emg, contaminated_emg = generate_contaminated_data(clean_all_truncated, noisy_emg_truncated)
print(clean_emg.shape, contaminated_emg.shape)
```
And line in the **Dataloader** section:
```
clean_train, clean_val_test, noisy_train, noisy_val_test = train_test_split(clean_emg, contaminated_emg, test_size=0.2, random_state=SEED, shuffle=True)
```

## Literature:

[1] Sun, W., Su, Y., Wu, X., Wu, X., 2020. A novel end-to-end 1D-ResCNN model to remove artifact from EEG signals. Neurocomputing 404, 108–121.

[2] Chun-Hsiang Chuang, Kong-Yi Chang, Chih-Sheng Huang, Tzyy-Ping Jung, IC-U-Net: A U-Net-based Denoising Autoencoder Using Mixtures of Independent Components for Automatic EEG Artifact Removal, NeuroImage, Volume 263, 2022, 119586, ISSN 1053-8119. Github: https://github.com/roseDwayane/AIEEG

[3] Yi, Peng & Chen, Kecheng & Ma, Zhaoqi & Zhao, Di & Pu, Xiaorong & Ren, Yazhou. (2021). EEGDnet: Fusing Non-Local and Local Self-Similarity for 1-D EEG Signal Denoising with 2-D Transformer.

[4] Zhang, H.; Zhao, M.; Wei, C.; Mantini, D.; Li, Z.; and Liu, Q. 2020. EEGdenoiseNet: A benchmark dataset for deep learning solutions of EEG denoising. arXiv preprint arXiv:2009.11662.



