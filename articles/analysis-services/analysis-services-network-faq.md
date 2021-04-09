---
title: Analysis Services ネットワーク接続についてよく寄せられる質問 | Microsoft Docs
description: この記事では、Analysis Services のネットワーク接続についてよく寄せられる質問とその回答を示します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "82838503"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>Analysis Services ネットワーク接続についてよく寄せられる質問

この記事では、ストレージ アカウント、データ ソース、ファイアウォール、IP アドレスへの接続についてよく寄せられる質問とその回答を示します。

## <a name="backup-and-restore"></a>バックアップと復元

**質問** - ファイアウォールの背後にあるストレージ アカウントを使用してバックアップおよび復元するにはどうすればよいですか。   
**回答** - Azure Analysis Services には、固定の IP アドレスまたはサービス タグが使用されていません。 Analysis Services サーバーに使用する IP アドレスの範囲は、*Azure リージョン* の IP アドレスの範囲内であれば任意のものを指定できます。 サーバーの IP アドレスは可変であり、時間の経過と共に変化する可能性があるため、ファイアウォール規則では、サーバーが存在する Azure リージョンの IP アドレスの範囲全体を許可する必要があります。

**質問** - Azure ストレージ アカウントは、Analysis Services サーバーとは異なるリージョンにあります。 ストレージ アカウントのファイアウォール設定を構成するにはどうすればよいですか。   
**回答** - ストレージ アカウントが別のリージョンにある場合、ストレージ アカウントのファイアウォール設定を構成して、 **[選択されたネットワーク]** からのアクセスを許可します。 ファイアウォールの **[アドレス範囲]** に、Analysis Services サーバーがあるリージョンの IP アドレスの範囲を指定します。 Azure リージョンの IP 範囲を取得するには、「[Azure IP 範囲とサービス タグ – パブリック クラウド](https://www.microsoft.com/download/details.aspx?id=56519)」をご覧ください。 すべてのネットワークからのアクセスを許可するようにストレージ アカウントのファイアウォール設定を構成することはできますが、[選択されたネットワーク] を選択して IP アドレス範囲を指定することをお勧めします。 

**質問** - Azure ストレージ アカウントが Analysis Services サーバーと同じリージョンにあります。 ストレージ アカウントのファイアウォール設定を構成するにはどうすればよいですか。   
**回答** - Analysis Services サーバーとストレージ アカウントが同じリージョンにあるため、両者間の通信では内部 IP アドレス範囲が使用されます。このため、選択されたネットワークを使用するようにファイアウォールを構成したり、IP アドレス範囲を指定したりすることはできません。 組織のポリシーにファイアウォールが必要な場合は、すべてのネットワークからのアクセスを許可するように構成する必要があります。


## <a name="data-source-connections"></a>データ ソース接続

**質問** - データ ソース システムには VNET があります。 Analysis Services サーバーに対して VNET のデータベースへのアクセスを許可するにはどうすればよいですか。   
**回答** - Azure Analysis Services は VNET に参加できません。 この場合の最適な解決策は、VNET 上にオンプレミス データ ゲートウェイをインストールして構成し、次に **AlwaysUseGateway** サーバー プロパティを使用して Analysis Services サーバーを構成することです。 詳細については、「[Azure Virtual Network (VNet) 上のデータソースに対してゲートウェイを使用する](analysis-services-vnet-gateway.md)」を参照してください。

**質問** - ファイアウォールの背後にソース データベースがあります。 Analysis Services サーバーがファイアウォールにアクセスできるようにファイアウォールを構成するにはどうすればよいですか。   
**回答** - Azure Analysis Services には、固定の IP アドレスまたはサービス タグが使用されていません。 Analysis Services サーバーに使用する IP アドレスの範囲は、*Azure リージョン* の IP アドレスの範囲内であれば任意のものを指定できます。 ソース データベースのファイアウォール規則で、サーバーの Azure リージョンの IP アドレスの "*全範囲*" を指定する必要があります。 もう 1 つの、おそらくより安全な代替策として、オンプレミス データ ゲートウェイを構成する方法があります。 次に、[AlwaysUseGateway サーバー プロパティ](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property)を使用して Analysis Services サーバーを構成し、オンプレミス データ ゲートウェイの IP アドレスがデータ ソースのファイアウォール規則で許可されているものであることを確認します。

## <a name="azure-apps-with-ip-address"></a>Azure アプリと IP アドレス

**質問** - IP アドレスがすぐに変化する Azure ベースのアプリケーション (たとえば、Azure Functions、Azure Data Factory) を使用しています。 Azure Analysis Services のファイアウォール規則を管理して、アプリが実行されている IP アドレスを動的に許可するにはどうすればよいですか。   
**回答** - Azure Analysis Services は、プライベート リンク、VNET、またはサービス タグをサポートしていません。 いくつかのオープン ソース ソリューションがあります (クライアント アプリケーションの IP アドレスを検出し、ファイアウォール規則を自動的かつ一時的に更新する https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) など)。


## <a name="next-steps"></a>次のステップ

[オンプレミス データ ゲートウェイをインストールして構成する](analysis-services-gateway-install.md)   
[オンプレミス データ ゲートウェイを使用してオンプレミスのデータ ソースに接続する](analysis-services-gateway.md)   
[Azure Virtual Network (VNet) 上のデータソースに対してゲートウェイを使用する](analysis-services-vnet-gateway.md)
