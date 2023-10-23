---
description: 資料夾的本質在於分類
---

# 資料夾

分類有幾個重點

1. 不要看到數量太多就分類
2. 不要根據前綴或後綴分類
3. 根據使用情境分類

舉例來說，在 Laravel 的 `App\Models`可以根據資料庫的名字來分類：

```
// 單資料庫，就不需要再開資料夾
- App
    - Models
        - User.php
        - Product.php

// 多個資料庫，可以使用資料庫名稱或 `database.connections` name
- App
    - Models
        - main
            - User.php
        - staff
            - Staff.php
            - Permissions.php   
```

Laravel `App\Http\Controllers` 的資料夾規劃：

```
- App
    - Http
        - Controllers
            // 根據 User Domain 分類
            - Staff
                - UserController.php
                - OrderController.php
            - Main
                - UserController.php
                - ProductController.php
                - CarController.php                
            - Vendor
                - UserController.php
                - ProductController.php
            - Webhook
                - WebhookController.php
```
