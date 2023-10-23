---
description: 寫給未來的人信
---

# Git Commit (編輯記錄)

當所有 Clean Code 技巧都用盡了，Git Commit 將會是 Clean Code 的最後一道防線。在程式碼因為詭異而複雜的商業邏輯弄的很難懂時，維護的人會像外尋找其他文件，而第一步會是 git blame。



在每次完成一個完整的功能或修改時，應撰寫一次 commit，而非累積更多功能再一次上。如果累積很多功能，那麼 commit message 一定會失去重點。或如果把一個功能的所有流程都寫成獨立的 commit，會很難清楚知道當時寫這段程式碼的目的。



Commit 的格式如下：

```
行為: 內容 [#issue-code]

1. 新增
2. 修改
3. 修復
4. 移除
5. 補充資訊
```

行為是指 git flow 定義行為種類

* feature
* bugfix
* release
* hotfix

當然，也可以自行定義，像是

* upgrade
* refactor
* tests

***

推薦的工具：

1. VScode：[https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)
2. IntelliJ IDEA：[https://plugins.jetbrains.com/plugin/7499-gittoolbox](https://plugins.jetbrains.com/plugin/7499-gittoolbox)
