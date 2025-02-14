<!--- indicate the Title for this pull request (PR) above -->

<!--
Thank you for contributing to the INBO tutorials repository.
-->

## Description
<!--- Briefly describe the tutorial or article that you want to contribute
or update-->
<!--- You can mention collaborators with "@githubname"-->


## Related Issue
<!--- if this closes an issue make sure to include e.g., "closes #4"
or similar - or if it just relates to an issue make sure to mention
it like "#4" -->
<!--See https://docs.github.com/en/github/managing-your-work-on-github/linking-a-pull-request-to-an-issue#linking-a-pull-request-to-an-issue-using-a-keyword-->


## Task list

<!--see https://docs.github.com/en/github/managing-your-work-on-github/about-task-lists
for an explanation on how to use task lists-->

<!-- Please check if the following steps are OK:-->

- [ ] My tutorial or article is placed in a subfolder of `tutorials/content/`
- [ ] The novel tutorial has a meaningful name, in relation to the content of the tutorial.
- [ ] The filename of my tutorial or article is `index.md`. In case of an Rmarkdown tutorial I have knitted my `index.Rmd` to `index.md` (both files are pushed to the repo). 
- `yaml` header:
    - [ ] *(recommended)* I am included as author in the `authors` yaml tag, using `[MY_AUTHOR_ID]`. An author information file exists in `<tutorials>/data/authors/<author>.toml`.
    - [ ] I have added `categories` to the YAML header and my category tags are from the [list of category tags](https://github.com/inbo/tutorials/blob/master/static/list_of_categories)
    - [ ] *(optional)* I have included `tags` in the YAML header (see the tags listed in the [tutorials website side bar](https://inbo.github.io/tutorials/) for tags that have been used before)
    - [ ] The `date` is in format `YYYY-MM-DD` and adjusted.
- [ ] *(recommended)* I have previewed this PR locally (see steps below; ask previous contributors for help) and confirmed that the new content renders as expected.


## Previewing the pull request

Thanks to GitHub Actions, an artifact (=zip file) of the rendered website is automatically created for each pull request.
This provides a way to preview how these updates will look on the website, useful to contributors and reviewers.

### Instructions to preview the updated website

1) On the PR page, you can find a "details" link under "checks - On PR, build the site and ...". Go there, click on the top link in the left sidebar ("Summary"), and download the generated artifact at the bottom of the page.
2) Decompress it into a target directory, e.g. `Downloads/tutorials_preview`.
3) To preview the website, use a program which can serve `http` sites on your local machine. One such option is [the `servr` package](https://github.com/yihui/servr) in R: `& '\C:\Program Files\R\R-4.4.2\bin\Rscript.exe' -e "servr::httd('./tutorials_preview')" -p8887` (*make sure to adjust the path to your `Rscript.exe`*).
4) Point your browser to http://localhost:8887.
5) Review the updated website. As a contributor, you can push extra commits to update the PR. As a reviewer, you can accept/refuse/comment the PR.

**Note: for step 3, you can use any other simple HTTP server to serve the current directory, e.g. [Python `http.server`](https://docs.python.org/3/library/http.server.html): `python -m http.server 8887 --bind localhost --directory path/to/tutorials_preview`**


### Alternative: Locally Building the Site

Alternatively, you can build the entire site locally ([see the README for instructions](https://github.com/inbo/tutorials?tab=readme-ov-file#building-the-site)); the Hugo preview server will update changes on the fly.
This requires [Hugo](https://gohugo.io/getting-started) to be installed on your computer.
