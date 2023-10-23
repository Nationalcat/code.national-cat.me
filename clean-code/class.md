---
description: 比較大的函式
---

# 類別

我會把 Class 分成兩種

1. 函式庫
2. 物件

### 函式庫

像是 Laravel 的 Controller，可以任意建立 public methods。



### 物件

Laravel 大部分的功能都是物件，像是 Command、ServiceProvider、Middleware，會要你實作或覆寫某些方法。~~當然，你也可以把這種物件當函式庫來擴充，結果程式碼就會很髒。~~



這些物件都是根據 [SOLID](https://w.wiki/7tBC) 物件導向設計原則來設計的。



直接解釋 SOLID  除了很難懂外，還又臭又長，所以從結果講重點：

1. 藉由抽象類別（abstract class）或介面（interface）限制可被使用的功能，這樣可以把所有實作功能抽象成同一件事。
2. 物件是從抽象開始被理解和設計，所以物件必須實作（implement）於抽象類別或介面。
3. 不在抽象類別和介面的規範內，必須封裝（defind to private）。









