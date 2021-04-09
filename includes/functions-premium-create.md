---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2020
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: cdeba09c91bee72508632b8468126150017df7cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98186979"
---
1. Azure portal メニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。

1. **[新規]** ページで、 **[計算]**  > 、 **[関数アプリ]** の順に選択します。

1. **[基本]** ページで、下の表に指定されている関数アプリの設定を使用します。

    | 設定      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **サブスクリプション** | 該当するサブスクリプション | この新しい Function App が作成されるサブスクリプション。 |
    | **[リソース グループ](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Function App を作成するための新しいリソース グループの名前。 |
    | **関数アプリ名** | グローバルに一意の名前 | 新しい Function App を識別する名前。 有効な文字は、`a-z` (大文字と小文字の区別をしない)、`0-9`、および `-`です。  |
    |**発行**| コード | コード ファイルまたは Docker コンテナーの発行オプション。 |
    | **ランタイム スタック** | 優先言語 | お気に入りの関数プログラミング言語をサポートするランタイムを選択します。 ポータルでの編集は現在、[Python 開発](../articles/azure-functions/functions-reference-python.md)ではサポートされません。|
    |**リージョン**| 優先リージョン | ユーザーに近い[リージョン](https://azure.microsoft.com/regions/)、または関数がアクセスする他のサービスの近くのリージョンを選択します。 |

1. **[Next:ホスティング]** を選択します。 **[ホスティング]** ページで、次の設定を入力します。

    | 設定      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **[ストレージ アカウント](../articles/storage/common/storage-account-create.md)** |  グローバルに一意の名前 |  Function App で使用されるストレージ アカウントを作成します。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 既存のアカウントを使用することもできますが、[ストレージ アカウントの要件](../articles/azure-functions/storage-considerations.md#storage-account-requirements)を満たしている必要があります。 |
    |**オペレーティング システム**| 優先オペレーティング システム | オペレーティング システムは、ランタイム スタックの選択に基づいてあらかじめ選択されますが、必要に応じて設定を変更できます。 Python は Linux でのみサポートされています。 ポータルでの編集は Windows でのみサポートされます。|
    | **[プラン](../articles/azure-functions/functions-scale.md)** | Premium | Function App にどのようにリソースが割り当てられるかを定義するホスティング プラン。 **[Premium]** を選択します。 既定では、新しい App Service プランが作成されます。 既定の **SKU とサイズ** は **EP1** です。ここで、EP は "_エラスティック Premium_" を表します。 詳細については、[Premium SKU の一覧](../articles/azure-functions/functions-premium-plan.md#available-instance-skus)を参照してください。<br/>Premium プランで JavaScript 関数を実行する場合は、vCPU の少ないインスタンスを選ぶ必要があります。 詳しくは、[シングルコア Premium プランの選択](../articles/azure-functions/functions-reference-node.md#considerations-for-javascript-functions)に関する記事をご覧ください。  |

1. **[Next:監視]** を選択します。 **[監視]** ページで、次の設定を入力します。

    | 設定      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Default | 最も近いサポートされているリージョン内に同じ *アプリ名* の Application Insights リソースを作成します。 この設定を展開することによって、 **[新しいリソース名]** を変更するか、データを格納する [Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)内の別の **場所** を選択することができます。 |

1. **[確認および作成]** を選択して、アプリ構成の選択内容を確認します。

1. **[確認および作成]** ページで設定を確認して、 **[作成]** を選択し、関数アプリをプロビジョニングしてデプロイします。

1. ポータルの右上隅の **[通知]** アイコンを選択し、"**デプロイメントに成功しました**" というメッセージが表示されるまで待ちます。

1. **[リソースに移動]** を選択して、新しい Function App を確認します。 また、 **[ダッシュボードにピン留めする]** を選択することもできます。 ピン留めすると、ダッシュボードからこの関数アプリ リソースに戻るのが容易になります。

    ![デプロイの通知](./media/functions-premium-create/function-app-create-notification2.png)
