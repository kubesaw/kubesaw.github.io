# KubeSaw Docs

This repository contains the documentation website code and Markdown source files for kubesaw.github.io.

## Contributing

The website is built using [mkdocs](https://www.mkdocs.org/getting-started/#getting-started-with-mkdocs)

**NOTE**: To contribute changes, make sure you do that from your own fork!

### Prerequisites

- python3
```shell
python3 --version
Python 3.11.5
```

### 1.  Create an isolated virtual environment
From the root folder of the repo run: 
```shell
python3 -m venv venv
``` 
**NOTE**: the second `venv` is the name of the folder containing the [virtual environment](https://docs.python.org/3/library/venv.html)

Now you need to activate the virtual env so that all the dependencies will be installed only here, not on your global python installation.

```shell
source venv/bin/activate
```

### 2. Install dependencies

```shell
pip install -r requirements.txt 
```

NOTE:
- add newly needed dependencies in the `requirements.txt` file

### 3. Run the local server

```shell
mkdocs serve
```
You should now be able to see the website locally by going to http://127.0.0.1:8000/.
Changes to the source content will auto-reload the webpage in the browser.

### 4. Stop the virtual environment

Run the following command to deactivate the virtual env
```shell
deactivate
```

### 5. Publish your changes

When you're done with your changes open a PR for the `main` branch, once the PR is merged the changes will be automatically deployed to https://kubesaw.github.io/ by the GH CI job.



