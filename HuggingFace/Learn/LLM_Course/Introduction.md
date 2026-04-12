# Introduction

* Course focus: This Hugging Face course is about setting up a working environment so you can run the provided code yourself.
* Recommended order: If you’re starting, review Chapter 1 first, then return to set up your environment.
* Dependencies are Python packages: All libraries used in the course are available as Python pip packages, so setup is about installing the required Python libraries.
* Two setup options:
  * Google Colab notebook
  * Python virtual environment
* Beginner recommendation: If you’re new, start with a Colab notebook.
* Windows not covered: The course materials won’t cover Windows setup; if you use Windows, use Colab instead.
* Other OS support: For Linux/macOS, you can follow either approach.
* Hugging Face account required: Much of the course depends on having a Hugging Face account—create one early.
* Why Colab: Colab is the simplest setup and can provide free accelerating hardware (GPUs/TPUs) for smaller workloads.
* Next step in Colab: Once comfortable, create a new notebook and proceed with the setup there.

## Install libraries (pip)

- The course libraries are installed using **pip** (Python’s package manager).
- In notebooks (e.g., Colab), you can run system commands by prefixing them with **`!`**.

### Install 🤗 Transformers

```python
# Install libraries
!pip install transformers
```

## Install the full Transformers dependencies (dev-style install)

### Lightweight install vs full install

- The initial install gives a **very light** 🤗 Transformers.
- It may not include **ML framework dependencies** (e.g., PyTorch or TensorFlow).

### Recommended install for the course

- Install the development version including required dependencies (and sentencepiece support):

```bash
!pip install transformers[sentencepiece]
```

## Install time

- This may take a bit of time but prepares you for the rest of the course.

## Using a Python virtual environment (venv)

### 1) Install Python

Follow a guide to install Python on your system.
Confirm installation:

```bash
python --version
```

### 2) Keep the “main” Python clean

* Treat the system Python as main.
* Don’t install app packages into it.
* Create separate virtual environments for each application to avoid dependency conflicts.

### 3) Create a project directory

Example:

```bash
mkdir ~/transformers-course
cd ~/transformers-course
```

### 4) Create a virtual environment (venv)

```bash
python -m venv .env
```

After this, you should see a .env directory:

```bash
ls -a
```

### 5) Activate / deactivate the environment

```bash
#### Activate
source .env/bin/activate

##### Deactivate
deactivate
```

### 6) Verify the environment is activated
Check which Python is being used:

```bash
which python
```

If it points to .../transformers-course/.env/bin/python, activation succeeded.

### 7) Install course dependencies inside the venv

```bash
pip install "transformers[sentencepiece]"
```

You’re then ready to continue the course.

