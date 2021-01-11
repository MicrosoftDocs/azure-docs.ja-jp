---
title: Azure PowerShell を使用した Azure Traffic Manager サブネットのオーバーライド | Microsoft Docs
description: この記事では、Traffic Manager サブネットのオーバーライドを使用して、Traffic Manager プロファイルのルーティング方法をオーバーライドし、Azure PowerShell を使用して、事前に定義された IP 範囲からエンドポイントへのマッピングを介し、エンドユーザーの IP アドレスに基づいてエンドポイントにトラフィックを送信する方法について説明します。
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 01bd3b1e945ee7c9ac16af7048536c0a9e2d731a
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401590"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Azure Powershell を使用した Traffic Manager サブネットのオーバーライド

Traffic Manager サブネットのオーバーライドを使用すると、プロファイルのルーティング方法を変更できます。  オーバーライドを追加すると、事前に定義された IP 範囲からエンドポイントへのマッピングを使用してエンド ユーザーの IP アドレスに基づいてトラフィックが転送されます。 

## <a name="how-subnet-override-works"></a>サブネットのオーバーライドのしくみ

サブネットのオーバーライドがトラフィック マネージャー プロファイルに追加されると、Traffic Manager によって、エンド ユーザーの IP アドレスに対してサブネットのオーバーライドがあるかどうかが最初に確認されます。 見つかった場合、ユーザーの DNS クエリは対応するエンドポイントに送られます。  マッピングが見つからない場合、Traffic Manager はプロファイルの元のルーティング方法にフォールバックします。 

IP アドレス範囲は、CIDR 範囲 (1.2.3.0/24 など) またはアドレス範囲として指定できます (1.2.3.4-5.6.7.8 など) として指定できます。 各エンドポイントに関連付けられている IP 範囲は、そのエンドポイントに対して一意である必要があります。 異なるエンドポイント間で IP 範囲が重複していると、プロファイルが Traffic Manager によって拒否されます。

サブネットのオーバーライドをサポートするルーティング プロファイルには、次の 2 種類があります。

* **地理的** - DNS クエリの IP アドレスに対するサブネットのオーバーライドが Traffic Manager によって検出された場合、エンドポイントの正常性にかかわらず、クエリがエンドポイントにルーティングされます。
* **パフォーマンス** - DNS クエリの IP アドレスに対するサブネットのオーバーライドが Traffic Manager によって検出された場合、トラフィックは正常な場合にのみエンドポイントにルーティングされます。  サブネットのオーバーライド エンドポイントが正常でない場合、Traffic Manager はパフォーマンス ルーティング ヒューリスティックにフォールバックします。

## <a name="create-a-traffic-manager-subnet-override"></a>Traffic Manager のサブネットのオーバーライドの作成

Traffic Manager サブネットのオーバーライドを作成するには、Azure PowerShell を使用して、Traffic Manager エンドポイントへのオーバーライド用のサブネットを追加します。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下のコマンドは、[Azure Cloud Shell](https://shell.azure.com/powershell) で、またはコンピューターから PowerShell を実行することで実行できます。 Azure Cloud Shell は無料の対話型シェルです。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 お使いのコンピューターから PowerShell を実行する場合は、Azure PowerShell モジュール 1.0.0 以降が必要です。 `Get-Module -ListAvailable Az` を実行して、インストールされているバージョンを確認できます。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカル環境で実行している場合は、`Login-AzAccount` を実行して Azure にサインインする必要もあります。


1. **Traffic Manager エンドポイントを取得する:**

    サブネットのオーバーライドを有効にするには、[Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0) を使用してオーバーライドを追加するエンドポイントを取得し、変数に格納します。

    Name、ProfileName、および ResourceGroupName を、変更するエンドポイントの値に置き換えます。

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **IP アドレスの範囲をエンドポイントに追加する:**
    
    エンドポイントに IP アドレス範囲を追加するには、[Add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) を使用して範囲を追加します。

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    範囲が追加されたら、[Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) を使用してエンドポイントを更新します。

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
[Remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0) を使用して IP アドレスの範囲の削除を完了できます。

1.  **Traffic Manager エンドポイントを取得する:**

    サブネットのオーバーライドを有効にするには、[Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0) を使用してオーバーライドを追加するエンドポイントを取得し、変数に格納します。

    Name、ProfileName、および ResourceGroupName を、変更するエンドポイントの値に置き換えます。

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **エンドポイントから IP アドレスの範囲を削除する:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     範囲が削除されたら、[Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) を使用してエンドポイントを更新します。

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>次のステップ
Traffic Manager の [トラフィック ルーティング方法](traffic-manager-routing-methods.md)の詳細を確認する。

[サブネット トラフィックのルーティング方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)の詳細を確認する
