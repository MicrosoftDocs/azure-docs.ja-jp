---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 460199c1958e3bb22148624b9f013eadbb707bd2
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69564391"
---
1. Azure portal の左上にある **[リソースの作成]** ボタンを選択し、 **[コンピューティング]**  >  **[Function App]** を選択します。

    ![Azure ポータルでの関数アプリの作成](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. 図の下にある表に指定されている関数アプリの設定を使用します。

    ![新しい関数アプリの設定を定義する](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Setting      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **アプリ名** | グローバルに一意の名前 | 新しい Function App を識別する名前。 有効な文字は、`a-z`、`0-9`、および `-` です。  | 
    | **サブスクリプション** | 該当するサブスクリプション | この新しい Function App が作成されるサブスクリプション。 | 
    | **[リソース グループ](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Function App を作成するための新しいリソース グループの名前。 |
    | **OS** | Windows | 利用できる言語オプションは、関数アプリの OS に依存します。 たとえば、Python では Linux が必要です。 |
    | **[ホスティング プラン](../articles/azure-functions/functions-scale.md)** | 従量課金プラン | Function App にどのようにリソースが割り当てられるかを定義するホスティング プラン。 既定の **[従量課金プラン]** では、リソースは関数の必要に応じて動的に追加されます。 この[サーバーなしの](https://azure.microsoft.com/overview/serverless-computing/) ホスティングでは、関数が実行された時間にのみ課金されます。 App Service プランで実行する場合は、[関数アプリのスケーリング](../articles/azure-functions/functions-scale.md)を管理する必要があります。  |
    | **Location** | 西ヨーロッパ | ユーザーに近い[リージョン](https://azure.microsoft.com/regions/)、または関数がアクセスする他のサービスの近くのリージョンを選択します。 |
    | **ランタイム スタック** | 優先言語 | お気に入りの関数プログラミング言語をサポートするランタイムを選択します。 C# および F# 関数用の **[.NET]** を選択します。 |
    | **[Storage](../articles/storage/common/storage-quickstart-create-account.md)** |  グローバルに一意の名前 |  関数アプリで使用されるストレージ アカウントを作成します。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 既存のアカウントを使用することもできますが、[ストレージ アカウントの要件](../articles/azure-functions/functions-scale.md#storage-account-requirements)を満たしている必要があります。 |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | 既定値 | 最も近いサポートされているリージョン内に同じ*アプリ名*の Application Insights リソースを作成します。 この設定を展開することによって、 **[新しいリソース名]** を変更するか、またはデータを格納する [Azure 地理的環境](https://azure.microsoft.com/global-infrastructure/geographies/)内の別の **[場所]** を選択することができます。 |

3. **[作成]** を選択して、Function App をプロビジョニングし、デプロイします。

4. ポータルの右上隅の通知アイコンを選択し、"**デプロイメントに成功しました**" というメッセージが表示されるまで待ちます。

    ![新しい関数アプリの設定を定義する](./media/functions-create-function-app-portal/function-app-create-notification.png)

5. **[リソースに移動]** を選択して、新しい関数アプリを確認します。 また、 **[ダッシュボードにピン留めする]** を選択することもできます。 ピン留めすると、ダッシュボードからこの関数アプリ リソースに戻るのが容易になります。
