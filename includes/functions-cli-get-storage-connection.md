---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673355"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Azure Storage の接続文字列を取得する

先ほど、関数アプリで使用する Azure ストレージ アカウントを作成しました。 このアカウントの接続文字列は、Azure のアプリ設定に安全に格納されています。 関数をローカルで実行するときは、*local.settings.json* ファイルに設定をダウンロードすることにより、その接続を使用して同じアカウントのストレージ キューへの書き込みを行うことができます。 

1. プロジェクトのルートから、次のコマンドを実行します。`<app_name>` は、前のクイックスタートの関数アプリ名に置き換えてください。 このコマンドを実行すると、ファイル内の既存の値はすべて上書きされます。

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. *local.settings.json* を開き、`AzureWebJobsStorage` という名前の値を検索します。それがストレージ アカウントの接続文字列です。 `AzureWebJobsStorage` という名前と接続文字列は、この記事の他のセクションで使用します。

> [!IMPORTANT]
> *local.settings.json* には、Azure からダウンロードされたシークレットが含まれているため、このファイルは必ずソース管理から除外してください。 ローカル関数プロジェクトで作成される *.gitignore* ファイルからは、このファイルが既定で除外されます。