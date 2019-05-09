---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 914c006daf49e22ebec870a549bfdbc63f882647
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2019
ms.locfileid: "55147978"
---
## <a name="test"></a>関数をテストする

cURL を使用して、Mac または Linux コンピューターで、または Windows で PowerShell を使用して、デプロイした関数をテストします。 次の cURL コマンドを実行します。`<app_name>` プレースホルダーを Function App の名前に置き換えます。 URL にクエリ文字列 `&name=<yourname>` を追加します。

```powershell
Invoke-WebRequest -Uri "https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>"
```

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![ブラウザーに表示された関数の応答。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

コマンド ラインで使用可能な `cURL` または `Invoke-WebRequest` がない場合は、Web ブラウザーのアドレスに同じ URL を入力します。 再度、`<app_name>` プレースホルダーを関数アプリの名前に置き換え、URL にクエリ文字列 `&name=<yourname>` を追加して、要求を実行します。

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![ブラウザーに表示された関数の応答。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
