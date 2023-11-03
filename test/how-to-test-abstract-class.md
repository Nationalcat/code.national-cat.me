# 測試 Abstract Class

abstract class 不像 class 可以直接執行和測試，Laravel 也沒有教怎麼測試。所以這邊示範一下要怎麼測試吧。

***

### 測試目標：

```php
abstract class AbstractHandler
{
    abstract public function do(): void;
    
    protected function add(int $quantity): void
    {
        $this->quantity += $quantity;
    }
}
```

### 測試範例：

```php
class AbstractHandlerTest extends TestCase
{
    use RefreshDatabase;
    /**
     * @test
     */
    public function add_quantity_is_work(): void
    {
       ## arrange
       $target = new class extends AbstractHandler {
          public int $quantity = 12;
          public function do(): void
          {
             $this->add(3);
          }
       }
       ## act
       $target->do(3);
       ## assert
       $this->assertEquals($target->quantity, 15);
    }
}
```

***

可以透過繼承匿名類別來測試 abstract class。
