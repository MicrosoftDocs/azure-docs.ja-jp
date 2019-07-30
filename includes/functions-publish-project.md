---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 48bb91b3b2e9a31de63e515edb857bc2a170ea79
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180960"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Azure に関数アプリのプロジェクトをデプロイする

Azure で関数アプリを作成した後、[`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core Tools コマンドを使用して Azure にプロジェクト コードをデプロイすることができます。 次のコマンドで、`<APP_NAME>` には、前の手順で作成したアプリの名前を指定します。

```bash
func azure functionapp publish <APP_NAME>
```

次のような出力が表示されます (読みやすくするために一部が省略されています)。

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

HttpTrigger の Invoke URL 値をコピーします。これを使用して Azure で関数をテストできるようになります。 URL には、関数キーである `code` クエリ文字列が含まれています。 このキーにより、他のユーザーが Azure で HTTP トリガー エンドポイントを呼び出すことが難しくなります。