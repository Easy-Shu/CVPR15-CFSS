# CVPR15-CFSS
------
Shizhan Zhu

Released on July 25, 2015

**If you want to use our code to train on another dataset (e.g. the [WFLW dataset](https://arxiv.org/abs/1805.10483)) please have a look at this:** We post our full codes for training on the [WFLW dataset](https://arxiv.org/abs/1805.10483) for comparison with future works. Please follow [this](https://github.com/zhusz/CVPR15-CFSS/tree/wflw) for the codes (a new branch). Particularly, please refer to [here](https://github.com/zhusz/CVPR15-CFSS/compare/d54524ae42f77602864809d02ee8f76b42306fcd...8278f5a6d3ce75cbc9961424978f6a97ecb6d93e) for a detailed instruction on how to modify the hyperparameters inside addAll.m (mainly readjusting the hyperparameters according to new landmark protocols and new dataset size).

**By research demands, we further release a CFSS model that is specifically trained for VJ (Matlab) face detection box. Download the model [here](https://www.dropbox.com/s/jvoylj8tpgo6yj4/CFSS_Model_VJ.tar.gz).**

**A comprehensive evaluation of our approach (CFSS) by IBUG [here](https://arxiv.org/pdf/1603.06015.pdf)**.

**The released off-the-shelf model is trained only dedicated to the distribution of labeled boxes of IBUG provided by the [offical site](http://ibug.doc.ic.ac.uk/resources/300-W/) (as stored in our data folder). If you want to test this method with other distribution of bounding box even with the same 68 landmarks, you need to retrain the model, in which the training set should be given the same bounding box distribution as your test set (rather than the existing one). Details of the bounding box issues in retraining can refer to the second point of [Notes](https://github.com/zhusz/CVPR15-CFSS#notes).**

**I have updated the error evaluation codes to better facilitate comparison for future works. After running the inference script, the variable `er` would exist in the workspace, which is the error vector used for calculating the mean error and CED curve. Specifically, we use the mean of 6-point around eyes to calculate the inter-pupil distance.**

## Description

This is the implementation of Shizhan Zhu et al.'s CVPR-15 work [Face Alignment by Coarse-to-Fine Shape Searching](http://www.cv-foundation.org/openaccess/content_cvpr_2015/papers/Zhu_Face_Alignment_by_2015_CVPR_paper.pdf). It is open source under BSD-3 license (see the `LICENSE` file). Codes can be used freely only for academic purpose. If you want to apply it to industrial products, please send an email to Shizhan Zhu at `zhshzhutah2@gmail.com` first.

## Citation
If you use the codes as part of your research project, please cite our work as follows:
```
@inproceedings{zhu2015face,
  title={Face Alignment by Coarse-to-Fine Shape Searching},
  author={Zhu, Shizhan and Li, Cheng and Loy, Chen Change and Tang, Xiaoou},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  pages={4998--5006},
  year={2015}
}
```

## Bug Fixing Log
1. Function [getTransViaMerge](https://github.com/zhusz/CVPR15-CFSS/blob/master/codes_release/trans/getTransViaMerge.m) has been updated to fix the prompting error `length(T) must be the same as size(oldPose,1)!`. For more details about the reason, please refer to the content of [Issue #01](https://github.com/zhusz/CVPR15-CFSS/issues/1). Here we would like to express our sincere thanks to [maxpy](https://github.com/maxpy) for providing precious discovery and suggestion to the problem.

## Dependency
Due to license issues, this implementation uses two publicly avaiable software packages as opposed to our original implementation: 
1. [VLFeat](https://github.com/vlfeat/vlfeat)
2. [LibSVM](https://github.com/cjlin1/libsvm).
Our implementation has been validated on vlfeat 0.9.20 and libSVM 3.20, with Matlab version R2013a (Unix).

## Installation
1. Put all the required dependency packages under the `external` folder. Matlab compatible bainary files must exist. Please note we have put `vl_sift_toosimple.c` in the `codes_released/feat/SIFT` folder. Please put it into the directory of `external/vlfeat-0.x.xx/toolbox/sift` before compiling (and hence its relative Matlab compatible binary file would appear after compiling). This would slightly speed up sift extraction.
2. Put all the 3148 (for training) + 689 (for testing) images in the `imageSource` folder. All images could be downloaded at [IBUG page](http://ibug.doc.ic.ac.uk/resources/facial-point-annotations/). Only LFPW, Helen, AFW and IBUG set are required. Please change one of the filename in IBUG set from `image_092⋅_01.jpg` to `image_092_01.jpg` to avoid space in file name.

## Training and Testing
To train the model using the default setting, simply use the following command:
```matlab
>> getParametricModels; addAll; learnCFSS;
```
Model files would be expected to appear in the `model` folder.

To do testing, simply use the following command:
```matlab
>> inferenceCFSS;
```

After testing, the variable `estimatedPose` would exist in the workspace, which is our final estimated facial shape.

## Off-the-shelf Pre-trained Model

A pre-trained model can be downloaded from [here](http://mmlab.ie.cuhk.edu.hk/projects/CFSS/model.tar.gz). Extract the three model files and put them into the `model` folder and run the inference script. Be reminded that all functions path should be included. As aforementioned, this model is trained only dedicated to the distribution of labeled boxes of IBUG.

By research demands, we further release a CFSS model that is specifically trained for VJ (Matlab) face detection box. Download the model [here](https://www.dropbox.com/s/jvoylj8tpgo6yj4/CFSS_Model_VJ.tar.gz).

## Baseline Experiment
With following two simple configuraton changes (you can do it in `addAll.m`), our codes can degenerate to SDM. Users can observe results difference especially in the cases where large head pose or extreme expression (e.g. large mouth) exihibits.

```matlab
config.stageTot = 1;
config.regs.iterTot = 4;
```

## Formats
Shape is represented as a 2n dimensional vector each in the format of `[x1,x2,...,xn,y1,y2,...,yn]`. Format of bounding box is `[left, right, top, bottom]`.

## Demo Video
We have uploaded our [demo video](http://youtu.be/S4PQ63duO-I) in youtube. The trained model should perform with similar accuracy as shown in the demo video. Otherwise, the software might have been used in an inappropriate way.

## How to speed up
We need to point out again, in this implementation, we can only use the publicly available packages [VLFeat](https://github.com/vlfeat/vlfeat) and [LibSVM](https://github.com/cjlin1/libsvm) due to open source license issues. If you want a faster performance, you need to pay attention to some details like integral map sharing tricks. Classifiers should also be replaced by much faster ones like linear kernel.

## Notes
1. Training typically requests at least 20 GB of memory (based on the default parameters settings). Training codes are implemented with parallel machenism and a multicore CPU + parpool enabled Matlab environment can significantly reduce the training time.
2. If you use the codes to train on another dataset, the bounding box given in the testing phase should be equally noisy with that in the training phase. According to our experience, the definition of **equally noisy** indicates: 1) no bias; and 2) equal variance. Bounding box variance larger than 1/3 of face size is regarded as very noisy. Under such circumstances, please consider to increase the searching stages into 4 stages if possible. Parameters should be tuned to align the dataset size and landmarks protocol via cross-validation.

## Feedback
Suggestions and opinions of this work (both positive and negative) are greatly welcome. Please contact the author by sending email to `zhshzhutah2@gmail.com`.

## Common Errors
1. Why does the function `svmtrain` or `svmpredict` prompt errors?

   Please note Matlab itself contains such two functions, and their interface is slightly different from those in libSVM. Please make sure you have included the path to libSVM.

## License
BSD-3, see `LICENSE` file for details.
