% Demandez le programme
% Vincent Rouvreau
% 26 november 2019

## What were the problems ?

+ How to be sure notebooks are not broken by a commit ?

+ Broken render message:
  \center\includegraphics[height=20cm]{something_went_wrong.png}

## How to be sure notebooks are not broken by a commit ?

cf. [\textcolor{blue}{\underline{.circleci/config.yml}}](https://github.com/GUDHI/TDA-tutorial/blob/md2html_for_ghpages/.circleci/config.yml)

~~~bash
for notebook in *.ipynb
do
  echo "Processing $notebook file..."
  jupyter nbconvert --to notebook \
    --inplace --execute $notebook
done
~~~

## Other iPython notebooks render tools

* [\textcolor{blue}{\underline{Jupyter nbviewer}}](https://nbviewer.jupyter.org/)
  + ... usage is limited
\center\includegraphics[height=5.5cm]{nbviewer_jupyter_error_503.png}

## Other iPython notebooks render tools

* [\textcolor{blue}{\underline{Binder}}](https://mybinder.org/)
  + requires a [\textcolor{blue}{\underline{.binder/environment.yml}}](https://github.com/GUDHI/TDA-tutorial/blob/master/.binder/environment.yml) file
  + [![Binder](binder_badge_logo.png){ height=10px }](https://mybinder.org/v2/gh/GUDHI/TDA-tutorial/master)
  + ... can be slow to be installed

## Render html from notebooks

cf. [\textcolor{blue}{\underline{.circleci/config.yml}}](https://github.com/GUDHI/TDA-tutorial/blob/md2html_for_ghpages/.circleci/config.yml)

~~~bash
for notebook in *.ipynb
do
  echo "Processing $notebook file..."
  jupyter nbconvert --to html \
    $notebook
done
~~~

## GitHub pages

 [\textcolor{blue}{\underline{Deploying documentation to github pages with CI}}](https://circleci.com/blog/deploying-documentation-to-github-pages-with-continuous-integration/)

\center\includegraphics{gh_pages.png}

## gh-pages node.js module

\vspace{-.5cm}
cf. [\textcolor{blue}{\underline{.circleci/config.yml}}](https://github.com/GUDHI/TDA-tutorial/blob/md2html_for_ghpages/.circleci/config.yml)

~~~yaml
  notebook-html-deploy:
    docker:
      - image: node:8.10.0
    steps:
      - checkout
      - attach_workspace:
          at: _doc
      - run:
          name: Install and configure dependencies
          command: |
            npm install -g --silent gh-pages@2.0.1
            git config user.email "gudhi-ci@lists.gforge.inria.fr"
            git config user.name "gudhi-ci"
      - run:
          name: Deploy docs to gh-pages branch
          command: gh-pages --dist _doc/html
~~~

## ERROR: The key you are authenticating with has been marked as read only.

* Create SSH key
~~~bash
ssh-keygen -t rsa -b 4096 -C "gudhi-ci@lists.gforge.inria.fr"
# Accept the default of no password for the key (This is a special case!)
# Choose a destination such as 'docs_deploy_key_rsa'
~~~

* [\textcolor{blue}{\underline{Add a SSH key}}](https://circleci.com/gh/GUDHI/TDA-tutorial/edit#ssh)

cf. [\textcolor{blue}{\underline{.circleci/config.yml}}](https://github.com/GUDHI/TDA-tutorial/blob/md2html_for_ghpages/.circleci/config.yml)

~~~yaml
steps:
  - add_ssh_keys:
    fingerprints:
      - "a2:9d:91:b2:4d:ef:4c:95:d3:75:fb:dc:56:d0:67:c3"
~~~

## Final fixups

cf. [\textcolor{blue}{\underline{.circleci/config.yml}}](https://github.com/GUDHI/TDA-tutorial/blob/md2html_for_ghpages/.circleci/config.yml)

~~~yaml
steps:
  ...
  - run:
      name: Disable jekyll builds
      command: |
        touch _doc/html/.nojekyll
  ...
  - run:
      name: Deploy docs to gh-pages branch
      command: |
        gh-pages --dotfiles --message "[skip ci] Update" \
          --dist _doc/html
~~~

## Conclusion
