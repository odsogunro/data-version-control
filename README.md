# Data Version Control Tutorial

Example repository for the [Data Version Control With Python and DVC](https://realpython.com/python-data-version-control/) article on [Real Python](https://realpython.com/).

To use this repo as part of the tutorial, you first need to get your own copy. Click the _Fork_ button in the top-right corner of the screen, and select your private account in the window that pops up. GitHub will create a forked copy of the repository under your account.

Clone the forked repository to your computer with the `git clone` command

```console
git clone git@github.com:YourUsername/data-version-control.git
```

Make sure to replace `YourUsername` in the above command with your actual GitHub username.

Happy coding!

```
```
# summary notes on dvc tutorial

### 20240807, thursday 

- https://realpython.com/python-data-version-control/

```
$ conda create --name dvc python=3.12.4 -y
```

- ...

```
$ conda activate dvc
```

- ...

```
$ conda config --add channels conda-forge
```

- ...

```
$ conda install dvc scikit-learn scikit-image pandas numpy -y
```

or 

```
$ conda install -c conda-forge dvc scikit-learn scikit-image pandas numpy -y

```

### ready to use dvc!

- ...

```
$ git clone https://github.com/odsogunro/data-version-control.git
```

- ...

```
$ cd data-version-control
```

- get imagnette data
```
$ curl -o ./data/imagenette2-160.tgz  https://s3.amazonaws.com/fast-ai-imageclas/imagenette2-160.tgz

or

$ curl https://s3.amazonaws.com/fast-ai-imageclas/imagenette2-160.tgz -o ./data/imagenette2-160.tgz

or 
$ wget ./data https://s3.amazonaws.com/fast-ai-imageclas/imagenette2-160.tgz 
```

- unzip and copy to ./data/raw/ directory
```
$ tar -xvzf ./data/imagenette2-160.tgz -C ./data
```
- ... 
```
$ tree -L 1 ./data/imagenette2-160

./data/imagenette2-160
├── noisy_imagenette.csv
├── train
└── val
```

- ...
```
$ mv ./data/imagenette2-160/train ./data/raw/train
$ mv ./data/imagenette2-160/val ./data/raw/val

or 

$ rsync -av --info=progress1 ./data/imagenette2-160/train ./data/raw
$ rsync -av --info=progress1 ./data/imagenette2-160/val ./data/raw

or

$ rsync -av --info=progress2 ./data/imagenette2-160/train ./data/raw
$ rsync -av --info=progress2 ./data/imagenette2-160/val ./data/raw

```

- ...
```
$ tree -L 4 .
.
├── LICENSE
├── README.md
├── data
│   ├── imagenette2-160
│   │   ├── noisy_imagenette.csv
│   │   ├── train
│   │   │   ├── n01440764
│   │   │   ├── n02102040
│   │   │   ├── n02979186
│   │   │   ├── n03000684
│   │   │   ├── n03028079
│   │   │   ├── n03394916
│   │   │   ├── n03417042
│   │   │   ├── n03425413
│   │   │   ├── n03445777
│   │   │   └── n03888257
│   │   └── val
│   │       ├── n01440764
│   │       ├── n02102040
│   │       ├── n02979186
│   │       ├── n03000684
│   │       ├── n03028079
│   │       ├── n03394916
│   │       ├── n03417042
│   │       ├── n03425413
│   │       ├── n03445777
│   │       └── n03888257
│   ├── imagenette2-160.tgz
│   ├── prepared
│   └── raw
│       ├── train
│       │   ├── n01440764
│       │   ├── n02102040
│       │   ├── n02979186
│       │   ├── n03000684
│       │   ├── n03028079
│       │   ├── n03394916
│       │   ├── n03417042
│       │   ├── n03425413
│       │   ├── n03445777
│       │   └── n03888257
│       └── val
│           ├── n01440764
│           ├── n02102040
│           ├── n02979186
│           ├── n03000684
│           ├── n03028079
│           ├── n03394916
│           ├── n03417042
│           ├── n03425413
│           ├── n03445777
│           └── n03888257
├── metrics
├── model
└── src
    ├── evaluate.py
    ├── prepare.py
    └── train.py

52 directories, 7 files
```

- ...
```
$ rm -rf ./data/imagenette2-160
$ rm ./data/imagenette2-160.tgz
```

## Part 01 of 05 - Practice the Basic DVC Workflow - START
### first experiment

- create and checkout branch 
```
$ git checkout -b "first_experiment"
```

### initialize dvc
- https://dvc.org/doc/start
- ...
```
$ dvc init 
---

Initialized DVC repository.

You can now commit the changes to git.

+---------------------------------------------------------------------+
|                                                                     |
|        DVC has enabled anonymous aggregate usage analytics.         |
|     Read the analytics documentation (and how to opt-out) here:     |
|             <https://dvc.org/doc/user-guide/analytics>              |
|                                                                     |
+---------------------------------------------------------------------+

What's next?
------------
- Check out the documentation: <https://dvc.org/doc>
- Get help and share ideas: <https://dvc.org/chat>
- Star us on GitHub: <https://github.com/iterative/dvc>
```

- .dvc/ file structure initialized 
```
tree -L 2 .dvc
.dvc
├── config
└── tmp
    ├── btime
    └── exps
```

- .dvc/config is empty
```
$ cat .dvc/config 
```


### turn off anonymous analytics tracking
- ...
```
$ dvc config core.analytics false
$ cat .dvc/config                
[core]
    analytics = false
```

### setup local/remote storage and usage
- https://dvc.org/doc/command-reference/remote/add
- https://dvc.org/doc/user-guide/data-management/remote-storage#configuration
- https://anaconda.org/search?q=dvc-
```
$ mkdir ./data/dvc_remote
$ dvc remote add -d remote_storage $HOME/Projects/data-version-control/data/dvc_remote
$ cat .dvc/config        
[core]
    analytics = false
    remote = remote_storage
['remote "remote_storage"']
    url = $HOME/Projects/data-version-control/data/dvc_remote
```

### tracking files

- ...
```
$ dvc add data/raw/train

100% Adding...|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████|1/1 [00:23, 23.96s/file]
                                                                                                                                                
To track the changes with git, run:

        git add data/raw/.gitignore data/raw/train.dvc

To enable auto staging, run:

        dvc config core.autostage true
```

- ...
```
$ dvc add data/raw/val

100% Adding...|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████|1/1 [00:10, 10.24s/file]
                                                                                                                                                
To track the changes with git, run:

        git add data/raw/.gitignore data/raw/val.dvc

To enable auto staging, run:

        dvc config core.autostage true
```

- ...
```
$ git add data/raw/.gitignore data/raw/train.dvc
$ git add data/raw/.gitignore data/raw/val.dvc
$ dvc config core.autostage true 

$ git add --all
On branch first_experiment
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   .dvc/.gitignore
        new file:   .dvc/config
        new file:   .dvcignore
        modified:   .gitignore
        modified:   README.md
        modified:   data/raw/.gitignore
        new file:   data/raw/train.dvc
        new file:   data/raw/val.dvc
```


### uploadling files

- ...
```
$ git commit -m "first commit with setup and dvc files"
[first_experiment 470eda7] first commit with setup and dvc files
 8 files changed, 311 insertions(+)
 create mode 100644 .dvc/.gitignore
 create mode 100644 .dvc/config
 create mode 100644 .dvcignore
 create mode 100644 data/raw/train.dvc
 create mode 100644 data/raw/val.dvc
```

- ...
```
$ dvc push
Collecting                                                                                                                      |13.4k [00:00, 14.9kentry/s]
Pushing
13397 files pushed 
```

- ...
```
$ git push --set-upstream origin first_experiment
```

### downloading files

#### dvc checkout
- delete the data/raw/val to see how the data can be restored from dvc cache of the remote store 
```
$ rm -rf data/raw/val
```

- get the data back from the cache
```
$ dvc checkout data/raw/val.dvc
Building workspace index                                                                                                          |2.00 [00:00,  243entry/s]
Comparing indexes                                                                                                               |3.94k [00:00, 65.4kentry/s]
Applying changes                                                                                                                 |3.92k [00:02, 1.88kfile/s]
A       data/raw/val/
```

#### dvc fetch
- when you clone your GitHub repository on a new machine, the cache will be empty.
- the fetch command gets the contents of the remote storage into the cache.
- ...
```
$ dvc fetch data/raw/val.dvc

$ dvc checkout
```
[or]
#### dvc pull
- ...

```
$ dvc pull
```

### basic day-to-day usage complete.

```
$ conda install -c conda-forge bpython csvkit mamba -y
```

## Part 01 of 05 - Practice the Basic DVC Workflow - END

...

## Part 02 of 05 - Build a Machine Learning Model - START

### building a machine learning model

1. prepare data for training
2. train a machine learning model
3. evaluate the performance of your model

```
tree -L 1 src
src
├── evaluate.py
├── prepare.py
└── train.py

1 directory, 3 files
```

1. run prepare.py
```
$ python src/prepare.py

tree -L 1 data/prepared/ 
data/prepared/
├── test.csv
└── train.csv


$ head -n 5 data/prepared/train.csv | csvlook 
$ tail -n 5 data/prepared/train.csv | csvlook -H


$ csvlook --max-rows 1 data/prepared/train.csv
|     a | filename                                                                                    | label     |
| ----- | ------------------------------------------------------------------------------------------- | --------- |
| False | /Users/odsogunro/Projects/data-version-control/data/raw/train/n03445777/n03445777_5768.JPEG | golf ball |


$ head -n 5 data/prepared/test.csv | csvlook
$ tail -n 5 data/prepared/test.csv | csvlook -H

$ csvlook --max-rows 1 data/prepared/test.csv
|     a | filename                                                                                  | label     |
| ----- | ----------------------------------------------------------------------------------------- | --------- |
| False | /Users/odsogunro/Projects/data-version-control/data/raw/val/n03445777/n03445777_2412.JPEG | golf ball |
```

### training the model

1. read the csv file that tells python where the images are
2. load the training images into memory
3. load the class labels into memory
4. preprocess the images so they can be used for training
5. train a machine learning model to classify the images
6. save the machine learning model to your disk

```
$ python src/train.py

/usr/local/Caskroom/miniconda/base/envs/dvc/lib/python3.12/site-packages/sklearn/linear_model/_stochastic_gradient.py:744: ConvergenceWarning: Maximum number of iteration reached before convergence. Consider increasing max_iter to improve the fit.

$ dvc add model/model.joblib

tree -L 1 model/            
model/
├── model.joblib
└── model.joblib.dvc

1 directory, 2 files

$ git add --all
$ git commit -m "trained an sgd classifier"
```

### evaluating the model

- ...
```
$ python src/evaluate.py

tree -L 1 metrics 
metrics
└── accuracy.json

$ git add --all
$ git commit -m "evaluate the sgd model accuracy"
```

## Part 02 of 05 - Build a Machine Learning Model - END

- ...
```
$ git push
$ dvc push
```

## Part 03 of 05 - Version Datasets and Models - START

### tagging commits

- ...
```
$ git tag -a sgd-classifier -m "SGDClassifier with accuracy 71.06%"

$ git push origin --tags
Enumerating objects: 1, done.
Counting objects: 100% (1/1), done.
Writing objects: 100% (1/1), 200 bytes | 200.00 KiB/s, done.
Total 1 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
To https://github.com/odsogunro/data-version-control.git
 * [new tag]         sgd-classifier -> sgd-classifier

$ git tag
sgd-classifier
```

### creating one git branch per experiment

- ...
```
$ git checkout -b "sgd-100-iterations"
Switched to a new branch 'sgd-100-iterations'

$ python src/train.py
$ python src/evaluate.py

$ dvc commit

$ git add --all
$ git commit -m "Change SGD max_iter to 100"
```

## Part 03 of 05 - Version Datasets and Models - END

...

## Part 04 of 05 - Share a Development Machine - START

## Part 04 of 05 - Share a Development Machine - END

...

## Part 05 of 05 - Create Reproducible Pipelines - START

## Part 05 of 05 - Create Reproducible Pipelines - END
