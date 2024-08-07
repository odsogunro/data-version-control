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

# first experiment

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