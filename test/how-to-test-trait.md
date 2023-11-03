# 測試 Trait

Trait 不像 class 可以直接執行和測試，Laravel 也沒有教怎麼測試。所以這邊示範一下要怎麼測試吧。

***

### 測試目標：

```php
trait Handler
{
    public function add(int $quantity): void
    {
        $this->quantity += $quantity;
    }
}
```

### 測試範例：

```php
class handlerTest extends TestCase
{
    use RefreshDatabase;
    /**
     * @test
     */
    public function add_quantity_is_work(): void
    {
       ## arrange
       $target = new class {
          use Handler;
          
          public int $quantity = 0;
       }
       ## act
       $target->add(3);
       ## assert
       $this->assertEquals($target->quantity, 3);
    }
}
```

***

可以透過匿名類別來使用 trait。
