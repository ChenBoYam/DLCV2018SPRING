# Face Recognition and Model Compression
## Task description
* Train a classifier to recognize human faces.
* Squeeze the model for practical uses.

For more information, please refer to [PPT](https://github.com/thtang/DLCV2018SPRING/blob/master/final/Challenge%20%232_%20%20Face%20Recongnition.pdf) provided by TA.
## Quick start
- Test accuracy: 86.395%, rank 2 on [leaderboard](https://www.kaggle.com/c/2018-spring-dlcv-final-project-2/leaderboard); validation accuracy: 86.166%
```
bash run.sh [test_image_folder] [output_filename csv]

# for example 
bash run.sh ./dlcv_final_2_dataset/test/ output.csv
```
- Environment requirements
```
pip install -r requirements.txt
```
### Dependency
`Python3` `tensorflow` `pytorch` `keras` `imgaug` `numpy` `sklearn` `matplotlib` `skimage` `pandas` `torchvision` `tensorboardX`

--------

## Baseline
- We always train a baseline model first __without any constraints__.
```
python3 train.py -tp [train_path] -tf [train_file_txt] -vp [valid_path] -vf [valid_file_txt] -s [save_dir]
```
- Add these three arguments, ```-ls 0.0 -lm 0.0 -pt all-one```, to the above command.

## Train
- Train an efficient VGG-16 with sparsity penalty and monotonicity-induced penalty
```
python3 train.py -tp [train_path] -tf [train_file_txt] -vp [valid_path] -vf [valid_file_txt] -s [save_dir]
```
- A dictionary, named para_dict.npy, will be stored into the specified directory (save_dir).
- We suggest to leverage the baseline model as the pre-trained model by adding argument
```
-i baseline_model_npy
```
- Example of prioritized channels

![](plot/conv1_1_gamma.png "1")| ![](plot/conv5_3_gamma.png "2")
:-----------------------------:|:------------------------------:



## Prune
- Prune a model based on the magnitude of scaling factors by a threshold (default=0.05)
```
python3 sparsify.py -i [input_npy] -o [output_npy] -th [threshold]
```

![sub-network](plot/pruning.png)

- Get a sub-model, for example: given a proportion = 1/2 = 50%
```
python3 stratify.py -i [input_npy] -o [output_npy] -dp [value (0,1] ]
```

- Visualization of pruned network

![pruned_net](plot/CP_pruned_net.png)

## Finetune
- Finetune the pruned model
```
python3 finetune.py -i [sparse_npy] -tp [train_path] -tf [train_file_txt] -vp [valid_path] -vf [valid_file_txt] -s [save_dir]
```
- A finalized dictionary, also named para_dict.npy, will be stored into the specified directory (save_dir). 

## Test
Test the finalized model
```
python3 test.py -i [finalized_npy] -o [output_csv_file] -tp [test_path]
```

Inference using extract features and KNN
```
python3 test_1NN.py -i [finalized_npy] -o [output_csv_file] -tp [test_path] -train [data path, e.g., ./dlcv_final_2_dataset]
```
![](plot/perf_summary.png)

Code for other benchmark model and development notebook are shown in [dev_folder](https://github.com/thtang/DLCV2018SPRING/tree/master/final/dev_notebook).<br>
For more information, please refer to our [poster](https://github.com/thtang/DLCV2018SPRING/blob/master/final/poster.pdf).

## Trained Model
* [baseline](https://www.dropbox.com/s/c1u540426hxb0bc/save_baseline.zip?dl=0)
* [sparse w/o finetuning](https://www.dropbox.com/s/3v8f2cuv3wf9f2u/save_full.zip?dl=0)
* [finalized](https://www.dropbox.com/s/9dkpvttpymbxlr4/save_finetune.zip?dl=0)

## Authors
[Chun-Min Chang](https://github.com/twcmchang/DLCV2018SPRING/tree/master/final), Tsun-Hsien Tang, Chia-Ching Lin, Kai-Chun Su

## Reference
[1] [hungnguyen94/SqueezeNet-Keras](https://github.com/hungnguyen94/SqueezeNet-Keras)<br>
[2] [marvis/pytorch-mobilenet](https://github.com/marvis/pytorch-mobilenet/blob/master/benchmark.py)
