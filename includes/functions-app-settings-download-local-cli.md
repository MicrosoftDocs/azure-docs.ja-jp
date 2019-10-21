---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 801613f4e3fb1e754856d716c6815895ea5da3aa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839101"
---
必要なストレージ アカウントと共に Azure で関数アプリを既に作成しました。 このアカウントの接続文字列は、Azure のアプリ設定に安全に格納されています。 この記事では、同じアカウントのストレージ キューにメッセージを書き込みます。 関数をローカルで実行しているときにストレージ アカウントに接続するには、アプリ設定を local.settings.json ファイルにダウンロードする必要があります。 次の Azure Functions Core Tools コマンドを実行して、設定を local.settings.json にダウンロードし、`<APP_NAME>` を前の記事の関数アプリの名前に置き換えます。

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

場合によっては Azure アカウントにサインインする必要があります。

> [!IMPORTANT]  
> local.settings.json ファイルは、機密情報が含まれているため、決して公開されず、ソース管理から除外される必要があります。

ストレージ アカウントの接続文字列である、値 `AzureWebJobsStorage` が必要になります。 この接続を使用して、出力バインドが期待どおりに動作することを確認します。
