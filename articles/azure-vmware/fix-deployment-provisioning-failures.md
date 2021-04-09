---
title: Azure VMware Solution のデプロイまたはプロビジョニングのエラーに関するサポート
description: Azure VMware Solution のプライベート クラウドから情報を取得して、Azure VMware Solution のデプロイまたはプロビジョニングのエラーに対するサービス要求を提出します。
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 27b645f4ca225fdd74bca6499b6581b3803e41a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542407"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Azure VMware Solution のデプロイまたはプロビジョニングのエラーに関するサポート リクエストを開く

この記事では、[サポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)を開き、Azure VMware ソリューションのデプロイまたはプロビジョニングのエラーに関する重要な情報を提供する方法について説明します。 

プライベート クラウド上でエラーが発生したときは、Azure portal でサポート リクエストを開く必要があります。 サポート リクエストを開くには、まず Azure portal でいくつかの重要な情報を取得します。

- 関連付け ID
- Azure ExpressRoute 回線 ID
- エラー メッセージ

## <a name="get-the-correlation-id"></a>関連付け ID を取得する
 
Azure でプライベート クラウドまたは任意のリソースを作成すると、そのリソースに対して、そのリソースの関連付け ID が自動的に生成されます。 より迅速にリクエストを開き解決するために、サポート リクエストにプライベート クラウドの関連付け ID を含めます。

Azure portal では、2 つの方法でリソースの関連付け ID を取得できます。

* **[概要]** ペイン
* デプロイ ログ
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>リソースの概要から関連付け ID を取得する

次に関連付け ID が選択された、失敗したプライベート クラウドのデプロイの操作の詳細の例を示します。

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="関連付け ID が選択された、失敗したプライベート クラウドのデプロイを示すスクリーンショット。":::

プライベート クラウドの **[概要]** ペインのデプロイ結果にアクセスするには、次のようにします。

1. Azure portal で、対象のプライベート クラウドを選択します。

1. 左側のメニューで、 **[概要]** を選択します。

デプロイが開始されると、そのデプロイの結果がプライベート クラウドの **[概要]** ペインに表示されます。

サービス要求に含めるために、プライベート クラウドのデプロイの関連付け ID をコピーして保存します。

### <a name="get-the-correlation-id-from-the-deployment-log"></a>デプロイ ログから関連付け ID を取得する

Azure portal でデプロイのアクティビティ ログを検索することで、失敗したデプロイの関連付け ID を取得できます。

デプロイ ログにアクセスするには、次のようにします。

1. Azure portal で、対象のプライベート クラウドを選択し、通知アイコンを選択します。

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Azure portal の通知アイコンを示すスクリーンショット。":::

1. **[通知]** ペインで、 **[アクティビティ ログのその他のイベント]** を選択します。

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="[通知] ペインで [アクティビティ ログのその他のイベント] リンクが選択されていることを示すスクリーンショット。":::

1. 失敗したデプロイとその関連付け ID を探すには、リソースの名前またはリソースの作成に使用したその他の情報を検索します。 

    次の例は、pc03 という名前のプライベート クラウド リソースでの検索結果を示します。
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="プライベート クラウド リソースの例の検索結果と、[Create or update a PrivateCloud]\(PrivateCloud の作成または更新\) ペインを示すスクリーンショット。":::
 
1. **[アクティビティ ログ]** ペインの検索結果で、失敗したデプロイの操作名を選択します。

1. **[Create or update a PrivateCloud]\(PrivateCloud の作成または更新\)** ペインで、 **[JSON]** タブを選択し、表示されるログで `correlationId` を探します。 `correlationId` の値をコピーして、それをサポート リクエストに含めます。 
 
## <a name="copy-error-messages"></a>エラー メッセージをコピーする

デプロイの問題の解決を支援するために、Azure portal に表示されているエラー メッセージをすべて含めます。 警告メッセージを選択して、エラーの概要を確認します。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="[エラー] ペインの [概要] タブにあるエラーの詳細を示すスクリーンショット。コピー アイコンが選択されています。":::

エラー メッセージをコピーするには、コピー アイコンを選択します。 コピーしたメッセージを保存し、サポート リクエストに含めます。
 
## <a name="get-the-expressroute-id-uri"></a>ExpressRoute ID (URI) を取得する
 
おそらく、プライベート クラウド ExpressRoute 回線を使用して既存のプライベート クラウドをスケーリングまたはピアリングしようとして失敗しています。 このシナリオでは、ExpressRoute ID をサポート リクエストに含める必要があります。

ExpressRoute ID をコピーするには、次のようにします。

1. Azure portal で、対象のプライベート クラウドを選択します。
1. 左側のメニューの **[管理]** で、 **[接続]** を選択します。 
1. 右側のペインで **[ExpressRoute]** タブを選択します。
1. **ExpressRoute ID** のコピー アイコンを選択し、その値を保存してサポート リクエストで使用します。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="ExpressRoute ID をクリップボードにコピーします。"::: 
 
## <a name="pre-validation-failures"></a>事前検証エラー

(デプロイ前の) プライベート クラウドの事前検証チェックに失敗した場合、関連付け ID は生成されません。 このシナリオでは、サポート リクエストに次の情報を提供できます。

- エラーと失敗のメッセージ。 これらのメッセージは、クォータ関連の問題など、多くのエラーで役に立ちます。 この記事で説明されているように、これらのメッセージをコピーし、サポート リクエストに含めることが重要です。
- Azure VMware Solution プライベート クラウドの作成に使用した情報。
  - 場所
  - Resource group
  - リソース名

## <a name="create-your-support-request"></a>サポート リクエストの作成

サポート リクエストの作成に関する一般的な情報については、[Azure サポート リクエストを作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)に関する記事を参照してください。 

Azure VMware Solution のデプロイまたはプロビジョニングのエラーに関するサポート リクエストを作成するには、次のようにします。

1. Azure portal で、 **[ヘルプ]** アイコンを選択し、 **[新しいサポート リクエスト]** を選択します。

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Azure portal の [新しいサポート リクエスト] ペインのスクリーンショット。":::

1. 必要な情報を入力または選択します。

   1. **[基本]** タブで、次の操作を行います。

      1. **[問題の種類]** には、 **[構成とセットアップに関する問題]** を選択してください。

      1. **[問題のサブタイプ]** には、 **[プライベート クラウドのプロビジョニング]** を選択します。

   1. **[詳細]** タブでは:

      1. 必要な情報を入力または選択します。

      1. この情報がリクエストされた場所に、関連付け ID または ExpressRoute ID を貼り付けます。 これらの値のための特定のテキスト ボックスが表示されない場合は、 **[問題に関する詳細情報を提供してください]** テキスト ボックスに貼り付けます。

    1. エラーの詳細 (コピーしたエラーまたは失敗のメッセージなど) を、 **[問題に関する詳細情報を提供してください]** テキスト ボックスに貼り付けます。

1. 入力内容を見直してから、 **[作成]** を選択してサポート リクエストを作成します。
