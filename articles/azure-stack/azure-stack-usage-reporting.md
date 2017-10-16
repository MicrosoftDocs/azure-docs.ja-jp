---
title: "Azure Stack 使用状況データの Azure への報告 | Microsoft Docs"
description: "Azure Stack で使用状況データ レポートを設定する方法について説明します。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: sngun;AlfredoPizzirani
ms.openlocfilehash: 695b93a818d3c0e615bb79e0a2861e134b2f5c89
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Azure Stack 使用状況データの Azure への報告 

使用状況データ (使用量データとも呼ばれる) は、使用されたリソースの量を表します。 使用量ベースの課金モデルを採用する Azure Stack 統合システムは、使用状況データを Azure に報告する必要があります。 この使用状況データは、課金目的で使用されます。 Azure Stack のオペレーターは、使用状況データを報告するように Azure Stack の環境を構成する必要があります。


> [!NOTE]
> 使用状況データの報告は、従量課金モデルのライセンスを持つ Azure Stack 統合システムのユーザーにとっては必須事項です。 一方、容量モデルのライセンスを持つユーザーはこの報告を省略できます。 Azure Stack Development Kit の場合、クラウド オペレーターは使用状況データを報告してその機能をテストできますが、ユーザーは自分が使用した分の料金を請求されることはありません。 Azure Stack での価格の詳細については、「[Azure Stack の購入方法](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)」のページをご覧ください。

## <a name="usage-data-reporting-flow"></a>使用状況データの報告フロー

