# computation_wiki

This is the computation wiki for the CIDC-bioinformatics team.

We document how to run WES, RNA-seq, and ATAC-seq pipelines on google cloud, and many others at this central place.

All the documentations are written in plain markdown and deployed using the awesome [MkDocs](https://www.mkdocs.org/).
The deployed website can be found at https://cimac-cidc.github.io/CIDC-bioinformatics-computation_wiki/

### How to contribute

Please read [Github flow](https://guides.github.com/introduction/flow/) for how a general workflow is executed.

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
