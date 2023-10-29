---
description: 由淺入深，快速入門
---

# 第一支測試

在寫測試之前，需要瞭解一些基礎知識：

### 3A 原則 - 測試只做這些事：

* arrange - 測試前的準備
* act - 執行測試
* assert - 驗證測試結果

### 測試資料

單元測試為了要快與不依賴外部延伸資料，所以測試資料庫必須在每次跑完一個測試案例的時候清空資料。可以使用和執行環境相同的資料庫，但比較推薦使用 SQLite，因為又小又快，也能直接使用記憶體當儲存空間。

### 測試要放哪

測試的檔案必須跟受測的檔案的相對路徑一致

受測檔案： `app/Http/Controllers/API/TargetController.php`

測試檔案：`tests/Unit/Http/Controllers/API/TargetControllerTest.php`

***

示範測試 TargetController 的 show 方法：

target:

```php
class TargetController extends Controller
{
    public function show(int $id): JsonResponse
    {
        $target = Target::where('status', true)->findOrFail($id)
    
        return response()->json([
            'id' => $target->id,
            'name' => $target->name,
            'created_at' => $target->created_at->toDateTimeString(),
            'updated_at' => $target->updated_at->toDateTimeString(),
        ]);
    }
}
```

test:

```php
class TargetControllerTest extends TestCase
{
    use RefreshDatabase;
    
    /**
     * @test
     * @group TargetController
     */
    public function get_target_is_work(): void
    {
        ## arrange
        Carbon::setTestNow('2018-09-20 12:00:00');
        Target::factory()->state([
            'id' => 920,
            'name' => '測試目標',
            'status' => true
        ])->create();
        ## act
        $response = $this->getJson(route('targets.show', [
            'id' => 920
        ]))
        ## assert
        $response->assertOk();
        $this->assertSome([
            'id' => 920,
            'name' => '測試目標',
            'created_at' => '2018-09-20 12:00:00',
            'updated_at' => '2018-09-20 12:00:00',
        ], $response->json());
    }
}
```

在這個測試案例我做了幾件事

1. 把 3A 步驟註解上去，這樣日後維護的時候能快速定位要修改的位置。
2. 測試名稱使用 snake-case，主要是因為測試名稱通常很長，且 PHPUnit 會根據 \`-\` 和大寫斷句
3. 使用 `RefreshDatabase` ，Laravel 內建重置資料庫的 Trait
4. `@test` 相當於在 test method 開頭寫 test，告訴 PHPUnit 這個是測試案例
5. `@group` 標記相同功能，日後跑測試案例可以只跑這個群組，可以加速開發流程&#x20;
6. `Carbon::setTestNow()` 設定測試時間，避免測試案例受到真實時間影響
7. &#x20;`Target::factory()`，Laravel 內建建立假資料的功能。
8. `$this->getJson`，Laravel 內建測試 Open API 的方法
