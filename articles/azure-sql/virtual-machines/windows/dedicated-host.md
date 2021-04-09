---
title: Azure Dedicated Host で SQL Server VM を実行する
description: Azure Dedicated Host で SQL Server VM を実行する方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 957e458bf21eb4a95b1faba1daf0459271648310
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358692"
---
# <a name="run-sql-server-vm-on-an-azure-dedicated-host"></a>Azure Dedicated Host で SQL Server VM を実行する 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、[Azure Dedicated Host](../../../virtual-machines/dedicated-hosts.md) での SQL Server 仮想マシン (VM) の使用方法の詳細について説明します。 Azure Dedicated Host の詳細については、ブログ記事「[Azure Dedicated Host の導入](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/)」を参照してください。 

## <a name="overview"></a>概要
[Azure 専用ホスト](../../../virtual-machines/dedicated-hosts.md)は、1 つの Azure サブスクリプションに対して専用の物理サーバーを提供するサービスであり、1 つ以上の仮想マシンをホストできます。 専用ホストは Microsoft のデータ センターで使用されるものと同じ物理サーバーであり、リソースとして提供されます。 リージョン、可用性ゾーン、障害ドメイン内に専用ホストをプロビジョニングできます。 次に、プロビジョニングされたホストに VM を直接配置し、ニーズに最も合った構成にすることができます。

## <a name="limitations"></a>制限事項

- すべての VM シリーズが専用ホストでサポートされているわけではありません。VM シリーズの可用性はリージョンによって異なります。 詳細については、[Azure 専用ホストの概要](../../../virtual-machines/dedicated-hosts.md)に関する記事を参照してください。

## <a name="licensing"></a>ライセンス

Azure Dedicated Host に SQL Server VM を配置する場合は、2 つの異なるライセンス オプションから選択できます。 

  - **SQL VM ライセンス**:これは既存のライセンス オプションで、SQL Server VM ライセンスごとに個別に課金されます。 
  - **専用ホスト ライセンス**:Azure 専用ホストで使用できる新しいライセンス モデルであり、SQL Server ライセンスはホスト レベルでバンドルされ、課金されます。 


既存の SQL Server ライセンスを使用するためのホスト レベルのオプション: 
  - SQL Server Enterprise Edition の Azure ハイブリッド特典 (AHB)
    - SA またはサブスクリプションをお持ちのお客様が利用できます。
    - 使用可能なすべての物理コアのライセンスを取得し、無制限の仮想化 (ホストでサポートされている vCPU の最大数まで) を利用できます。
        - Azure Dedicated Host に AHB を適用する方法の詳細については、「[Azure Hybrid Benefit についてのよくあるご質問](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)」を参照してください。 
  - 10 月 1 日より前に取得された SQL Server ライセンス
      - SQL Server Enterprise Edition には、ホストレベルと VM ごとのライセンス オプションがあります。 
      - SQL Server Standard Edition では、VM ごとのライセンス オプションのみ使用できます。 
          - 詳細については、[Microsoft の製品条項](https://www.microsoft.com/licensing/product-licensing/products)に関するページを参照してください。 
  - SQL Server 専用ホスト レベルのオプションが選択されていない場合は、マルチテナント VM と同様に、個々の VM のレベルで SQL Server AHB を選択できます。



## <a name="provisioning"></a>プロビジョニング  
専用ホストへの SQL Server VM のプロビジョニングは、他の Azure 仮想マシンと何ら変わりはありません。 これは、[Azure PowerShell](../../../virtual-machines/windows/dedicated-hosts-powershell.md)、[Azure portal](../../../virtual-machines/dedicated-hosts-portal.md)、[Azure CLI](../../../virtual-machines/linux/dedicated-hosts-cli.md) を使用して行うことができます。

既存の SQL Server VM を専用ホストに追加するプロセスにはダウンタイムが必要ですが、データに影響を及ぼすことも、データが失われることもありません。 ただし、システム データベースを含むすべてのデータベースは、移動前にバックアップする必要があります。

## <a name="virtualization"></a>仮想化 

専用ホストの利点の 1 つは、無制限の仮想化です。 たとえば、64 個の仮想コアのライセンスを使用することができますが、128 個の仮想コアを使用するようにホストを構成できるため、仮想コアの数が 2 倍になる一方で、支払いは SQL Server ライセンスの半分になります。 

これは独自のホストであるため、1:2 の比率で仮想化を設定することができます。 

## <a name="faq"></a>よく寄せられる質問

**Q:Azure 専用ホストで Windows Server/SQL Server ライセンスの Azure ハイブリッド特典はどのように適用されますか?**

A:お客様はソフトウェア アシュアランス付きの既存の Windows Server/SQL Server ライセンス、または条件を満たすサブスクリプション ライセンスの価値を利用し、Azure ハイブリッド特典を使用して Azure 専用ホストの支払いに割引料金を適用できます。 Windows Server Datacenter および SQL Server Enterprise Edition を使用するお客様は、ホスト全体のライセンスを取得して Azure ハイブリッド特典を使用すると、無制限の仮想化 (基になるサーバーの物理容量に応じて、できるだけ多くの Windows Server 仮想マシンをホスト上にデプロイすること) が可能になります。  Windows Server と SQL Server 2008/R2 の延長セキュリティ更新プログラムは、Azure 専用ホストの Windows Server と SQL Server のすべてのワークロードにも無料で適用されます。 

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](frequently-asked-questions-faq.md)
* [Windows VM 上の SQL Server の価格ガイダンス](pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](doc-changes-updates-release-notes.md)