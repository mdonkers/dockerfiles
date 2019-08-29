## dockerfiles

[![Travis CI](https://img.shields.io/travis/mdonkers/dockerfiles.svg?style=for-the-badge)](https://travis-ci.org/mdonkers/dockerfiles)

This repository holds a few Dockerfiles that might be helpful for others as well.
Builds can be found on Docker Hub under [miel](https://hub.docker.com/u/miel).


**Table of Contents**

<!-- toc -->

- [About](#about)
- [Resources](#resources)
  * [My dotfiles](#my-dotfiles)
- [Using the `Makefile`](#using-the-makefile)
- [Attributions](#attributions)

<!-- tocstop -->

## About

Almost all (or all) of these images should be available on Docker Hub under [miel](https://hub.docker.com/u/miel).
Use them at your own risk. Sometimes the images might be tailored to my own needs, although
I try to keep them as generic or configurable as possible.

## Resources

### My dotfiles

My [dotfiles](https://github.com/mdonkers/dotfiles) contain several aliases and helper functions to run these images, specifically here: [github.com/mdonkers/dotfiles/blob/master/.dockerfunc](https://github.com/mdonkers/dotfiles/blob/master/.dockerfunc).

## Using the `Makefile`

```
$ make help
dockerfiles                    Tests the changes to the Dockerfiles build.
image                          Build a Dockerfile (ex. DIR=telnet).
run                            Run a Dockerfile from the command at the top of the file (ex. DIR=telnet).
shellcheck                     Runs the shellcheck tests on the scripts.
test                           Runs the tests on the repository.
```

## Attributions

Code for making and testing these containers is largely taken from [Jess Frazelle](https://github.com/jessfraz/dockerfiles) (at least initial versions).
Also Dockerfiles might have taken inspiration from her, as well as other places around the web.
