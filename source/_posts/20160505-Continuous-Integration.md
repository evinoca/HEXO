---
title: Continuous Integration
date: 2016-05-05 16:30:33
categories: Tools Collector
tags: Tools
---



决定将持续集成加入自学列表中，虽然一直在用Jenkins但是一直也没有认真研究过还有什么可以扩展的地方。

另外打算找时间学一下buildBot

下文来自http://docs.python-guide.org/en/latest/scenarios/ci/

# Continuous Integration

## Why?

Martin Fowler, who first wrote about [Continuous Integration](http://martinfowler.com/articles/continuousIntegration.html)(short: CI) together with Kent Beck, describes the CI as follows:

> Continuous Integration is a software development practice where members ofa team integrate their work frequently, usually each person integrates atleast daily - leading to multiple integrations per day. Each integration isverified by an automated build (including test) to detect integration errorsas quickly as possible. Many teams find that this approach leads tosignificantly reduced integration problems and allows a team to developcohesive software more rapidly.

<!--more-->

## Jenkins

[Jenkins CI](http://jenkins-ci.org) is an extensible continuous integrationengine. Use it.

## Buildbot

[Buildbot](http://docs.buildbot.net/current/) is a Python system toautomate the compile/test cycle to validate code changes.

## Tox

[tox](http://tox.readthedocs.org/en/latest/) is an automation tool providingpackaging, testing and deployment of Python software right from the console orCI server. It is a generic virtualenv management and test command line toolwhich provides the following features:

- Checking that packages install correctly with different Python versions andinterpreters
- Running tests in each of the environments, configuring your test tool ofchoice
- Acting as a front-end to Continuous Integration servers, reducing boilerplateand merging CI and shell-based testing.

## Travis-CI

[Travis-CI](https://travis-ci.org/) is a distributed CI server which buildstests for open source projects for free. It provides multiple workers to runPython tests on and seamlessly integrates with GitHub. You can even have itcomment on your Pull Requests whether this particular changeset breaks thebuild or not. So if you are hosting your code on GitHub, travis-ci is a greatand easy way to get started with Continuous Integration.

In order to get started, add a `.travis.yml` file to your repository withthis example content:

```
language: python
python:
  - "2.6"
  - "2.7"
  - "3.2"
  - "3.3"
# command to install dependencies
script: python tests/test_all_of_the_units.py
branches:
  only:
    - master

```

This will get your project tested on all the listed Python versions byrunning the given script, and will only build the master branch. There are alot more options you can enable, like notifications, before and after stepsand much more. The [travis-ci docs](http://about.travis-ci.org/docs/)explain all of these options, and are very thorough.

In order to activate testing for your project, go to [the travis-ci site](https://travis-ci.org/)and login with your GitHub account. Then activate your project in yourprofile settings and you’re ready to go. From now on, your project’s testswill be run on every push to GitHub.

