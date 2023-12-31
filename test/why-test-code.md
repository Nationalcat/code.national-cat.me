---
description: 最常聽人說寫測試是寫兩份程式碼，但我認為測試程式碼才能讓功能完整。
---

# 為什麼要寫測試

> 醜話說在前面，測試覆蓋率達到 100%，系統還是會有 bug！

如果寫測試無法完全保證程式不會出錯，那為什麼還要寫呢？

1. 回歸測試（保護程式碼）
2. 還原使用者邏輯
3. 避免寫出肥大的功能

### 回歸測試（保護程式碼）

這是所有理由中最實用的一個。在修改和擴充功能的時候，很容易改 A 壞 B，即便是人力充沛的專案，也會有漏測的狀況，但自動化測試可以專案所有寫過的測試迅速的測一遍。如果改壞了，也會很快知道。（3000 個測試案例，大概可以在 20 秒跑完吧！）

### 還原使用者邏輯

測試案例會根據使用這個功能的人的角度去設計。像是更新訂單付款狀態的測試，會先建立未付款的假訂單，然後丟到這個 API 試試看會不會如預期那樣將該筆訂單從未付款改成指定的狀態。

也因為測試的資料有模擬使用者的邏輯，在要改別人的程式碼的時候也能看到比較全面的資料平面圖。

### 避免寫出肥大的功能

因為要還原使用邏輯，所以測試其實不太好寫。為了讓測試好寫，會很自然的把功能瘦身（拆功能或元件化）。

