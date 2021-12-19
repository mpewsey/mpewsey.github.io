---
layout: post
title: How to Use Jupyter Notebooks for GitHub Pages Posts
tags: python
mathjax: false
image: https://user-images.githubusercontent.com/23442063/144757976-66f7ef35-cc7e-4912-a7cc-e5bb665f571b.png
---

Due to their ability to directly incorporate code and code-generated visualizations, Jupyter notebooks are an excellent tool for technical blogging. However, some extra steps are required to setup and convert Jupyter notebooks to a format that can be used by Jekyll, the site generator used by GitHub Pages. This post provides the steps necessary to prepare a Jupyter notebook for conversion to a markdown document that can be used by Jekyll. In addition, it provides a script by which all posts for a GitHub Pages website can be converted via automation.

<!--excerpt-->

![Jupyter to Jekyll Post](https://user-images.githubusercontent.com/23442063/144757976-66f7ef35-cc7e-4912-a7cc-e5bb665f571b.png)

## Notebook Setup

The following sections present special requirements and considerations for setting up a Jupyter notebook that will be converted to a markdown post.

### YAML Front Matter

As required for Jekyll posts, the first cell in a Jupyter notebook that will be used for a post must include [YAML front matter](https://jekyllrb.com/docs/front-matter/). To ensure that Jupyter converts this text as-is when converting the document to markdown, the cell must be marked as a `Raw NBConvert` cell as shown below:

![YAML Front Matter](https://user-images.githubusercontent.com/23442063/144682448-f713583b-4b3a-4730-a24e-b7e380ba4a10.png)

### Write Your Post

Next, write your post as usual using the Jupyter notebook. Cells that include text should be of `Markdown` type, while code cells should obviously be `Code` type.

If using `matplotlib` to generate plots within the notebook, either the `%matplotlib notebook` or `%matplotlib inline` magic commands will be required prior to plotting in the notebook.

It should be noted that which `matplotlib` magic command you use will determine how the images are incorporated into your post later during the conversion process. If the `%matplotlib notebook` magic command is used, Jupyter converts the plots into Base64 string images, which are included directly within the converted markdown file. No other image files are created. However, if the `%matplotlib inline` magic command is used, separate image files are created within a folder at the notebook location, and the resulting markdown file will include references to these images.

Personally, I prefer to use the `%matplotlib notebook` magic command since it creates larger plots by default and eliminates the need to keep up with additional files. However, most search engines will not include Base 64 string images in their search results, and there may be some web browser caching benefits to having separate image files. If those aspects are something you care about, you may want to consider using inline plots instead.

## Manually Convert the Jupyter Notebook to Markdown

To manually convert the Jupyter notebook to markdown, while in the notebook editor, simply select `File > Download as > Markdown (.md)`. You will download either a markdown file or, if your notebook generates artifacts, a zip file for the notebook.

Alternatively, you may use the `jupyter nbconvert <NOTEBOOK_PATH> --to markdown` command from your system's command line interface.

With your notebook converted to markdown, simply place the markdown file in the posts directory of your Jekyll website. If your post includes any image artifacts, you will additionally need to place those in an accessible website directory, such as an assets folder, then update the image references within the markdown file to point to that location.

Unfortunately, the need to move files around and update post contents for image references can quickly become a major burden when manually converting notebooks. Inevitably, you will want to make edits to your notebooks and will have to repeat the process again to have these edits reflected in your posts. Fortunately, we can automate this process, as demonstrated in the following section.

## Script to Convert Jupyter Notebooks to Markdown

To automate the conversion of notebooks to markdown files to be used as posts, I've written the following Python script:

<script src="https://emgithub.com/embed.js?target=https%3A%2F%2Fgithub.com%2Fmpewsey%2Fmpewsey.github.io%2Fblob%2Fmain%2Fconvert_notebooks.py&style=github&showBorder=on&showFileMeta=on&showCopy=on&fetchFromJsDelivr=on"></script>

This script first finds the paths to all Jupyter notebooks in the designated notebooks directory then performs the following actions on each:

* Calls the `jupyter nbconvert <NOTEBOOK_PATH> --to markdown` command to convert the Jupyter notebook to markdown.
* Performs post-processing actions on the converted markdown file, e.g. updating image paths to the website's assets directory and removing undesired strings that `nbconvert` includes.
* Moves the created markdown file to a location in the website's posts directory.
* Moves any images generated during the conversion to the website's assets directory.
* Computes the hash of the notebook file contents and writes it to a `.sha256` file in the same folder as the notebook. This hash is used to detect if the notebook has been modified during future conversions and, if not, allows those files to be skipped, reducing overall conversion time.

To use this script locally, simply place it in the root directory of your website and edit the directory variables at the top of the script to suit your project. Ensure that Jupyter is installed and run the script from the command line:

```
python convert_notebooks.py
```

## Running Script Using GitHub Actions

To ensure that the posts in your GitHub repository are always up to date without needing to always run this script locally, it is convenient to set up a GitHub action that runs the script every time a new commit is pushed to the main branch of the repository.

To do this, add the following YML file to the special `.github/workflows` folder in your project. This YML file checks out the repository to the GitHub virtual environment, installs Python and the necessary script dependencies, runs the notebook conversion script, and then pushes any resulting changes back to the repository.

<script src="https://emgithub.com/embed.js?target=https%3A%2F%2Fgithub.com%2Fmpewsey%2Fmpewsey.github.io%2Fblob%2Fmain%2F.github%2Fworkflows%2Fconvert_notebooks.yml&style=github&showBorder=on&showFileMeta=on&showCopy=on&fetchFromJsDelivr=on"></script>

With this process, Jupyter notebooks can be relatively easily converted to markdown posts for use by GitHub Pages.
