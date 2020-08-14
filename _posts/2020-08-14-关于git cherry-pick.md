---
layout:     post
title:      关于 git cherry-pick
date:       2020-08-14 14:00:00 +0800
categories: original
---

## cherry-pick能做什么

可以将A分支的某些更改的内容（某些commit）移到B分支上。也就是说cherry-pick会重演某些commit，将那些commit的更改重新执行一次。

## 如何使用cherry-pick

 - 查看某个分支的最近n次提交，找到想要cherry-pick的某个或某几个commit

    ```
    git log --oneline -n
    ```

 - checkout到目标分支

    ```
    git checkout develop
    ```

 - cherry-pick某一次提交

    ```
    git cherry-pick abcd1234
    ```

 - 或者cherry-pick某几次提交
    
    ```
    git cherry-pick  
    20c2f506d789bb9f041050dc2c1e954fa3fb6910 2633961a16b0dda7b767b9264662223a2874dfa9 
    5d5929eafd1b03fd4e7b6aa15a6c571fbcb3ceb4  
    ```
    多个commit-hash使用空格分割，最好按照提交时间先后顺序排列，最先提交的commit放在前面

## cherry-pick也可以用在同一个分支

被删除的功能需要再次被加回来时，可以在同一分支上使用cherry-pick,使用方法与多分支相同。