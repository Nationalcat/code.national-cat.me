---
description: 常聽人說好的程式碼不用註解，但我看所有 contributors 很多的開源專案都有註解
---

# 註解

先示範沒註解的程式碼：

```php
<?php

use App\Models\PhoneBill;
use App\Enums\SendDriver;
use App\Services\Sender\SenderFactory;

function sendPhoneBills(array $ids, SendDriver $driver): void 
{
    $bills = PhoneBill::whereIntegerInRaw('id', $ids)
        ->where('is_send', false)
        ->get();
        
    $sender = SenderFactory::create($driver);
    foreach($bills as $bill) {
        $sender->send([
            'name' => $bill->name,
            'price' => $bill->price,
            'phone' => $bill->phone,
            'expired_at' => $bill->expired_at->toDateTimeString(),
            'created_date' => $bill->created_at->toDateTimeString(),
        ]);
    }
    
    PhoneBill::whereIntegerInRaw('id', $ids)
        ->where('is_send', false)
        ->update(['is_send' => true]);
}

```

這是一個發送電話帳單通知的功能，但沒有任何註解的情況下，看這段程式碼其實會有點吃力，因為需要會寫 PHP、懂 Laravel Eloquent，Factory 設計模式，還有發送通知的條件。



如果程式碼加上自然語言的註解，那麼閱讀上就只剩自然語言的隔閡了。

{% code fullWidth="false" %}
```php
<?php

use App\Models\PhoneBill;
use App\Enums\SendDriver;
use App\Services\Sender\SenderFactory;

/**
 * 發送電話帳單通知
 */
function sendPhoneBills(array $ids, SendDriver $driver): void 
{
    $bills = PhoneBill::whereIntegerInRaw('id', $ids)
        // 未發送過通知
        ->where('is_send', false)
        ->get();
    // 初始化通知發送功能，並發送通知
    $sender = SenderFactory::create($driver);
    foreach($bills as $bill) {
        $sender->send([
            'name' => $bill->name,
            'price' => $bill->price,
            'phone' => $bill->phone,
            'expired_at' => $bill->expired_at->toDateTimeString(),
            'created_date' => $bill->created_at->toDateTimeString(),
        ]);
    }
    // 更新發送狀態，避免重複發送
    PhoneBill::whereIntegerInRaw('id', $ids)
        ->where('is_send', false)
        ->update(['is_send' => true]);
}

```
{% endcode %}

***

### 在你覺得需要空一行的時候註解

大部分的人會習慣在一個程式邏輯告一段落的時候換行，但卻不留下註解，那一行其實很適合留一段訊息給之後維護的人。

### 在你覺得程式碼有點多的時候註解

註解也有分類的效果，像是在一個很多鍵的陣列上：

```php
<?php

return [
    // 基本資料
    'id' => 321312,
    'name' => '黃柑仔',
    'phone' => '0912123123',
    // 帳單資訊
    'price' => 399.00,
    'expired_at' => '2023-10-30 12:00:00',
    'created_date' => '2023-10-22 12:00:00',
];
```

### 在你需要看文件的時候註解

有時註解無法一行表達所有事情，就會需要引導看註解的人到外部連結或寫多行註解

```php
<?php

/**
 * https://developers.ecpay.com.tw/?p=2856
 *
 * 特店編號(MerchantID)：3002607
 * 特店後台登入帳號：stagetest3
 * 特店後台登入密碼：test1234
 * 特店統一編號：00000000
 * 串接金鑰HashKey：pwFHCqoQZGmho4w6
 * 串接金鑰HashIV：EkRm7iFT261dpevs
 */
class ECPayAPI 
{

}
```

***

### 可以用，但少用的註解

有少數狀況需要在程式碼的結尾註解，像是需要描述 Array 的鍵的時候。

```php
<?php

return [
    // 基本資料
    'id' => 321312, // 編號
    'name' => '黃柑仔', // 名字
    'phone' => '0912123123', // 電話
    // 帳單資訊
    'price' => 399.00, // 帳單金額
    'expired_at' => '2023-10-30 12:00:00', // 逾期時間
    'created_date' => '2023-10-22 12:00:00', // 建立時間
];
```

### 註解應該用什麼語言？

個人建議不要使用外語，尤其這份程式碼幾年內不會給外國人維護，真的沒有必要考大家英文。因為有人怕丟臉就會留下更簡略的註解，有人英文爛就留下語法錯誤或錯誤意思的註解。

如果有打算跨國的話，我也建議建立新的在地專案才對，讓國外的同事也好做事。話說台灣已經是很便宜的人力市場了，向外擴編不就會增加成本嗎？
