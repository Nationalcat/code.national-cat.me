---
description: 程式碼越靠左邊就越平易近人
---

# 排版

### 限制每行字數

寫程式推薦的寬度是每行 80 ～ 120 字，即便有外接大螢幕，也不建議超過這個數字。

***

### 換行

程式碼越往右邊就意味著該行要判斷的事情越複雜，且不容易閱讀上下文

```php
// bad code, 條件太長了
if ($conditions['status'] === true && $conditions['is_test'] === false && $conditions['type'] === 'boolean') {
    echo 'hello';
}
// good code, 換行，這種方式還能寫註解
if ($conditions['status'] === true
 && $conditions['is_test'] === false
 && $conditions['value'] === 'hello') {
    echo 'hello';
}
// good code, 萃取出新的方法
if ($this->canEcho($conditions)) {
    echo 'hello';
}
```

***

### 使用匿名函式

```php
// bad code
collect($products)->map(function($product) use ($request) {
    return [
        'id' => $product->id,
        'name' => $product->name,
        'status' => $product->status && $request->can_display
    ]
});

// good code
collect($products)->map(fn($product) => [
    'id' => $product->id,
    'name' => $product->name,
    'status' => $product->status && $request->can_display
]);
```

***

### 三元運算子正確用法

如果結果是單數，請換行

```php
// 變數
$name = $displayName
        ? $product->name
        : '商品';
// 陣列
[
    'name' => $displayName
        ? $product->name
        : '商品'
];
```

如果是 null 或 array，可以這樣寫

```php
// 變數
$coupon = empty($coupon) ? null : [
    'id' => $coupon->id,
    'name' => $coupon->name
];
// 陣列
[
    'coupon' => empty($coupon) ? null : [
        'id' => $coupon->id,
        'name' => $coupon->name
    ]
];
```

如果輸出結果是兩組陣列，還是建議抽出去做

```php
// 變數
$coupon = $coupon['default'] === 'default' 
    ? $this->formatDefaultCoupon($coupon)
    : $this->formatCoupon($coupon);
// 陣列
[
    'coupon' => $coupon['default'] === 'default' 
        ? $this->formatDefaultCoupon($coupon)
        : $this->formatCoupon($coupon)
    ]
];
```

***

### 使用雙引號或 [Heredoc](https://www.php.net/manual/en/language.types.string.php#language.types.string.syntax.heredoc)

雙引號和 Heredoc 可以解析變數，如果沒有變數需求則使用單引號

```php
// 雙引號
"{$product['name']}, is {$this->getColor($product)}"
// heredoc, 可以直接換行
<<<MESSAGE
{$product['name']}, is {$this->getColor($product)}
MESSAGE;
```



