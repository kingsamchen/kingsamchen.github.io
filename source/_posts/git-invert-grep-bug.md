---
title: Git Invert-grep Bug
categories: PROGRAMMING
date: 2018-09-14 15:27:33
tags: [git, bug]
---
前段时间部门技术老板要求每个组统计每个研发同学在某个版本的千行 bug 率，所以第一步就要能够统计某个人在某个指定分支的提交信息。

这个很容易做，利用

```
git log d313a36e199^..HEAD --shortstat --author="Kingsley Chen"
```

就可以拿到包含修改记录的提交信息。但是要同时排除掉 merge 提交的数据，避免出现重复和乱记。

对于普通的 `git merge`，只需要在前面的命令中加入 `--no-merge` 即可，但是问题在于，我们项目的分支管理采用的不是 git flow branching model，而是类似 chromium 的 trunk-based model。

我们所有的提交都会在 develop 上完成，发布某个版本时切除 release 分支，版本的修改在 release 上完成，发布后通过 `git merge --squash` 将 release 分支的新提交合会 develop；所以前面的 `--no-merge` 便没有了效果。

想到的一个方案是使用 `--grep='Merge commits from'` 来获得和并提交，然后利用 `--invert-grep` 进行取反。

但是在实践中我发现，`--invert-grep` 虽然在文档上说是 invert the previous grep，但是它实际上会导致 `--author=''` 过滤失效，因为 `--author` 内部实质上也是等价于一个 grep

这里有一个相关的问题 https://public-inbox.org/git/xmqqshjj4ce5.fsf@gitster.mtv.corp.google.com/ 但是看起来官方认为这个行为并不是一个 bug。

最后我选择的解决方案是用 python 写一个完整的 tool，手动做了排除，代码也比较简单，看了一下都不超过 80 LOC

```python
#!python3
# -*- coding: utf-8 -*-
# 0xCCCCCCCC

import re
import shlex
import subprocess
import sys

FIRST_REV = ''
LAST_REV = ''

def run(cmd):
    #print('-> ' + cmd)
    return subprocess.check_output(shlex.split(cmd)).decode('utf-8')

def query_authors():
    return list(map(lambda l: l.strip().split('\t')[1],
                    run('git shortlog -sn {}^..{}'.format(FIRST_REV, LAST_REV)).splitlines()))

def sum_commits(records):
    insertion_num = 0
    deletion_num = 0

    for stat in records:
        mr = re.search(r'(\d+)(?= insertion)', stat)
        if mr:
            insertion_num += int(mr.groups()[0])

        mr = re.search(r'(\d+)(?= deletion)', stat)
        if mr:
            deletion_num += int(mr.groups()[0])

    return insertion_num, deletion_num

def print_analyzed(results):
    print('{0:>20}Insertion{0:>10}Deletion{0:>10}Total'.format(' '))
    for author, records in results.items():
        print('{1:<20}{2:>6}{0:<10}{3:>6}{0:<10}{4:>6}'.format(' ',
            author, records[0], records[1], records[2]))

def main():
    if len(sys.argv) < 3:
        print('No full range specified! Analyze entire history for current branch.')
        return

    global FIRST_REV, LAST_REV
    FIRST_REV = sys.argv[1]
    LAST_REV = sys.argv[2]

    authors = query_authors()
    results = {}
    for author in authors:
        commits_query = 'git log {}^..{} --shortstat --author="{}"'.\
                            format(FIRST_REV, LAST_REV, author)
        commits = list(filter(lambda s: re.search('files? changed', s),
                              run(commits_query).splitlines()))
        total_insertions, total_deletions = sum_commits(commits)

        excluded_query = commits_query + ' --grep="Merge commits"'
        excluded_commits = list(filter(lambda s: re.search('files? changed', s),
                                       run(excluded_query).splitlines()))
        excluded_insertions, excluded_deletions = sum_commits(excluded_commits)

        total_insertions -= excluded_insertions
        total_deletions -= excluded_deletions

        results[author] = (total_insertions, total_deletions, total_insertions + total_deletions)

    print_analyzed(results)

if __name__ == '__main__':
    main()
```
