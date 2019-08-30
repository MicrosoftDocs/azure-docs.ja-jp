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
ms.openlocfilehash: e2d63ab38bad341400538c5079fee22737cf0b8e
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562958"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Azure に関数アプリのプロジェクトをデプロイする

Azure で関数アプリを作成した後、[`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core Tools コマンドを使用して Azure にプロジェクト コードをデプロイすることができます。 以下の例の `<APP_NAME>` は、前の手順で作成したアプリの名前に置き換えてください。

### <a name="c--javascript"></a>C\# または JavaScript

```command
func azure functionapp publish <APP_NAME>
```

### <a name="python"></a>Python

```command
func azure functionapp publish <APP_NAME> --build remote
```

### <a name="typescript"></a>TypeScript

```command
npm run build:production 
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

`HttpTrigger` の `Invoke url` 値をコピーします。これを使用して Azure で関数をテストできるようになります。 URL には、関数キーである `code` クエリ文字列が含まれています。 このキーにより、他のユーザーが Azure で HTTP トリガー エンドポイントを呼び出すことが難しくなります。