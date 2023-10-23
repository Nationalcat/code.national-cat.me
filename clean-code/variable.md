---
description: 變數就像小孩一樣，生的越多負擔越大
---

# 變數

程式碼多半會髒，大概有八成是因為變數太多了。變數多會延伸出幾個問題：

* 命名會變困難，因為簡單的單字可能都被使用
* 變數會長大，也會有叛逆期。同一個變數經歷過幾個不同階段的迴圈以及 by reference 的修改，這支變數充滿了未知數。
* 如果不只一個變數再重複利用，還有多個變數時，就會直接照成可怕的耦合。這大概就像延長線上的所有電線打結的樣子，你必須要順著頭或尾一步一步的解開這條線的來龍去脈。

先舉一個待解決的例子（這個案例算是非常輕微）：

```php
<?php
// bad code
function example () {
    $result = [];
    $products = [];
    $disabledProductIds = [];
    // 透過 by reference 更新變數資料
    $this->getProducts($products);
    foreach($products as $key => $product) {
        // 移除狀態為 false 的商品
        if ($product->status === false) {
            unset($products[$key]);
            $disabledProductIds[] = $product->id;
            continue;
        }
        // 商品名字加上原本索引
        $product->name .= "($key)";
    }

    $products = array_value($products);
    $result['products'] = $products;
    $result['disabled_ids'] = $disabledProductIds;

    return $result;
}
```

總共養了三支變數，當中 `$products` 還有 by Reference 的方式更新，接著又使用 `unset` 移除資料。

這個範例會對於之後的維護和擴充產生幾個問題：

* 當要對 `$products` 操作的時候，要從頭 `dd($products)` 這個變數來確定應該怎麼寫改。
* 如果要知道這個功能最後輸出什麼結果，就必須要 `dd($result)`，這還只能勉強猜到變數的內容，因為難保之後會不會有人從中間選擇性寫入其他值。

減少變數的方法有兩種：

* 將變數丟到新的類別或方法去處理，但會分散邏輯
* 使用 fluent interface 去解，Laravel Collection 就能很漂亮的解套了

```php
<?php
// good code

// 總共只使用一個變數
function example () {
    // `get products` 應直接回傳 products，而些 reference
    $products = $this->getProducts();
    // 雖然會多跑一次迴圈，但對於可讀性來說，是非常划算
    return [
        'products' => collect($products)
            // 已啟用商品
            ->filter(fn($product) => $product->status)
            ->map(fn($prodcut, $key) => [
                'id' => $product->id,
                'name' => "{$product->name}({$key})",
                'status' => $product->status,
            ])
            ->values()
            ->toArray(),
        'disabled_ids' => collect($products)
            // 已停用商品
            ->filter(fn($product) => $product->status === false)
            ->pluck('id')
            ->toArray(),
    ];
}
```

***

### 減少未知的變數

如果最後輸出的變數是陣列或 Collection，建議重新 Map 一次，可以有效減少未知的部分，還能集中程式碼邏輯。

{% code title="bad code" %}
```php
return $products;
```
{% endcode %}

{% code title="good code" %}
```php
// 單層 array
return [
    // 商品
    'id' => $product->id,
    'name' => $product->name,
    'created_at' => $product->created_at->toDateTimeString(),
    'updated_at' => $product->updated_at->toDateTimeString(),
    // 商品規格
    'specs' => $product->specs->map(fn($spec) => [
        'id' => $spec->id,
        'name' => $spec->name,
        'stocks' => $spec->stocks,
     ])
];

// 多層 Array 或多層 Collection
return collect($products)->map(fn($product) => [
    // 商品
    'id' => $product->id,
    'name' => $product->name,
    'created_at' => $product->created_at->toDateTimeString(),
    'updated_at' => $product->updated_at->toDateTimeString(),
    // 商品規格
    'specs' => $product->specs->map(fn($spec) => [
        'id' => $spec->id,
        'name' => $spec->name,
        'stocks' => $spec->stocks,
     ])
]);
```
{% endcode %}

