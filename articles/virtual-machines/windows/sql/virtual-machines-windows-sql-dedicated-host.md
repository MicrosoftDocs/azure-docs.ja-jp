---
title: Azure 専用ホストでの SQL Server VM
description: Azure 専用ホストでの SQL Server VM の実行の詳細について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8b29bbce1511b022def522d46c74b99967a76ea3
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204482"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>Azure 専用ホストでの SQL Server VM 

この記事では、[Azure 専用ホスト](/azure/virtual-machines/windows/dedicated-hosts)での SQL Server VM の使用方法の詳細について説明します。 Azure 専用ホストの詳細については、ブログ記事「[Azure Dedicated Host の導入](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/)」を参照してください。 

## <a name="overview"></a>概要
[Azure 専用ホスト](/azure/virtual-machines/windows/dedicated-hosts)は、1 つの Azure サブスクリプションに対して専用の物理サーバーを提供するサービスであり、1 つ以上の仮想マシンをホストできます。 専用ホストは Microsoft のデータ センターで使用されるものと同じ物理サーバーであり、リソースとして提供されます。 リージョン、可用性ゾーン、障害ドメイン内に専用ホストをプロビジョニングできます。 次に、プロビジョニングされたホストに VM を直接配置し、ニーズに最も合った構成にすることができます。


[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]


## <a name="licensing"></a>ライセンス

Azure 専用ホストに SQL Server VM を追加する場合は、2 つの異なるライセンス オプションのいずれかを選択できます。 

  - **SQL VM ライセンス**:これは既存のライセンス オプションで、SQL Server VM ライセンスごとに個別に課金されます。 
  - **専用ホスト ライセンス**:Azure 専用ホストで使用できる新しいライセンス モデルであり、SQL Server ライセンスはホスト レベルでバンドルされ、課金されます。 


既存の SQL Server ライセンスを使用するためのホスト レベルのオプション: 
  - SQL Server Enterprise Edition の Azure ハイブリッド特典
    - SA またはサブスクリプションをお持ちのお客様が利用できます。
    - 使用可能なすべての物理コアのライセンスを取得し、無制限の仮想化 (ホストでサポートされている vCPU の最大数まで) を利用できます。
        - Azure 専用ホストに Azure ハイブリッド特典を適用する方法の詳細については、「[Azure Hybrid Benefit についてのよくあるご質問](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)」を参照してください。 
  - 10 月 1 日より前に取得された SQL Server ライセンス
      - SQL Server Enterprise Edition には、ホストレベルと VM ごとのライセンス オプションがあります。 
      - SQL Server Standard Edition では、VM ごとのライセンス オプションのみ使用できます。 
          - 詳細については、[Microsoft の製品条項](https://www.microsoft.com/licensing/product-licensing/products)に関するページを参照してください。 
  - SQL Server 専用ホスト レベルのオプションが選択されていない場合は、マルチテナント VM と同様に、SQL Server AHB を個々の VM のレベルで選択できます。



## <a name="provisioning"></a>プロビジョニング  
専用ホストへの SQL Server VM のプロビジョニングは、他の Azure 仮想マシンと何ら変わりはありません。 これは、[Azure PowerShell](../dedicated-hosts-powershell.md)、[Azure portal](../dedicated-hosts-portal.md)、[Azure CLI](../../linux/dedicated-hosts-cli.md) を使用して行うことができます。

既存の SQL Server VM を専用ホストに追加するプロセスにはダウンタイムが必要ですが、データに影響はなく、データが失われることもありません。 ただし、システム データベースを含むすべてのデータベースは、移動前にバックアップする必要があります。

## <a name="virtualization"></a>仮想化 

専用ホストの利点の 1 つは、無制限の仮想化です。 たとえば、64 個の仮想コアのライセンスを使用することができますが、128 個の仮想コアを使用するようにホストを構成できるため、仮想コアの数を 2 倍にする一方で、支払いは SQL Server ライセンスの半分に対してのみになります。 

これは独自のホストであるため、1:2 の比率で仮想化を設定することができます。 

## <a name="faq"></a>FAQ

**Q:Azure 専用ホストで Windows Server/SQL Server ライセンスの Azure ハイブリッド特典はどのように適用されますか?**

A:お客様はソフトウェア アシュアランス付きの既存の Windows Server/SQL Server ライセンス、または条件を満たすサブスクリプション ライセンスの価値を利用し、Azure ハイブリッド特典を使用して Azure 専用ホストの支払いに割引料金を適用できます。 Windows Server Datacenter および SQL Server Enterprise Edition を使用するお客様は、ホスト全体のライセンスを取得して Azure ハイブリッド特典を使用すると、無制限の仮想化 (基になるサーバーの物理容量に応じて、できるだけ多くの Windows Server 仮想マシンをホスト上にデプロイすること) が可能になります。  Windows Server と SQL Server 2008/R2 の延長セキュリティ更新プログラムは、Azure 専用ホストの Windows Server と SQL Server のすべてのワークロードにも無料で適用されます。 

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上の SQL Server の価格ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)


