# Building a project in conda and pypi

## A new start: The github actions

This was a really confusing process for me in the past, but it has been significantly improved since I learned how to use [github actions](https://github.com/features/actions). Once everything is set up as below, the only thing you have to do is release on github and your package will automatically be released on [pypi](https://pypi.org/) and [conda-forge](https://conda-forge.org/).

### Initial setup

- Make sure that `LICENSE` is included in your `MANIFEST.in` file. For an example see [here](https://github.com/xgcm/xgcm/blob/master/MANIFEST.in)


### Setting up pypi

Sign up for pypi and add secrets to github.

[I got a photo for this on the desktop....]

### Github actions script

In your repo root folder create `.github/workflows/pythonpublish.yaml` with the following content.

```
name: Upload Python Package

on:
  release:
    types: [created]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - name: Set up Python
        uses: actions/setup-python@v1
        with:
          python-version: '3.x'
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install setuptools setuptools-scm wheel twine
      - name: Build and publish
        env:
          TWINE_USERNAME: ${{ secrets.PYPI_USERNAME }}
          TWINE_PASSWORD: ${{ secrets.PYPI_PASSWORD }}
        run: |
          python setup.py sdist bdist_wheel
          twine upload dist/*
```
Your package has to be initially released to publish it on `conda-forge`. If your package is not released yet. Go ahead and release in github now.

### Register conda-forge feedstock

This is still a bit confusing but usually does not require a lot of maintenance besides the setup. So lets go through it step by step:

To add a new package to conda-forge we will follow their [instructions](https://conda-forge.org/#add_recipe)
1. Fork [conda-forge/staged-recipes](https://github.com/conda-forge/staged-recipes)
2. Clone your fork
3. Create a new branch `git checkout -b [your-package-name]`
4. Add a new conda recipe in a new "recipes/[your-package-name]" directory. An example for a `meta.yaml` file:

```
{% set name = "[your-package-name]" %}
{% set version = "[initial-version]" %}

package:
  name: {{ name|lower }}
  version: {{ version }}

source:
  url: https://pypi.io/packages/source/{{ name[0] }}/{{ name }}/{{ name }}-{{ version }}.tar.gz
  sha256: [sha]

build:
  noarch: python
  number: 0
  script: "{{ PYTHON }} -m pip install . -vv"

requirements:
  host:
    - python
    - pip
    - pytest-runner
  run:
  [ your package dependencies]
    - python
    - xarray
    - pandas
    - numpy

test:
  imports:
    - [your-package-name]

about:
  home: https://github.com/jbusecke/cmip6_preprocessing
  license: MIT
  license_family: MIT
  license_file: LICENSE
  summary: 'Some code to homogenize naming and parse variables in a xgcm compatible xarray dataset'

  description: |
    Frustrated with how 'dirty' CMIP6 data still is? Do you just want to run a
    simple (or complicated) analysis on various models and end up having to
    write logic for each seperate case? Then this package is for you.
    Developed during the cmip6-hackathon this package provides utility
    functions that play nicely with intake-esm.
  dev_url: https://github.com/jbusecke/cmip6_preprocessing

extra:
  recipe-maintainers:
    - [github-username]
```
All the content in [...] has to be customized.

> To find your hash (sha256), check [here](https://conda-forge.org/docs/maintainer/adding_pkgs.html#populating-the-hash-field)

Once all the fields are filled to your liking test the package locally (you have to have [docker](https://hub.docker.com/editions/community/docker-ce-desktop-mac/) installed for this).

```
cd staged-recipes
CONFIG=osx64 ./.circleci/run_docker_build.sh
```

Once this step is successful (and it can take a while to build !), commit your changes and start a pull request to [conda-forge/staged-recipes](https://github.com/conda-forge/staged-recipes) with `git push -u origin [branchname]`. 

Once the PR is submitted check the CI and see if there are any changes that need to be made (someone from the conda-forge team might comment). This can take a bit...

Then after this is merged they will build a new feedstock-[your package name] repo with you as a maintainer. There is not really a notification though, so just check in a bit.

Every 









## Old ways (wayyy to complicated).

I am sooo confused which elements are needed for both ways of publishing….


So far I have added these files for mom_xtools that were not in the original cookiecutter (which cookiecutter did i use?)

- `MANIFEST.in`
- Change travis ci to  [https://travis-ci.com/jbusecke/mom_xtools](https://travis-ci.com/jbusecke/mom_xtools) 

## PyPI
I tried to register the package `python setup.py register` and it did come back with this:
```
Registering mom_xtools to https://upload.pypi.org/legacy/
Server response (410): Project pre-registration is no longer required or supported, upload your files instead.
WARNING: Registering is deprecated, use twine to upload instead (https://pypi.org/p/twine/)
```

Apparently this method is outdated….
 [https://packaging.python.org/tutorials/packaging-projects/](https://packaging.python.org/tutorials/packaging-projects/)  is the official tutorial 

## Testing Workflow

### Tagging the version
Install the needed dependencies:
```
conda install setuptools wheel twine
```

Now install [versioneer](https://github.com/warner/python-versioneer) (optional), which automatically grabs the version number from git (no need to manually change setup.py).
```
pip install versioneer
```
Install versioneer by adding this to your `setup.cfg`
```
[versioneer]
VCS = git
style = pep440
versionfile_source = <package_name>/_version.py
versionfile_build = <package_name>/_version.py
tag_prefix =
parentdir_prefix = <package_name>-
```
then run `versioneer install` to make necessary changes.

Check your `setup.py`. It must contain an `import versioneer` statement and within the `setup()` call add:
```
version=versioneer.get_version(),
cmdclass=versioneer.get_cmdclass(),
```
Then run `versioneer install` one more time to check if it stoped complaining. Commit and push your changes. Then you can start tagging your new version.

The usual git stuff…
```
git init
git commit -m “first commit”
git remote add origin git@github.com:jbusecke/mom_xtools.git
git push -u origin master
```

Set initial tag and push
```
git tag -a v0.1.0
git push —tags
```

Confirm that the tag is set with:
```
git tag
```

### Creating tarball
Create the tarball for distribution with
```
python setup.py sdist bdist_wheel
```

### Dry run Test PyPi (optional)
This uploads it to the test channel
`python -m twine upload --repository-url https://test.pypi.org/legacy/ dist/*`
Create a fresh conda env: `conda create --name pypi_test`.
`pip install -i https://test.pypi.org/simple/ --no-deps example-pkg-your-username`
> Note that `_` seem to be replaced with `-` in the package name.

The the [tutorial](https://packaging.python.org/tutorials/packaging-projects/) says, that one should to this 
```
import example_pkg
example_pkg.name
```
but for me only `example.__name__` seemed to work. This might be a version issue…

Not sure if this is serious…but it seems to work for now…

### Publishing to Pypi
`twine upload dist/*`

> Dont forget to add the badge to your repo!



### Maybe delete the dist stuff after successful update?


## !!! Problems !!!
The name and version thing does not work…that needs to be fixed.

## Manual steps
Travis-ci might have to be activated…never sure which it is org or com.

## Maintenance after updates.
Always define a proper new version or pypi complains…
If there are versions with that funky build scramble at the end, remove them from `dist/`, then proceed.
```
git tag vX.Y.Z
#or alternatively with annotation
git tag -a <tag-name> -m <tag-message>
git push —tags
python setup.py sdist bdist_wheel
twine upload dist/*
```

## Wish List:
How to I get the info on pypi updated? E.g. from the readme? Might have to go to Manual steps
- Explain every single step and how to do it manually (e.g. versioneer)

## References:
Version according to this:  [https://semver.org/](https://semver.org/) 
