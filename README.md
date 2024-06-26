# Contact info:
- ![linkd](https://raw.githubusercontent.com/dmhendricks/signature-social-icons/master/icons/round-flat-filled/35px/linkedin.png) LinkdIn: [tamjidahsan](http://linkedin.com/in/tamjidahsan/)
- ![](https://raw.githubusercontent.com/dmhendricks/signature-social-icons/master/icons/round-flat-filled/35px/mail.png) email: [tamjid@inneed.cloud](tamjid@inneed.cloud) # change this
___
[![git](https://raw.githubusercontent.com/dmhendricks/signature-social-icons/master/icons/round-flat-filled/35px/github.png)](https://github.com/tamjid-ahsan) [![medium](https://raw.githubusercontent.com/dmhendricks/signature-social-icons/master/icons/round-flat-filled/35px/medium.png)](http://tamjida.medium.com/) [![www](https://raw.githubusercontent.com/dmhendricks/signature-social-icons/master/icons/round-flat-filled/35px/www.png)](https://tamjidahsan.vercel.app/)
___
SECTIONS:

1. Conda
    - `env` management
    - `tensorflow`
2. Connect to a jupyter server running in the cloud (`EC2`)
3. `Snowflake`
4. `NVM`

NOTE:

[1] Text inside <> refers to respective names

[2] This is tested on a Windows 11 Pro machine using powershell, WSL2 (Windows Subsystem for Linux, version 2) terminal of Ubuntu-20.04, git-bash version 2.30.0.windows.1, and Microsoft Visual Studio Code version 1.58.2., and latest compatible version of `MacOS` in MacBook Pro 14 inch - M3 Pro (Late 2023)
___
___
# Installing Environment Requirements

If using pip: (must have valid txt file)

```python
pip install -r <requirements>.txt
```

If using conda:

```python
conda create --name <env_name> --file <requirements>.txt
```

> NOTE: Creating requirement file:

```python
# using pip
pip freeze > <requirements_pip>.txt
# using conda
conda list --export > <requirements_conda>.txt
```
___

# Environment management using `Anaconda`

## Install saved env

create new env from .yml

```python
conda env create -f <env_name>.yml
```

> NOTE:

>> create .yml

```python
# show conda list
conda env list
# activate env first
conda activate <env>
```

> reproduce across OS; OS agnostic

```python
## conda environment to a YAML file without specifying 
    # explicit build versions. This allows for greater 
    # flexibility in terms of platform and build dependencies, 
    # making it easier to share environments across different 
    # systems, including Windows, Linux, and macOS.
conda env export > <env>.yml --no-builds 

# Another method
## environment file that can produce environments that 
    # are reproducible across Mac OS, Windows, and Linux, 
    # just including those packages into the environment 
    # file that have been specifically installed.
conda env export --name <env_name> --from-history > <env_file>.yml 
```

>>> [further reading: "conda-for-data-scientists"](https://carpentries-incubator.github.io/introduction-to-conda-for-data-scientists/04-sharing-environments/index.html)

OR

```bash
conda env export > <env>.yml # os specific
OR
conda list -e > requirements.txt
```

Useful command:

```bash
# Create a new environment named py35
conda create --name py35

# Activate the new environment to use it
## WINDOWS: 
conda activate py35
## UNIX-like & macOS: 
source activate py35

# to see all env size 
du -sh ~/<mambaforge-pypy3>/envs/* # use `anaconda3` for conda or `mambaforge-pypy3` for mamba appropriately

# or use this bash command
du -sh $(conda info | grep "active env location" | cut -d ':' -f 2 | awk -F: -vOFS=: '{$1 = $1 "/envs/*"; print}' | sed -e 's/^\s*//')

# or this fish command !!! needs debug
du -h --max-depth 1 (conda info | grep "active env location" | cut -d ':' -f 2 | awk -F: -vOFS=: '{$1 = $1 "/envs"; print}' | sed -e 's/^\s*//')


```

## Add kernel

List all kernels available

```python
jupyter kernelspec list
```

Add kernel

```python
python -m ipykernel install --user --name <env_name> --display-name "<kernel_name>"
```

NOTE:

Remove kernel

```python
jupyter kernelspec remove <kernel_name>
```

## Remove envs

backup env (fist activate the env)
```sh
conda env export > <NAME>.yml
```

remove env (first deactivate the env)

```sh
conda env remove -n <ENV_NAME>
OR
conda remove --name <ENV_NAME> --all
```

clean
```sh
conda clean --all # https://docs.conda.io/projects/conda/en/latest/commands/clean.html
OR
conda clean -tp  # delete tarballs and unused packages
```

there might be some leftovers (installed by pip), manually remove or do `pip uninstall` before removing env.
```sh
pip freeze | xargs pip uninstall -y
```

How it works:

`pip freeze` lists all the installed packages and their versions in your Python environment. The `|` (pipe) operator is used to pass the output of pip freeze to the next command. `xargs` reads the list of packages from the pip freeze output and passes them as arguments to the subsequent command. `pip uninstall -y` uninstalls each package without prompting for confirmation.
___
___
# Create gpu enabled `tensorflow` env  

check requirements: (conda-forge must be available as a channel.)
```
python --version && conda --version
```

install anaconda and tensorflow:
```
conda create --name <NAME_OF_ENV> anaconda 
conda activate <NAME_OF_ENV>
conda install tensorflow-gpu
```
If errored:
- check channel
- check package version
> Note: list all available packages:
> ```
> conda search -f tensorflow-gpu
> ```
> OR
> ```
> conda search -c conda-forge tensorflow-gpu | tail -n5
> ```
> 
> You can use list channels:
> 
> ```
> conda config --show channels
> ```
> 
> 
> This will remove the channel called NOT_WANTED
> ```
> conda config --remove channels NOT_WANTED
> ```
> 
> to add channel:
> 
> ```
> conda config --append channels CHANNEL_NAME
> ```
> 
> This will make conda-forge first hit channel. Your anaconda's default channel will get lower priority.
> 
> ```
> conda config --add channels conda-forge 
> ```

(Optional but recommended) add kernel
```
python -m ipykernel install --user --name <NAME_OF_ENV> --display-name "<NAME_OF_KERNEL>"
```

verify:
* activate env - if not activated already, else skip
```
conda activate <NAME_OF_ENV>
```
* python version
```
python --version
```
* tensorflow version:
```
python -c "import tensorflow as tf; print(tf.__version__)"
```
* GPU:
```
python -c "import tensorflow as tf; print(tf.config.list_physical_devices('GPU'))"
```
___
<b> [<code>tensorflow</code> Compatibility](https://www.tensorflow.org/install/source#gpu) (GPU only) as of: OCT, 22.</b>

|        Version        | Python version |  Compiler |  Build tools | cuDNN | CUDA |
|:---------------------:|:--------------:|:---------:|:------------:|:-----:|:----:|
| tensorflow-2.9.0      | 3.7-3.10       | GCC 9.3.1 | Bazel 5.0.0  | 8.1   | 11.2 |
| tensorflow-2.8.0      | 3.7-3.10       | GCC 7.3.1 | Bazel 4.2.1  | 8.1   | 11.2 |
| tensorflow-2.7.0      | 3.7-3.9        | GCC 7.3.1 | Bazel 3.7.2  | 8.1   | 11.2 |
| tensorflow-2.6.0      | 3.6-3.9        | GCC 7.3.1 | Bazel 3.7.2  | 8.1   | 11.2 |
| tensorflow-2.5.0      | 3.6-3.9        | GCC 7.3.1 | Bazel 3.7.2  | 8.1   | 11.2 |
| tensorflow-2.4.0      | 3.6-3.8        | GCC 7.3.1 | Bazel 3.1.0  | 8.0   | 11.0 |
| tensorflow-2.3.0      | 3.5-3.8        | GCC 7.3.1 | Bazel 3.1.0  | 7.6   | 10.1 |
| tensorflow-2.2.0      | 3.5-3.8        | GCC 7.3.1 | Bazel 2.0.0  | 7.6   | 10.1 |
| tensorflow-2.1.0      | 2.7, 3.5-3.7   | GCC 7.3.1 | Bazel 0.27.1 | 7.6   | 10.1 |
| tensorflow-2.0.0      | 2.7, 3.3-3.7   | GCC 7.3.1 | Bazel 0.26.1 | 7.4   | 10.0 |
| tensorflow_gpu-1.15.0 | 2.7, 3.3-3.7   | GCC 7.3.1 | Bazel 0.26.1 | 7.4   | 10.0 |
| tensorflow_gpu-1.14.0 | 2.7, 3.3-3.7   | GCC 4.8   | Bazel 0.24.1 | 7.4   | 10.0 |
| tensorflow_gpu-1.13.1 | 2.7, 3.3-3.7   | GCC 4.8   | Bazel 0.19.2 | 7.4   | 10.0 |
| tensorflow_gpu-1.12.0 | 2.7, 3.3-3.6   | GCC 4.8   | Bazel 0.15.0 | 7     | 9    |
| tensorflow_gpu-1.11.0 | 2.7, 3.3-3.6   | GCC 4.8   | Bazel 0.15.0 | 7     | 9    |
| tensorflow_gpu-1.10.0 | 2.7, 3.3-3.6   | GCC 4.8   | Bazel 0.15.0 | 7     | 9    |
| tensorflow_gpu-1.9.0  | 2.7, 3.3-3.6   | GCC 4.8   | Bazel 0.11.0 | 7     | 9    |
| tensorflow_gpu-1.8.0  | 2.7, 3.3-3.6   | GCC 4.8   | Bazel 0.10.0 | 7     | 9    |
| tensorflow_gpu-1.7.0  | 2.7, 3.3-3.6   | GCC 4.8   | Bazel 0.9.0  | 7     | 9    |
| tensorflow_gpu-1.6.0  | 2.7, 3.3-3.6   | GCC 4.8   | Bazel 0.9.0  | 7     | 9    |
| tensorflow_gpu-1.5.0  | 2.7, 3.3-3.6   | GCC 4.8   | Bazel 0.8.0  | 7     | 9    |
| tensorflow_gpu-1.4.0  | 2.7, 3.3-3.6   | GCC 4.8   | Bazel 0.5.4  | 6     | 8    |
| tensorflow_gpu-1.3.0  | 2.7, 3.3-3.6   | GCC 4.8   | Bazel 0.4.5  | 6     | 8    |
| tensorflow_gpu-1.2.0  | 2.7, 3.3-3.6   | GCC 4.8   | Bazel 0.4.5  | 5.1   | 8    |
| tensorflow_gpu-1.1.0  | 2.7, 3.3-3.6   | GCC 4.8   | Bazel 0.4.2  | 5.1   | 8    |
| tensorflow_gpu-1.0.0  | 2.7, 3.3-3.6   | GCC 4.8   | Bazel 0.4.2  | 5.1   | 8    |


>> Check compatibly of card: [GPU compatibility](https://developer.nvidia.com/cuda-gpus) | [Wiki](https://en.wikipedia.org/wiki/CUDA#GPUs_supported)

___
___
# Connect to a jupyter server running in the cloud

## PyCharm CE
> Coming Soon.


<br>

## VS Code
> Coming Soon.


# Manual install of tensorflow
> Coming Soon.
___

# Node via `NVM`

## Install Node

```bash
nvm install <node_version>      // Install a specific Node version
nvm install node                // Install latest Node release (Current)
nvm install --lts               // Install latest LTS release of NodeJS
nvm install-latest-npm          // Install latest NPM release only
```

## List Available Node Releases

```bash
nvm ls-remote
nvm ls-remote | grep -i "latest"        
nvm ls-remote | grep -i "<node_version>"
```

## List Installed Nodes

```sh
nvm list node                   // Lists installed Node versions
nvm list  (or)  nvm ls          // Lists installed Node versions with additional release info
```

## Switch To Another Node Version

```sh
nvm use node                      // Switch to the latest available Node version
nvm use <node_version_or_alias>  // Switch to a specific version
nvm use --lts                    // Switch to the latest LTS Node version
```

## Verifying Node Version

```sh
node -v  (or)  node --version
npm -v   (or)  npm --version
nvm -v   (or)  nvm --version
```

## Set Alias

```sh
nvm alias default node                  // Always defaults to the latest available node version on a shell
nvm alias default <node_version>        // Set default node version on a shell
nvm alias <alias_name> <node_version>   // Set user-defined alias to Node versions 

nvm unalias <alias_name>                // Deletes the alias named <alias_name>
```

## Path to Node Executable

```sh
nvm which <installed_node_version>      // path to the executable where a specific Node version is installed
```

## Uninstall Specific Node Version

```sh
nvm uninstall <node_version>    // Uninstall a specific Node version
nvm uninstall --lts             // Uninstall the latest LTS release of Node
nvm uninstall node              // Uninstall latest (Current) release of Node
```

## Uninstall NVM

```sh
To remove, delete, or uninstall nvm, just remove the $NVM_DIR folder (usually ~/.nvm)
```

<br>

___

[source](https://gist.github.com/chranderson/b0a02781c232f170db634b40c97ff455)
___

___

DISCLAIMER:

Author does not take any responsibility if you manage to break your coding environment setup. But if that bad luck ever bestow upon you, don't feel hesitant to [contact](http://linkedin.com/in/tamjidahsan/).
___
contact: <a href="mailto:tamz888@yahoo.com">tamz888@yahoo.com</a> [<img src="https://raw.githubusercontent.com/tamjid-ahsan/ml-endpoint-docker-gunicorn-flask/main/data/TAlogo1.png" alt="TA" height="3%" width="3%">](http://linkedin.com/in/tamjidahsan/)
___
