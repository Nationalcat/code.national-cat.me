---
description: 切記！參數和分號是越少越好
---

# 函式

要鑑別一個函式是否好用與好不好維護，可以先從參數和分號數量來決定。

### 當參數越多，對於使用這個函式的人來說會越痛苦

```php
// bad code
function getProduct(
    int $id,
    int $accessUserId,
    bool $status,
    array $filters,
    bool $isTest
): Product
{
    // do something...
    return $product;
}
```

像這個例子，會建議把只留一個 `id`，其他的判斷可以把資料撈出來後再判斷。

```php
// good code
function getProduct(int $id): Product
{
    // do something...
    return $product;
}
```

<mark style="color:red;">最好的函示是沒有參數</mark>，盡可能往這個目標設計。

***

### 由分號數量判斷函式的長度

如果直接看行數，會把註解和陣列以及 fluent interface 加總進去，然後促使人過度把程式碼拆散。

```php
<?php

namespace App\Http\Controllers;

use App\Models\Product;
use App\Enums\ProductType;
use Illuminate\Http\JsonResponse;

class ProductController extends Controller
{
    public function show(int $id): JsonResponse
    {
        $product = Product
            // 已啟用
            ::where('status', true)
            // 主商品
            ->where('type', ProductType::MAIN)
            ->with([
                'specs' => fn($query) => $query->where('status', true)
            ])
            ->findOrFail($id);
    
        return response()->json([
            // 商品資料
            'id' => $product->id,
            'name' => $product->name,
            'created_at' => $product->created_at->toDateTimeString(),
            'update_at' => $product->updated_at->toDateTimeString(),
            // 規格資料
            'specs' => $product->specs->map(fn($spec) => [
                'id' => $spec->id,
                'name' => $spec->name,
                'price' => $spec->price,
                'stocks' => max($spec->stocks, 15), // 最多限購 15 個
            ])
        ]);
    }
}
```

這個範例看起來很長，但總共只使用兩個分號，也能很清楚的表示取特定商品資料，以及輸出結果以及結果的格式。
