---
description: 好的命名可以讓你少寫很多註解
---

# 命名

## 好的命名會有三個重點要素：

1. 不容易撞名
2. 沒有縮寫
3. 可以判讀型別

***

### 1. 不容易撞名

只要越靠近命名對象，其命名會越獨特。舉例來說，如果從資料庫撈出商品資料，其命名應該會是 `product` ，而不是 `data` ，盡可能的描述對象的特徵。

如果情境有遇到需要區分**已啟用**或**已停用**，那麼可以使用 `enabledProduct` 、 `disabledProduct`。

### 2. 沒有縮寫

縮寫會增加學習成本，不是所有人都會知道縮寫的原文意思。

> _bzerrstr 你知道這個是什麼嗎？這是 PHP 專門處理 bzip2 的原生函式。_

### 3. 可以判讀型別

* Boolean：可以藉由肯定句的方式來命名，像是 `isActive` 、`canBuy` 、 `hasProduct`
* Iterable：只要單字是複數就意味著可以跑迴圈，像是 `products`
* Integer：通常結尾會是 `total` 、 `count` ，或是單字本身就是數值 `quantity`

***

### 駝峰式命名 vs 蛇形命名法

蛇形命名法（snake\_case）可讀性最高，但不適合寫在功能裡，會爆長。

```php
<?php

use App\CodeTables\ProductType;
use App\Models\Product;

function has_product_and_product_is_main_product($product): bool
{
    return $product instanceof Product::class 
        && $product->type === ProductType::MAIN;    
}

$product = Product::find(920);
// 再接一個條件，這個視窗可能就塞不一下
if (has_product_and_product_is_main_product($product)) {
    echo '是主商品';
}
```

駝峰式命名（CamelCase）有個很大的缺點，單字越多，可讀性就越差，如果要使用駝峰式命名，就要盡可能的減少單字。

```php
<?php

use App\CodeTables\ProductType;
use App\Models\Product;

$product = Product::find(920);
$hasProduct = $product instanceof Product::class;
// bad name
$hasProductAndProductIsMainProduct = $hasProduct && $product->type === ProductType::MAIN;
// good name
$isMainProduct = $hasProduct && $product->type === ProductType::MAIN;
```

至於應該用哪種命名系統，建議遵循程式語言或測試環境訂的標準，像是 PHP 的 PSR-12、PHPunit 的測試案例是用 snake-case。
