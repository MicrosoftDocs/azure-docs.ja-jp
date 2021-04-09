---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424879"
---
## <a name="deploy-the-function-project-to-azure"></a>Azure に関数プロジェクトをデプロイする

Azure への関数アプリの作成に成功したら、[func azure functionapp publish](../articles/azure-functions/functions-run-local.md#project-file-deployment) コマンドを使用して、ローカル関数プロジェクトをデプロイすることができます。  

次の例の `<APP_NAME>` は、実際のアプリ名に置き換えてください。

```console
func azure functionapp publish <APP_NAME>
```

publish コマンドを実行すると、次のような出力結果が表示されます (簡潔にするため一部省略しています)。

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
