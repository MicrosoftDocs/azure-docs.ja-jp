---
title: インクルード ファイル
description: インクルード ファイル
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 28d003e123069c47d87d81570b4a5b69b3b9d64b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181545"
---
1. Azure SignalR Service のリソースを作成するには、まず [Azure portal](https://portal.azure.com) にサインインします。 ページの左上にある **[+ リソースの作成]** を選択します。 **[Marketplace を検索]** テキスト ボックスに「**SignalR Service**」と入力します。

2. 結果から **[SignalR Service]** を選択し、 **[作成]** を選択します。

3. 新しい **SignalR** の設定ページで、SignalR の新しいリソースに次の設定を追加します。

    | Name | 推奨値 | 説明 |
    | ---- | ----------------- | ----------- |
    | リソース名 | *testsignalr* | SignalR リソースに使用する一意のリソース名を入力します。 名前は 1 ～ 63 文字の文字列で、数字、英字、ハイフン (`-`) 文字のみを使用する必要があります。 名前の先頭と末尾にはハイフン文字を使用できません。また、連続するハイフン文字は無効です。|
    | サブスクリプション | サブスクリプションの選択 |  SignalR のテストに使用する Azure サブスクリプションを選択します。 アカウントにサブスクリプションが 1 つしかない場合は自動的に選択されるため、 **[サブスクリプション]** ドロップダウンは表示されません。|
    | リソース グループ | *SignalRTestResources* というリソース グループを作成する| SignalR リソースのリソース グループを選択または作成します。 このグループで複数のリソースをまとめておくと、そのリソース グループを削除することで複数のリソースを同時に削除できるため、便利です。 詳細については、 [リソース グループを使用した Azure リソースの管理](../articles/azure-resource-manager/resource-group-overview.md)に関するページを参照してください。 |
    | Location | *米国東部* | **[場所]** を使用して、SignalR リソースがホストされている地理的位置を指定します。 最高のパフォーマンスを得るには、アプリケーションの他のコンポーネントと同じリージョンにリソースを作成することをお勧めします。 |
    | 価格レベル | *Free* | 現時点では、**Free** と **Standard** のオプションを利用できます。 |
    | [ダッシュボードにピン留めする] | ✔ | リソースが探しやすくなるようにダッシュボードにピン留めするには、このボックスをオンにします。 |

4. **作成** を選択します。 デプロイが完了するまでに数分かかることがあります。

5. デプロイが完了したら、 **[設定]** の下の **[キー]** を選択します。 主キーの接続文字列をコピーします。 この文字列は、後でお使いのアプリがその Azure SignalR Service のリソースを使用するように構成するために使用します。

    接続文字列は、次の形式になります。
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
