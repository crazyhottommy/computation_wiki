
### CIDC Bioinformatics Group Pipeline documentation

# computation_wiki

This is the computation wiki for the CIDC-bioinformatics team.

We document how to run WES, RNA-seq, and ATAC-seq pipelines on google cloud, and many others at this central place.

All the documentations are written in plain markdown and deployed using the awesome [MkDocs](https://www.mkdocs.org/).
The deployed website can be found at https://cimac-cidc.github.io/CIDC-bioinformatics-computation-wiki/

### How to contribute

Please read [Github flow](https://guides.github.com/introduction/flow/) for how a general workflow is executed.


This site collects the documentations for running RIMA, CHIPS, WES and TCR

![](docs/google-cloud/plots/CIMAC-CIDC-FlowChart.png)

## How to contribute

1. Fork the GitHub repo:https://github.com/CIMAC-CIDC/CIDC-bioinformatics-computation-wiki

2. Git clone your fork.

2. Under ~/CIDC-bioinformatics-computation-wiki/docs, add markdown file in the corresponding sub-dir.

3. Edit ~/CIDC-bioinformatics-computation-wiki/mkdocs.yml to point the path to the newly added documentations.

4. Commit your changes, git push and do the pull request.

```bash

git status

git add *

git commit -m "what you've done"

git push origin master
```

## Log
```bash
pip install mkdocs

##### Theme #####
## https://squidfunk.github.io/mkdocs-material/
pip install mkdocs-material

#### Extensions ####
### pymdown-extensions ###
## requirements
pip install markdown
pip install pygments
pip install fontawesome_markdown
## install
pip install pymdown-extensions

#### Plugins ####
pip install mkdocs-git-revision-date-plugin
pip install mkdocs-git-revision-date-localized-plugin
pip install mkdocs-minify-plugin
```
