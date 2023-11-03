---
description: 模擬一段測試過程
---

# Mock / Spy

測試過程中總會有一些流程必須跳過，像是寄信、打外部 API 等，略過這些流程的方法就是 Mock/Spy。

***

先舉一個例子，這是一個常見的類別，裡面有 `__construct`和 `execute` 方法：

```php
use App\Handler;

class Target
{
    public function __construct(private TemplateHandlerhandler $handler)
    {
    }
    
    public function execute($mailer): string
    {
        return $mailer->send($this->handler->getTemplate(), new User(3))
    }
}
```

這個類別相依了 `Handler` 、 `$mailer` 和 `User` 的物件，在使用的時候會像這樣：

```php
$handler = new TemplateHandler();
$target = new Target($handler);
$target->execute(new Mailer());
```

如果直接跑測試，就真的會寄信了。所以先示範一下如何 mock：

```php
use Mockery;

/**
 * @test
 */
public function send_email_is_work(): void
{
    ## arrange
    // mock handler
    $handler = Mockery::mock(TemplateHandler::class);
    $handler->shouldReceive('getTemplate')
        ->once()
        ->andReturn('hello');
    // mock mailer
    $mailer = Mockery::mock(Mailer::class);
    $mailer->shouldReceive('send')
        ->once()
        ->andReturn('ok');
    ## act
    $result = (new Target($handler))->execute($mailer);
    ## assert
    $this->assertEquals('ok', $result);
}
```

以上述範例來說，`handler` 和 `mailer` 是可以被 mock 的。但 `new User(3)` 無法被替換。這種 `new class` 的方式會造成功能的依賴耦合（Dependency），但 Laravel 有提供 `app` 可以隨時隨地的反轉依賴（Dependency inversion）。這種作法還有個好處，可以避免 `__construct` 寫太多東西：

```php
app(User::class, ['id' => 3]);
```

如果要 mock Laravel 依賴注入這寫法，就必須要這樣寫：

```php
// 物件沒有參數
app(TemplateHandler::class);
$this->mock(TemplateHandler::class);
// 物件有參數
app(User::class, ['id' => 3]);
$this->app->offsetSet(User::class, $this->mock(User::class));
```

***

### Mock vs Spy

Mock 與 Spy 最大的差異是先後驗證，Mock 的驗證會寫在 `arrange`，Spy 驗證會寫在 `assert` 階段上。兩者的程式碼也差在 Spy 多了 [shouldIgnoreMissing()](https://github.com/mockery/mockery/blob/fab5c135e23fdf40ef7d0e58489a18f581b05e34/library/Mockery.php#L124C90-L124C90)，Spy 會在執行階段時忽略全部並回傳 null，如果 Spy 的物件需要回傳值就需要這樣寫：

```php
## arrange
$spy = $this->spy(TemplateHandler::class);
$spy->expects()->getTemplate()->andReturn('hello')
## act
// do something...
## assert
$spy->shouldHaveReceived('execute')->once();
```

就如剛剛所說的，Spy 與 Mock 只差一個方法。所以 mock 也是可以使用 `shouldHaveReceived`，但 mock 通常會使用 `shouldReceive` 先行驗證，所以沒有必要驗證兩次，除非 mock 是使用 `expects` 來模擬行為。

如果希望所有驗證都寫在 `assert` 區塊，就使用 Spy。

我是覺得被 mock 的東西只是為了執行測試的手段過程，我就不會特別去寫驗證來證明有測過他，因為整個 I/O 都可能是假的。

***

### 不要 Mock/Spy

使用 Mock/Spy 可以更容易的寫好測試，但用得越多，整個測試就會越假，越偏離實際使用。像上面的範例，我可以把所以外部物件全都 mock 一遍，但就只能測到哪些物件有沒使用，而不能保護當中的物件如果遇到更改需求時能不能反應異常。

像是 `TemplateHandler` 的 `getTemplate` 本來是不需要參數的，但有天有個需要帶入第一個參數，這時測試還是會過。因為 mock/Spy 把整個物件取代掉了，所以不會知道這個物件的實際需求。

除非要做局部的測試（只測試目標的特定流程，故可以快速忽略前面的需求）或排除需要網路或外部服務的功能，否則不推薦濫用它們。
