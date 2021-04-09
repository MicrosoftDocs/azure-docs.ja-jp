---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "78255823"
---
## <a name="robots933456-in-logs"></a>ログの robots933456

コンテナー ログで次のメッセージが表示されることがあります。

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

このメッセージは無視してかまいません。 `/robots933456.txt` は、コンテナーが要求を処理できるかどうかを調べるために App Service が使用するダミーの URL パスです。 404 応答は、パスが存在しないことを示すだけですが、コンテナーが正常であり、要求に応答できる状態であることを App Service に知らせます。

