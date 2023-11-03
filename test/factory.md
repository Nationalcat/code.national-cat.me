---
description: 建立假資料，同時建立資料的狀態與關聯
---

# Factory

通常寫在 Laravel 的程式幾乎都會呼叫資料庫，所幸有提供 [Eloquent factories](https://laravel.com/docs/10.x/eloquent-factories) 來建立資料庫的假資料。

***

### 測試資料庫有個很重要的特質——每次測試都要是新的。

如果打算做一個測試資料庫，但裡面的資料是既有的資料的話，每次撰寫測試都需要重新理解這個資料庫裡面藏了什麼資料。這無疑會增加寫測試的難度。

***

測試目標：

```php
class Target
{
    public function getTarget(int $id): array
    {
        $target = Target::with(['categories'])->findOrFail($id);
        
        return [
            'id' => $target->id,
            'name' => $target->name,
            'categories' => $target->categories->map(fn($category) => [
                'id' => $category->id,
                'name' => $category->name,
            ])->toArray()
        ];
    }
}
```

### 測試範例：

```php
use Illuminate\Foundation\Testing\RefreshDatabase;

class TargetTest extends TestCase
{
    use RefreshDatabase;
    
    public function get_target_is_work(): void
    {
        ## arrange
        Target::factory()
            ->state([
                'id' => 920,
                'name' => '柑仔'
            ])
            ->has(Category::factory()->state([
                'id' => 123,
                'name' => '橘貓'
            ]))
            ->create;
        ## act
        $result = $this->app->make(Target::class)->getTarget(920);
        ## assert
        $this->assertEquals([
            'id' => 920,
            'name' => '柑仔',
            'categories' => [
                [
                    'id' => 123,
                    'name' => '橘貓'
                ]
            ]
        ], $result);
    }
}
```

***

1. 首先，在測試之前都要使用 `RefreshDatabase` trait，會在執行每支測試的時候重置資料庫。
2. 接著建立 Target 與 Target 關聯的 Category，這時測試資料庫就會有測試案例上直接寫出來的測試資料。
3. 最後執行測試的時候就會照著測試資料去跑程式

雖然這麼做會讓測試案例變得很長，但可以很清楚資料資料的關聯，以及這些資料與這段程式碼的關係。尤其是當資料的狀態可以影響流程走向時，就可以很細微的將測試覆蓋到每一行程式碼上。