使用状況データは、Azure ブリッジ経由で Azure Stack から Azure に送信されます。 Azure では、コマース システムが使用状況データを処理し、課金を生成します。 課金が生成された後、Azure サブスクリプションの所有者はそれを表示したり、[Azure アカウント センター](https://account.windowsazure.com/Subscriptions)からダウンロードしたりできます。 Azure Stack がどのようにライセンス供与されるかについては、「[Azure Stack packaging and pricing document (Azure Stack のパッケージ化および価格設定ドキュメント)](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409)」を参照してください。 

次の図は、Azure Stack における使用状況データのフローを表しています。

![課金のフロー](media/azure-stack-usage-reporting/billing-flow.png)

## <a name="set-up-usage-data-reporting"></a>使用状況データ レポートの設定

Azure Stack で使用状況データ レポートを設定するには、[Azure Stack インスタンスを Azure に登録する](azure-stack-register.md)必要があります。 登録プロセスの一部として、Azure Stack の Azure ブリッジ コンポーネントは、Azure Stack を Azure に接続するよう構成されます。 Azure ブリッジが構成されると、次の使用状況データが Azure に送信されます。 

* **メーター ID** – 使用されたリソースの一意の ID。
* **数量** – リソース使用状況データの量。
* **場所** – 現在の Azure Stack リソースがデプロイされている場所。
* **リソース URI** – 使用状況が報告されているリソースの完全修飾 URI。 
* **サブスクリプション ID** – Azure Stack ユーザーのサブスクリプション ID。
* **時間** – 使用状況データの開始および終了時間。 これらのリソースが Azure Stack で使用されるときと、使用状況データがコマースに報告されるときとの間には、ある程度の時間のずれがあります。 Azure Stack は 24 時間ごとに使用状況データを集計し、その使用状況データを Azure のコマース パイプラインに報告するにはさらに数時間かかります。 そのため、午前 0 時の少し前に発生した使用状況は翌日 Azure に表示される可能性があります。

## <a name="test-usage-data-reporting"></a>使用状況データ レポートのテスト 

1. 使用状況データ レポートをテストするには、Azure Stack でいくつかのリソースを作成します。 たとえば、コアの使用状況がどのように報告されるかを確認するには、Basic および Standard SKU で[ストレージ アカウント](azure-stack-provision-storage-account.md)、[Windows Server VM](azure-stack-provision-vm.md)、および Linux VM を作成できます。 異なる種類のリソースの使用状況データは、別のメーターのもとに報告されます。  

2. リソースを数時間実行されたままにします。 使用状況の情報は、1 時間ごとに約 1 回収集されます。 収集の後、このデータは Azure に送信され、Azure コマース システムに処理されます。 このプロセスは、最大数時間かかることがあります。  

3. Azure アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインし、Azure Stack を登録するために使用される Azure サブスクリプションを選択します。 次の図に示すように、Azure Stack 使用状況データ (使用された各リソースの課金される量) を表示できます。  

   ![課金のフロー](media/azure-stack-usage-reporting/pricing-details.png)

Azure Stack Development Kit の場合、リソースは課金されないため、価格は $0.00 として示されます。 Azure Stack 統合システムの場合、これらの各リソースの実際のコストが表示されます。

## <a name="are-users-charged-for-the-infrastructure-virtual-machines"></a>ユーザーはインフラストラクチャ仮想マシンを使用すると課金されますか?
いいえ。一部の Azure Stack リソースプロバイダーの、デプロイ中に作成される仮想マシンとインフラストラクチャ仮想マシンの使用状況データは Azure に報告されますが、ユーザーには課金されません。 

ユーザーは、ユーザー サブスクリプションのもとで作成する仮想マシンについてのみ課金されます。 そのため、Azure Stack のライセンス条項に従い、すべてのワークロードはユーザー サブスクリプションのもとでデプロイする必要があります。

## <a name="how-do-i-use-my-existing-windows-server-licenses-in-azure-stack"></a>Azure Stack で既存のWindows Server ライセンスを使用する方法 
既存の Windows Server ライセンスは Azure Stack で使用できます。このモデルは BYOL (ライセンス持ち込み) と呼ばれます。 既に所有しているライセンスを使用すれば、追加の使用状況メーターを生成せずに済みます。 既存のライセンスを使用するには、「[Windows Server 向け Azure Hybrid Use Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md)」のトピックに記載されているように、Windows Server 仮想マシンをデプロイする必要があります。 

## <a name="what-azure-meters-are-used-when-reporting-usage-data-in-integrated-systems"></a>統合システムで使用状況データを報告するときはどのような Azure メーターが使用されますか?
* **定価メーター** – ユーザー ワークロードに関連付けられたリソースに使用されます。  
* **管理メーター** – インフラストラクチャ リソースに使用されます。 これらのメーターの価格は 0 ドルです。

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>使用されたリソースに対してどのサブスクリプションが課金されますか?
[Azure Stack を Azure に登録する](azure-stack-register.md)ときに指定されたサブスクリプションが課金されます。

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>使用状況データ レポートでは、どのような種類のサブスクリプションがサポートされますか?
Azure Stack 統合システムの場合、Enterprise Agreement (EA) と CSP サブスクリプションがサポートされます。 

Azure Stack Development Kit の場合、Enterprise Agreement (EA)、従量課金、CSP、MSDN サブスクリプションで、使用状況データの報告がサポートされます。

## <a name="which-sovereign-clouds-support-usage-data-reporting"></a>使用状況データの報告はどのソブリン クラウドでサポートされますか?
Azure Stack Development Kit で使用状況データを報告するには、グローバルな Azure システムで作成されたサブスクリプションが必要です。 ソブリン クラウド (Azure Government、Azure Germany、および Azure China クラウド) のいずれかで作成されたサブスクリプションは Azure に登録できないため、使用状況データ レポートをサポートしません。 

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>ユーザーが Azure 課金ポータルで Azure Stack 使用状況データを識別するにはどうすればよいですか?
ユーザーは、使用状況の詳細ファイルで Azure Stack 使用状況データを確認できます。 使用状況の詳細ファイルを取得する方法については、「[download usage file from the Azure Account Center (Azure アカウント センターからの使用状況ファイルのダウンロード)](../billing/billing-download-azure-invoice-daily-usage-date.md#download-usage-from-the-account-center-csv)」の記事を参照してください。 使用状況の詳細ファイルには、Azure Stack ストレージおよび VM を識別する Azure Stack メーターが含まれています。 Azure Stack で使用されるリソースはすべて、"Azure Stack" という名前のリージョンのもとに報告されます。

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Azure Stack で報告された使用状況が Azure アカウント センターから生成されたレポートと一致しないのはなぜですか?
Azure Stack の Usage API を使用して使用状況データを報告する場合と、Azure アカウント センターを使用して使用状況データを報告する場合とでは時間差があります。 この遅延は、使用状況データを Azure Stack から Azure コマースにアップロードするために必要な時間です。 この遅延のために、午前 0 時の少し前に発生した使用状況は翌日 Azure に表示される可能性があります。 [Azure Stack の Usage API](azure-stack-provider-resource-api.md) を使用した場合、その結果を Azure 課金ポータルで報告された使用状況と比較すると、違いを確認できます。

## <a name="next-steps"></a>次のステップ

* [プロバイダー使用量 API](azure-stack-provider-resource-api.md)  
* [テナント使用量 API](azure-stack-tenant-resource-usage-api.md)
* [使用量に関する FAQ](azure-stack-usage-related-faq.md)