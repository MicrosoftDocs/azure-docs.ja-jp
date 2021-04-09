---
title: Azure Confidential Computing FAQ
description: Confidential Computing についてよく寄せられる質問の回答です。
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: a5ecd3827bbdc12b098684f1feda2df652f11940
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551914"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Azure Confidential Computing についてよく寄せられる質問

この記事では、[Azure 仮想マシンで Confidential Computing ワークロード](overview.md)を実行することに関して特に多く寄せられる質問への回答を示します。

この記事で Azure の問題に対処できない場合は、[MSDN および Stack Overflow の Azure 関連フォーラム](https://azure.microsoft.com/support/forums/)を参照してください。 問題をこれらのフォーラムまたは [Twitter の @AzureSupport](https://twitter.com/AzureSupport) に投稿できます。 Azure サポート要求を送信することもできます。 サポート要求を送信するには、[[Azure サポート]](https://azure.microsoft.com/support/options/) ページで [サポートを受ける] を選択します。

## <a name="confidential-computing-virtual-machines"></a>Confidential Computing 仮想マシン <a id="vm-faq"></a>

**DCsv2 シリーズの VM を Azure にデプロイするにはどうすればよいですか。**

DCsv2 VM は次のような方法でデプロイできます。
   - [Azure Resource Manager テンプレート](../virtual-machines/windows/template-description.md)の使用
   - [Azure Portal](https://portal.azure.com/#create/hub) から
   - [Azure Confidential Computing (仮想マシン)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) マーケットプレース ソリューション テンプレートで。 サポートされているシナリオ (リージョン、イメージ、可用性、ディスク暗号化) だけを顧客に利用してもらうとき、マーケットプレース ソリューション テンプレートが役立ちます。 

**すべての OS イメージが Azure Confidential Computing と連動しますか?**

いいえ。 仮想マシンをデプロイできるのは、Ubuntu Server 18.04、Ubuntu Server 16.04、Windows Server 2019 Datacenter、および Windows Server 2016 Datacenter を搭載した第 2 世代のオペレーティング マシン上だけです。 [Linux](../virtual-machines/generation-2.md) と [Windows](../virtual-machines/generation-2.md) の Gen 2 VM に関する詳細をご覧ください。

**DCsv2 仮想マシンがポータルで淡色表示され、選択できません**

VM の横に付いている情報バブルに基づき、さまざまなアクションを行います。
   -    **UnsupportedGeneration**:仮想マシン イメージの生成を "Gen2" に変更します。
   -    **NotAvailableForSubscription**: お使いのサブスクリプションではこのリージョンを利用できません。 使用可能なリージョンを選択します。
   -    **InsufficientQuota**:[クォータを増やすためのサポート要求を作成します](../azure-portal/supportability/per-vm-quota-requests.md)。 無料試用版サブスクリプションの場合、Confidential Computing VM のクォータがありません。 

**ポータルのサイズ セレクターで見つけようとしても、DCsv2 仮想マシンが表示されません**

[利用できるリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)を選択していることを確認してください。 また、サイズ セレクターでは必ず "すべてのフィルターをクリア" を選択してください。 

**Azure Confidential Computing で高速ネットワークを有効にできますか?**

 いいえ。 高速ネットワークは、DC シリーズおよび DCsv2 シリーズの仮想マシンではサポートされていません。 Confidential Computing 仮想マシンのデプロイ、および Confidential Computing で実行されている Azure Kubernetes Service クラスターのデプロイでは、高速ネットワークは有効にできません。

**これらのマシンで Azure Dedicated Host を使用できますか?**

はい。 Azure Dedicated Host は、DCsv2 シリーズの仮想マシンをサポートします。 Azure Dedicated Host は、仮想マシンを実行するためのシングル テナントの物理サーバーを提供します。 ユーザーは通常、物理的なセキュリティ、データの整合性、監視に関するコンプライアンス要件に対応するために、Azure Dedicated Host を使用します。 

**Azure Resource Manager テンプレートのデプロイ失敗エラー "Operation could not be completed as it results in exceeding approved standard DcsV2 Family Cores Quota (操作結果が、承認されている Standard DcsV2 ファミリのコア クォータを超えるため、操作を完了できませんでした)"** が表示されます

[クォータを増やすためのサポート要求を作成します](../azure-portal/supportability/per-vm-quota-requests.md)。 無料試用版サブスクリプションの場合、Confidential Computing VM のクォータがありません。 

**DCsv2-Series VM と DC-Series VM の違いは何ですか?**

DC-Series VM は、より古い Intel SGX を搭載した 6 コア Intel プロセッサ上で動作し、メモリの合計や Enclave Page Cache (EPC) のメモリが少なく、2 つのリージョン (米国東部とヨーロッパ西部での Standard_DC2s と Standard_DC4s サイズ) でのみ利用できます。 これらの VM を一般提供する予定はありません。また、運用環境での使用はお勧めできません。 これらの VM をデプロイするには、[Confidential Compute DC-Series VM [プレビュー]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) Marketplace インスタンスを使用してください。

**DCsv2 仮想マシンは世界中で利用できますか?**

いいえ。 現時点では、これらの仮想マシンは一部のリージョンでのみ利用できます。 利用できるリージョンの最新情報については、[リージョン別製品ページ](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)をご覧ください。 

**これらのマシンではハイパースレッディングは無効にされますか?**

すべての Azure Confidential Computing クラスターでは、ハイパースレッディングが無効にされます。

**Open Enclave SDK を DCsv2 仮想マシンにインストールするにはどうすればよいですか。**
   
Azure マシンまたはオンプレミス マシンに OE SDK をインストールする方法については、[Open Enclave SDK GitHub](https://github.com/openenclave/openenclave) の手順に従ってください。
     
OS 固有のインストール手順については、Open Enclave SDK GitHub も参照できます。
   - [Windows に OE SDK をインストールする](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [Ubuntu 18.04 に OE SDK をインストールする](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
   - [Ubuntu 16.04 に OE SDK をインストールする](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)