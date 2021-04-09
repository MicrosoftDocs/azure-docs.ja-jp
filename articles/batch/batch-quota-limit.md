---
title: サービスのクォータと制限
description: 既定の Azure Batch のクォータ、制限、および制約と、クォータの引き上げを要求する方法について説明します
ms.topic: conceptual
ms.date: 01/28/2021
ms.custom: seodec18
ms.openlocfilehash: 433272c76b9ff27d9cad542cf65a8ec0d8fc0378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99052382"
---
# <a name="batch-service-quotas-and-limits"></a>Batch サービスのクォータと制限

他の Azure サービスと同様に、Batch サービスに関連付けられている特定のリソースにも制限があります。 これらの制限の多くは、Azure によって、サブスクリプションまたはアカウント レベルで適用される既定のクォータです。

Batch ワークロードの設計やスケールアップを行う際は、これらのクォータに留意してください。 たとえば、プールのコンピューティング ノード数がターゲットとして指定した数に満たない場合は、Batch アカウントのコア クォータ制限に達している可能性があります。

1 つの Batch アカウントで複数の Batch ワークロードを実行することも、同じサブスクリプションで異なる Azure リージョンの複数の Batch アカウントにワークロードを分散することもできます。

Batch で実稼働ワークロードを実行する予定がある場合は、1 つまたは複数のクォータについて既定値から増やすことが必要になる場合があります。 クォータを増やすには、オンライン [カスタマー サポートに申請](#increase-a-quota) (無料) してください。

## <a name="resource-quotas"></a>リソース クォータ

クォータは、容量の保証ではなく、制限です。 大規模な容量が必要な場合は、Azure サポートにお問い合わせください。

また、クォータは保証された値でないことに注意してください。 クォータは、Batch サービスからの変更や、クォータ値を変更するユーザー要求によって異なる場合があります。

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>コア クォータ

### <a name="cores-quotas-in-batch-service-mode"></a>Batch サービス モードでのコア クォータ

コア クォータは、Batch でサポートされる各 VM シリーズに存在し、ポータルの **クォータ** ページに表示されます。 次に説明するように、VM シリーズのクォータ制限はサポート要求で更新できます。 専用ノードの場合、Batch では、各 VM シリーズに対してコア クォータ制限が適用されます。また、Batch アカウント全体に対して合計のコア クォータ制限も適用されます。 優先順位の低いノードの場合、Batch では、異なる VM シリーズを区別せずに、Batch アカウントに対して合計のコア クォータのみ適用されます。

### <a name="cores-quotas-in-user-subscription-mode"></a>ユーザー サブスクリプション モードでのコア クォータ

プール割り当てモードを **ユーザー サブスクリプション** に設定して [Batch アカウント](accounts.md)を作成した場合、Batch VM とその他のリソースは、プールの作成時またはサイズ変更時に直接サブスクリプションに作成されます。 Azure Batch コア クォータは適用されず、リージョンのコンピューティング コア、シリーズごとのコンピューティング コア、およびその他のリソースに対するサブスクリプションのクォータが使用され、適用されます。

これらのクォータの詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。

## <a name="pool-size-limits"></a>プール サイズの制限

プール サイズの制限は、Batch サービスによって設定されます。 [リソース クォータ](#resource-quotas)と異なり、これらの値は変更できません。 ノード間通信とカスタム イメージを使用するプールのみが、標準クォータとは異なる制限を持ちます。

| **リソース** | **上限** |
| --- | --- |
| **[ノード間通信に対応するプール](batch-mpi.md)内の計算ノード**  ||
| バッチ サービス プール割り当てモード | 100 |
| バッチ サブスクリプション プール割り当てモード | 80 |
| **マネージド イメージ リソースで作成されたプール [内の計算ノード](batch-custom-images.md)** <sup>1</sup> ||
| 専用ノード | 2000 |
| 優先順位の低いノード | 1000 |

<sup>1</sup> ノード間通信が有効になっていないプール用。

## <a name="other-limits"></a>その他の制限

これらの追加制限は Batch サービスによって設定されます。 [リソース クォータ](#resource-quotas)と異なり、これらの値は変更できません。

| **リソース** | **上限** |
| --- | --- |
| [同時実行タスク数](batch-parallel-node-tasks.md) | ノードのコア数 x 4 |
| [アプリケーション](batch-application-packages.md) 数 | 20 |
| アプリケーションあたりのアプリケーション パッケージ数 | 40 |
| プールあたりのアプリケーション パッケージ数 | 10 |
| タスクの最長有効期間 | 180 日<sup>1</sup> |
| コンピューティング ノードあたりの[マウント](virtual-file-mount.md)数 | 10 |
| プールあたりの証明書数 | 12 |

<sup>1</sup> タスクの最長有効期間 (ジョブに追加されてから完了するまで) は、180 日間です。 完了したタスクは 7 日間保持されます。最長有効期間内に完了しなかったタスクのデータにはアクセスできません。

## <a name="view-batch-quotas"></a>Batch クォータの確認

Batch アカウントのクォータを [Azure portal](https://portal.azure.com) で確認するには、次のようにします。

1. **[Batch アカウント]** を選択し、目的の Batch アカウントを選択します。
1. Batch アカウントのメニューで **[クォータ]** を選びます。
1. Batch アカウントに現在適用されているクォータを確認します。

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Azure portal の Batch アカウント クォータを示すスクリーンショット。":::

## <a name="increase-a-quota"></a>クォータを増やす

[Azure portal](https://portal.azure.com) を使用して、Batch アカウントまたはサブスクリプションに対するクォータの引き上げを要求できます。 クォータの引き上げの種類は、Batch アカウントのプール割り当てモードによって異なります。 クォータの増加を要求するには、クォータを増やしたい VM シリーズを含める必要があります。 クォータの増加が適用されると、すべてのシリーズの VM に適用されます。

1. ポータルのダッシュボードで **[ヘルプとサポート]** タイルを選択します。または、ポータルの右上隅にある疑問符 ( **[?]** ) を選択します。
1. **[新しいサポート要求]**  >  **[基本]** の順にクリックします。
1. **[基本]** で次のようにします。

    1. **[問題の種類]**  >  **[サービスとサブスクリプションの制限 (クォータ)]**

    1. サブスクリプションを選択します。

    1. **[クォータの種類]**  > **バッチ**

       **[次へ]** を選択します。

1. **[Details (詳細)]** には次の項目があります。

    1. **[詳細の指定]** で、場所、クォータの種類、Batch アカウントを指定します。

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="クォータ増加を要求したときのクォータ詳細画面のスクリーンショット。":::

       クォータの種類には次のものが含まれます。

       * **Batch アカウントあたり**  
         1 つの Batch アカウントに固有の値。専用コアと優先順位の低いコア、およびジョブとプールの数を含みます。

       * **リージョンあたり**  
         リージョン内のすべての Batch アカウントに適用される値。各サブスクリプションのリージョンごとの Batch アカウント数を含みます。

       優先順位の低いクォータは、すべての VM シリーズ全体で 1 つの値です。 制約付き SKU が必要な場合は、 **[優先順位の低いコア]** を選択して、要求する VM ファミリを含める必要があります。

    1. [ビジネスへの影響](https://aka.ms/supportseverity)に従って **[重要度]** を選択します。

       **[次へ]** を選択します。

1. **[連絡先情報]** で次のようにします。

    1. **希望連絡方法** を選択します。

    1. 必要な連絡先情報を確認および入力します。

       **[作成]** を選択してサポート リクエストを送信します。

サポート要求を送信した後は、Azure サポートからの連絡を待ちます。 クォータ要求の処理が完了するには、数分または最大で 2 営業日かかる場合があります。

## <a name="related-quotas-for-vm-pools"></a>VM プールの関連クォータ

Azure 仮想ネットワークにデプロイされている仮想マシンの構成の Batch プールによって、追加の Azure ネットワーク リソースが自動的に割り当てられます。 仮想ネットワークでは、50 プール ノードごとに次のリソースが必要になります。

- 1 つの[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#network-security-groups)
- 1 つの[パブリック IP アドレス](../virtual-network/public-ip-addresses.md)
- 1 つの[ロード バランサー](../load-balancer/load-balancer-overview.md)

これらのリソースは、Batch プールの作成時に提供される仮想ネットワークを含むサブスクリプションで割り当てられます。 これらのリソースは、サブスクリプションの[リソース クォータ](../azure-resource-manager/management/azure-subscription-service-limits.md)によって制限されます。 仮想ネットワークで大規模なプール デプロイを計画している場合、これらのサブスクリプションのクォータを確認してください。 必要に応じて、 **[ヘルプとサポート]** を選択し、Azure portal で増加を要請してください。

## <a name="next-steps"></a>次のステップ

* [Azure portal を使用して Azure Batch アカウントを作成します](batch-account-create-portal.md)。
* [Batch サービスのワークフローと主要なリソース](batch-service-workflow-features.md) (プール、ノード、ジョブ、タスクなど) について学習します。
* [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)について学習します。

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png