---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: codemillmatt
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: masoucou
ms.custom: include file
ms.openlocfilehash: 02586d38903c60ba8982753ca0bd3e15192d5deb
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006378"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Azure Portal で資格情報をコピーする

サンプル アプリケーションから Azure Storage に対して要求を実行するときは、承認されている必要があります。 要求を承認するには、ストレージ アカウントの資格情報を接続文字列としてアプリケーションに追加します。 次の手順に従って、ストレージ アカウントの資格情報を表示します。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 自分のストレージ アカウントを探します。
3. ストレージ アカウントの概要の **[設定]** セクションで、 **[アクセス キー]** を選択します。 ここでは、アカウント アクセス キーと各キーの完全な接続文字列を表示できます。
4. **[Key1]** の **[接続文字列]** の値を見つけ、 **[コピー]** ボタンを選択して接続文字列をコピーします。 すぐ後の手順で、接続文字列の値を環境変数に追加します。

    ![Azure portal から接続文字列をコピーする方法を示すスクリーンショット](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成

接続文字列をコピーしたら、*MainPage.xaml.cs* ファイル内のクラス レベル変数に設定します。 *MainPaage.xaml.cs* を開き、`storageConnectionString` 変数を見つけます。 `<yourconnectionstring>` は、実際の接続文字列に置き換えてください。

コードは次のとおりです。

```csharp
string storageConnectionString = "<yourconnectionstring>";
```