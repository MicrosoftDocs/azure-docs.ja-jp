---
title: "Azure Load Balancer 用の高可用性ポートを構成する | Microsoft Docs"
description: "すべてのポート上で負荷分散の内部トラフィックに高可用性ポートを使用する方法について説明します"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: 7256548b988812c64ca9a9f8a84fec377646635d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-high-availability-ports-for-internal-load-balancer"></a>内部 Load Balancer 用の高可用性ポートを構成する方法

この記事では、内部 Load Balancer に高可用性 (HA) ポートをデプロイする例について説明します。 ネットワーク仮想アプライアンス固有の構成については、対応するプロバイダーの Web サイトを参照してください。

>[!NOTE]
> 高可用性ポート機能は現在プレビュー中です。 プレビュー期間は、一般公開リリースの機能と同じレベルの可用性と信頼性がない場合があります。 詳細については、[Microsoft Azure プレビューのMicrosoft Azure 追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

図 1 は、この記事で説明するデプロイ例の次の構成を示したものです。
- NVA は、HA ポート構成の背後にある内部 Load Balancer のバックエンド プールにデプロイされます。 
- DMZ サブネットに適用された UDR は、次のホップを内部 Load Balancer の仮想 IP にすることで、すべてのトラフィックを <?> にルートします。 
- 内部 Load Balancer は、LB アルゴリズムに従ってアクティブな NVA LB のいずれかにトラフィックを分散します。
- NVA はトラフィックを処理し、バックエンド サブネット内の元の送信先に転送します。
- また、対応する UDR がバックエンド サブネットで構成されている場合、リターン パスが同じルートをたどる可能性があります。 

![HA ポートのデプロイ例](./media/load-balancer-configure-ha-ports/haports.png)

図 1 - 高可用性ポートを使用して内部 Load Balancer の背後にデプロイされているネットワーク仮想アプライアンス 

## <a name="preview-sign-up"></a>プレビューのサインアップ

Load Balancer Standard SKU の HA ポート機能のプレビューに参加するには、PowerShell または Azure CLI 2.0 を使用してサブスクリプションを登録します。

- PowerShell を使用したサインアップ

   ```powershell
   Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```

- Azure CLI 2.0 を使用したサインアップ

    ```cli
  az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network  
    ```

## <a name="configuring-ha-ports"></a>HA ポートを構成する

HA ポートの構成には、内部 Load Balancer のセットアップ、バックエンド プールへの NVA のデプロイ、NVA ヘルスを検出するための対応するロード バランサー ヘルス プローブの構成、および HA ポートに関するロード バランサー ルールが必要です。 全般的な Load Balancer 関連の構成については、[基本に関するページ](load-balancer-get-started-ilb-arm-portal.md)を参照してください。 この記事では、HA ポート構成について取り上げます。

構成では、基本的にフロントエンド ポートおよびバックエンドのポート値を **0** に設定し、プロトコル値を **All** に設定する必要があります。 この記事では、Azure Portal、PowerShell、および Azure CLI 2.0 を使用して高可用性ポートを構成する方法について説明します。

### <a name="configure-ha-ports-load-balancer-rule-with-the-azure-portal"></a>Azure Portal を使用して HA ポートのロード バランサー ルールを構成する

Azure Portal には、この構成のチェックボックスを使用する **[HA ポート]** オプションがあります。 このオプションを選択すると、関連するポートとプロトコルの構成が自動的に設定されます。 

![Azure Portal を使用した HA ポートの構成](./media/load-balancer-configure-ha-ports/haports-portal.png)

図 2 - Azure Portal を使用した HA ポータルの構成

### <a name="configure-ha-ports-load-balancer-rule-with-powershell"></a>PowerShell を使用して HA ポートのロード バランサー ルールを構成する

PowerShell で、次のコマンドを使用して HA ポートのロード バランサー ルールを作成し、内部 Load Balancer を作成します。

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-ha-ports-load-balancer-rule-with-azure-cli-20"></a>Azure CLI 2.0 を使用して HA ポートのロード バランサー ルールを構成する

「[Azure CLI を使用した内部ロード バランサーの作成](load-balancer-get-started-ilb-arm-cli.md)」の手順 4 で、次のコマンドを使用して HA ポートの Load Balancer ルールを作成します。

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>次のステップ

- [高可用性ポート](load-balancer-ha-ports-overview.md)の詳細について学びます
