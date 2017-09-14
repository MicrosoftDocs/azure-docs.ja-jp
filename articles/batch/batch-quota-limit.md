---
title: "Azure Batch 用のサービスのクォータと制限 | Microsoft Docs"
description: "既定の Azure Batch のクォータ、制限、および制約と、クォータの引き上げを要求する方法について説明します"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: f3f69ed8d3a985afe07e648e7512a88b25278ced
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017


---
# <a name="batch-service-quotas-and-limits"></a>Batch サービスのクォータと制限

他の Azure サービスと同様に、Batch サービスに関連付けられている特定のリソースにも制限があります。 これらの制限の多くは、Azure によって、サブスクリプションまたはアカウント レベルで適用される既定のクォータです。 ここでは、これらの既定の設定と、クォータの引き上げを要求する方法について説明します。

Batch ワークロードの設計やスケールアップを行う際は、これらのクォータに留意してください。 たとえば、プールのコンピューティング ノード数がターゲットとして指定した数に満たない場合は、Batch アカウントのコア クォータ制限、またはサブスクリプションのリージョンの VM コア クォータの制限に達している可能性があります。

1 つの Batch アカウントで複数の Batch ワークロードを実行することも、同じサブスクリプションで異なる Azure リージョンの複数の Batch アカウントにワークロードを分散することもできます。

Batch で実稼働ワークロードを実行する予定がある場合は、1 つまたは複数のクォータについて既定値から増やすことが必要になる場合があります。 クォータを増やすには、オンライン [カスタマー サポートに申請](#increase-a-quota) (無料) してください。

> [!NOTE]
> クォータは、容量の保証ではなく、クレジット制限です。 大規模な容量が必要な場合は、Azure サポートにお問い合わせください。
> 
> 

## <a name="resource-quotas"></a>リソース クォータ
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="quotas-in-user-subscription-mode"></a>ユーザー サブスクリプション モードでのクォータ

**ユーザー サブスクリプション**がプール割り当てモードに設定された Batch アカウントでは、Batch VM とその他のリソース (ストレージ アカウントなど) は、プールの作成時に直接サブスクリプションに作成されます。 このモードで作成されたアカウントには、Azure Batch のコア クォータは適用されません。 代わりに、リージョンのコンピューティング コアとその他のリソースにはサブスクリプションのクォータが適用されます。 これらのクォータの詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。

ユーザー サブスクリプション モードで作成したアカウントのリソース使用量を計画する場合は、Linux VM 40 台、または Windows VM 20 台ごとに、次の Batch リソースが (コンピューティング コア以外に) 必要な点に注意してください。

| リソース | クォータ | プロバイダー |
| --- | ---| --- |
| 1 つのストレージ アカウント | ストレージ アカウント | Microsoft.Storage |
| 1 つのパブリック IP アドレス | パブリック IP アドレス | Microsoft.Network | 
| 1 つの仮想ネットワーク | Virtual Networks | Microsoft.Network | 
| 1 つのネットワーク セキュリティ グループ | ネットワーク セキュリティ グループ | Microsoft.Network | 
| 1 つの仮想マシン スケール セット | Virtual Machine Scale Sets | Microsoft.Compute | 
| 1 つのロード バランサー | ロード バランサー | Microsoft.Network | 

リージョン レベルまたは VM ファミリごとのコア クォータは、Batch プールに必要な VM サイズに応じて設定する必要があります。

| クォータ | プロバイダー |
| --- | ---- |
| リージョン コアの合計 | Microsoft.Compute |
| ... ファミリ コア | Microsoft.Compute |



## <a name="other-limits"></a>その他の制限
| **リソース** | **上限** |
| --- | --- |
| [同時実行タスク数](batch-parallel-node-tasks.md)  |ノードのコア数 x 4 |
| [アプリケーション](batch-application-packages.md) 数 |20 |
| アプリケーションあたりのアプリケーション パッケージ数 |40 |
| 各アプリケーション パッケージのサイズ |約 195 GB<sup>1</sup> |
| 開始タスクの最大サイズ | 32768 文字<sup>2</sup> |

<sup>1</sup> 最大ブロック BLOB サイズに関する Azure Storage の制限<br />
<sup>2</sup> リソース ファイルと環境変数を含む

## <a name="view-batch-quotas"></a>Batch クォータの確認
Batch アカウントのクォータは、[Azure Portal][portal] で確認します。

1. ポータルで **[Batch アカウント]** を選択し、目的の Batch アカウントを選択します。
2. Batch アカウントのメニュー ブレードで **[プロパティ]** を選択します。
3. **[プロパティ]** ブレードに、Batch アカウントに現在適用されているクォータが表示されます。
   
    ![Batch アカウントのクォータ][account_quotas]

ユーザー サブスクリプション モードで作成した Batch アカウントについては、Azure Portal で関連するサブスクリプションのクォータを表示します。

1. **[サブスクリプション]** を選択し、Batch アカウントに使用しているサブスクリプションを選択します。

2. **[サブスクリプション]** ブレードで、**[使用量 + クォータ]** を選択します。



## <a name="increase-a-quota"></a>クォータを増やす
次の手順を実行し、[Azure Portal][portal] を使用して、Batch アカウントまたはサブスクリプションに対するクォータの引き上げを要求します。 クォータの引き上げの種類は、Batch アカウントのプール割り当てモードによって異なります。

### <a name="increase-a-batch-cores-quota"></a>Batch のコア クォータを増やす 

Batch アカウントを **Batch サービス** モードで作成した場合は、次の手順を実行して Batch のコア クォータの引き上げを要求します。

1. ポータルのダッシュボードで **[ヘルプとサポート]** タイルを選択します。または、ポータルの右上隅にある疑問符 (**[?]**) を選択します。
2. **[新しいサポート要求]** > **[基本]** の順にクリックします。
3. **[基本]** ブレードで、次の操作を行います。
   
    a. **[問題の種類]** > **[クォータ]**
   
    b. サブスクリプションを選択します。
   
    c. **[クォータの種類]** > **バッチ**
   
    d. **[サポート プラン]** > **[Quota Support - Included (クォータのサポート - 含む)]**
   
    **[次へ]** をクリックします。
4. **[Problem (問題)]** ブレードで、次の操作を行います。
   
    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 [ビジネスへの影響][support_sev]に従って **[重要度]** を選択します。
   
    b. **[詳細]** で、変更する個々のクォータ、Batch アカウント名、および新しい制限値を指定します。
   
    **[次へ]** をクリックします。
5. **[連絡先情報]** ブレードで、次の操作を行います。
   
    a. **希望連絡方法**を選択します。
   
    b. 必要な連絡先情報を確認および入力します。
   
    **[作成]** をクリックしてサポート要求を送信します。

サポート要求を送信した後は、Azure サポートからの連絡を待ちます。 要求を完了するには最大で 2 営業日かかります。

### <a name="increase-a-subscription-cores-quota"></a>サブスクリプションのコア クォータを増やす

Batch アカウントを**ユーザー サブスクリプション** モードで作成し、リージョンまたは VM ファミリの追加のコアが必要な場合は、サブスクリプションのクォータの引き上げを要求します。 手順については、「[Azure Resource Manager のコア クォータを増やす要求](../azure-supportability/resource-manager-core-quotas-request.md)」をご覧ください。



## <a name="related-topics"></a>関連トピック
* [Azure Portal で Azure Batch アカウントを作成して管理する](batch-account-create-portal.md)
* [Azure Batch 機能の概要](batch-api-basics.md)
* [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

