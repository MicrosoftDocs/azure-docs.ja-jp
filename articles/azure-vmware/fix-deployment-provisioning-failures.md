---
title: Azure VMware Solution のデプロイまたはプロビジョニングのエラーに関するヘルプを表示する
description: Azure VMware Solution のプライベート クラウドから必要な情報を取得して、Azure VMware Solution のデプロイまたはプロビジョニングのエラーに対するサービス要求をファイルに取り込む方法について説明します。
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 63d5440a9e2b15463e465e1d32762889508feca1
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752241"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Azure VMware Solution のデプロイまたはプロビジョニングのエラーに関するヘルプを表示する

この記事では、Azure portal でサービス要求 (SR) を開き、プライベート クラウドでの Azure VMware Solution のデプロイまたはプロビジョニングのエラーに関する支援を受ける方法について説明します。 ただし、まず、Azure portal でいくつかの重要な情報を収集する必要があります。 ほとんどの場合、次のものが必要です。

- 関連付け ID (失敗したデプロイのもの)
- ExpressRoute 回線 ID (プライベート クラウドの ExpressRoute 回線を使用して既存のプライベート クラウドをスケーリングまたはピアリングしようとして失敗した場合)

## <a name="collect-the-correlation-id"></a>関連付け ID の収集
 
まず、関連付け ID を見てみましょう。 プライベート クラウド (または Azure 内の任意のリソース) を作成すると、関連付けられた関連付け ID が生成されます。 各 Azure Resource Manager デプロイでも一意の関連付け ID が生成されます。 この ID により、迅速な SR 作成と解決が可能になります。 
 
失敗したプライベート クラウドのデプロイからの出力の例を次に示します。関連付け ID は強調表示されています。

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="関連付け ID が表示されている失敗したプライベート クラウドのデプロイ。":::

サービス要求に含めるため、この関連付け ID をコピーして保存します。 詳細については、この記事の最後にある「[サポート リクエストの作成](#create-your-support-request)」を参照してください。

プライベート クラウドをデプロイする前に事前検証段階でエラーが発生した場合、関連付け ID は生成されません。 この場合は、次のような、Azure VMware Solution のプライベート クラウドの作成時に使用した情報を入力するだけで済みます。

- 場所
- Resource group
- リソース名
 
### <a name="collect-a-summary-of-errors"></a>エラーの概要の収集

エラーの詳細は、問題の解決にも役立ちます。 上の画面から、次のスクリーンショットに示すように、 **[詳細はこちらをクリック]** (強調表示) を選択し、エラーの概要を表示します。
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="エラーの概要。":::

ここでも、SR に含めるため、この概要をコピーして保存します。
 
### <a name="retrieve-past-deployments"></a>過去のデプロイを取得する

失敗したデプロイを含む過去のデプロイを取得するには、[通知] アイコンを選択してアクセスする、デプロイ アクティビティ ログを検索します。

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="通知を開きます。":::

[通知] で、 **[アクティビティ ログのその他のイベント]** を選択します。

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="リンク:アクティビティ ログのその他のイベント。":::

次に、リソースの名前、またはリソースの作成で使用した他の一意の情報を検索して、失敗したデプロイとその関連付け ID を検索します。 次の例では、プライベート クラウド リソース (pc03) での検索結果を示します。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="過去に失敗した Azure VMware Solution のデプロイを検索します。":::
 
失敗したデプロイの操作名を選択すると、ウィンドウが開き、詳細が表示されます。 [JSON] タブを選択し、[correlationId] を探します。 コピーして SR に含めます。 
 
## <a name="collect-the-expressroute-id-uri"></a>ExpressRoute ID (URI) の収集
 
プライベート クラウドが既にあり、プライベート クラウドの ExpressRoute 回線を使用してスケーリングまたはピアリングしようとしたときにエラーが発生したとします。 その場合、SR を作成するときに、プライベート クラウドの ExpressRoute ID を使用してそれを識別できます。

ポータルでプライベート クラウドを表示する場合は、 **[接続性] > [ExpressRoute]** を選択し、 **[ExpressRoute ID]** をクリップボードにコピーします。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="ExpressRoute ID をクリップボードにコピーします。"::: 
 
ExpressRoute ID を新しいサポート リクエストの適切なフィールドに貼り付けます。 詳細については、次のセクション、「[サポート リクエストの作成](#create-your-support-request)」を参照してください。
 
> [!NOTE]
> 場合によっては、事前検証チェックがデプロイ前に失敗する可能性があり、この場合、使用可能な情報はエラーやエラー メッセージだけになります。 これらは、クォータ関連の問題など、さまざまなエラーにおいて役立つため、サポート リクエストにこれらのメッセージを含めることが重要です。 これらの情報を収集するには、前のセクション「[エラーの概要の収集](#collect-a-summary-of-errors)」を参照してください。

## <a name="create-your-support-request"></a>サポート リクエストの作成

サポート リクエストを作成するための一般的なガイダンスについては、[Azure サポート リクエストを作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)に関する記事を参照してください。 

ここでは、Azure VMware Solution のデプロイまたはプロビジョニング エラーのための SR の作成に固有の追加のガイダンスを示します。

1. **[ヘルプ]** アイコン、 **[+ 新しいサポート リクエスト]** の順に選択します。

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="SR の ExpressRoute ID を収集します。":::

2. すべての必須フィールドに入力し、 **[基本]** タブで次の操作を行います。

    - **[問題の種類]** には、 **[構成とセットアップに関する問題]** を選択してください。

    - **[問題のサブタイプ]** には、 **[プライベート クラウドのプロビジョニング]** を選択します。

3. **[詳細]** タブでは:

    - すべての必須フィールドに必要事項を入力します。

    - 指定された特定のフィールドに関連付け ID または ExpressRoute ID を貼り付けます。 これら専用のフィールドが表示されない場合は、 **[問題に関する詳細情報を提供してください]** の下のテキスト ボックスに貼り付けます。

    - エラーの詳細 (コピーしたエラーの概要を含む) を、 **[問題に関する詳細情報を提供してください]** の下のテキスト ボックスに貼り付けます。

4. 確認してから **[作成]** を選択して SR を作成します。
