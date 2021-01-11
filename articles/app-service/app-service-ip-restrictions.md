---
title: Azure App Service のアクセス制限
description: Azure App Service でアクセス制限を指定することによってアプリをセキュリティで保護する方法について説明します。
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ce5882f2621dc5b8c48bcf5be6d4ea3a2f723bfe
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962963"
---
# <a name="azure-app-service-access-restrictions"></a>Azure App Service のアクセス制限

アクセス制限により、アプリへのネットワーク アクセスを制御する優先度で順序付けされた許可/拒否のリストを定義することができます。 リストには、IP アドレスまたは Azure Virtual Network のサブネットを含めることができます。 1 つまたは複数のエントリがある場合、リストの最後にあるものは暗黙的に "すべて拒否" になります。

アクセス制限機能は、App Service でホストされているすべてのワーク ロードに対して動作します。これには、Web アプリ、API アプリ、Linux アプリ、Linux コンテナー アプリ、関数が含まれます。

アプリへの要求が行われると、送信元のアドレスがアクセス制限リスト内の IP アドレス規則に対して評価されます。 送信元アドレスが Microsoft.Web に対するサービス エンドポイントで構成されているサブネット内にある場合は、ソース サブネットが、アクセス制限リスト内の仮想ネットワーク規則と比較されます。 アドレスがリスト内の規則に基づいてアクセスを許可されない場合、サービスは [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 状態コードで応答します。

アクセス制限機能は、コードが実行される worker ホストの上流にある App Service フロントエンド ロールに実装されています。 そのため、アクセス制限は、事実上のネットワーク ACL です。

Azure Virtual Network (VNet) からご自分の Web アプリへのアクセスを制限する機能は、[サービス エンドポイント][serviceendpoints]と呼ばれます。 サービス エンドポイントを使用すると、選択したサブネットからマルチテナント サービスへのアクセスを制限することができます。 ネットワーク側と、有効にされているサービスの両方でこれを有効にする必要があります。 App Service Environment でホストされているアプリへのトラフィックを制限することはできません。 App Service Environment 内の場合は、IP アドレス ルールでアプリへのアクセスを制御できます。

![アクセス制限のフロー](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>ポータルでのアクセス制限規則の追加および編集 ##

アクセス制限の規則をアプリに追加するには、メニューを使用して **[ネットワーク]** > **[アクセス制限]** を開き、 **[アクセス制限を構成する]** をクリックします

![App Service のネットワーク オプション](media/app-service-ip-restrictions/access-restrictions.png)  

[アクセス制限] の UI では、アプリに対して定義されているアクセス制限規則の一覧を確認できます。

![アクセス制限の一覧](media/app-service-ip-restrictions/access-restrictions-browse.png)

一覧には、現在アプリに設定されているすべての制限が表示されます。 アプリに VNet 制限がある場合は、Microsoft.Web に対するサービス エンドポイントが有効かどうかが表に表示されます。 アプリに制限が定義されていない場合は、どこからでもアプリにアクセスできます。  

## <a name="adding-ip-address-rules"></a>IP アドレス規則の追加

**[[+] ルールの追加]** をクリックすると、新しいアクセス制限の規則を追加できます。 規則は、追加するとすぐに有効になります。 規則は、最も小さい数字から始まり大きい数字に向かって、優先順位順に適用されます。 追加した規則が 1 つであっても、暗黙の "すべて拒否" 規則があります。

規則を作成するときは、許可/拒否だけでなく規則の種類も選択する必要があります。 優先度の値およびアクセス制限対象を指定する必要もあります。  必要に応じて、規則に名前と説明を追加できます。  

![IP アクセス制限規則を追加する](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

IP アドレスに基づく規則を設定するには、種類として IPv4 または IPv6 を選択します。 IP アドレスの表記には、IPv4 アドレスと IPv6 アドレスのどちらの場合も CIDR 表記を使用する必要があります。 正確なアドレスを指定するには、1.2.3.4/32 のようなアドレスを使用できます。この場合、最初の 4 オクテットが IP アドレスを表し、/32 がマスクです。 すべてのアドレスを表す IPv4 CIDR 表記は 0.0.0.0/0 です。 CIDR 表記の詳細については、「[Classless Inter-Domain Routing (クラスレス ドメイン間ルーティング)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)」を参照してください。 

## <a name="service-endpoints"></a>サービス エンドポイント

サービス エンドポイントを使用して、選択した Azure 仮想ネットワーク サブネットへのアクセスを制限できます。 特定のサブネットへのアクセスを制限するには、種類が仮想ネットワークである制限規則を作成します。 アクセスを許可または拒否するサブスクリプション、VNet、およびサブネットを選択できます。 選択したサブネットに対する Microsoft.Web でサービス エンドポイントがまだ有効になっていない場合は、自動的に有効にしないチェック ボックスをオンにしていない限り、自動的にオンになります。 アプリでは有効にしてサブネットでは有効にしない状況は、サブネット上のサービス エンドポイントを有効にするためのアクセス許可があるかどうかに大きく関係します。 他のユーザーにサブネット上のサービス エンドポイントを有効にさせる必要がある場合は、チェック ボックスをオンにし、後でサブネットで有効にされることを想定して、サービス エンドポイント用にアプリを構成することができます。 

![VNet アクセス制限規則を追加する](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

App Service Environment で実行されているアプリへのアクセスを制限するために、サービス エンドポイントを使うことはできません。 アプリが App Service Environment 内にあるときは、IP アクセス規則でアプリへのアクセスを制御できます。 

サービス エンドポイントにより、Application Gateway やその他の WAF デバイスを使用してアプリを構成することができます。 また、セキュリティで保護されたバックエンドを使用して多層アプリケーションを構成することもできます。 いくつかの方法の詳細については、[ネットワーク機能と App Service](networking-features.md) に関する記事と、「[サービス エンドポイントと Application Gateway の統合](networking/app-gateway-with-service-endpoints.md)」を参照 してください。

> [!NOTE]
> 現在のところ、IP SSL 仮想 IP (VIP) を使用する Web アプリにはサービス エンドポイントがサポートされていません。 
>

## <a name="managing-access-restriction-rules"></a>アクセス制限規則の管理

任意の行をクリックして、既存のアクセス制限規則を編集できます。 優先順位の変更を含め、編集内容はすぐに有効になります。

![アクセス制限規則を編集する](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

規則を編集するとき、IP アドレス規則と Virtual Network 規則の間で種類を変更することはできません。 

![アクセス制限規則を編集する](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

規則を削除するには、規則の **[...]** をクリックし、 **[削除]** をクリックします。

![アクセス制限規則を削除する](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>単一 IP アドレスのブロック ##

最初の IP 制限規則を追加するとき、サービスは、2147483647 の優先順位で、明示的な "**すべて拒否**" 規則を追加します。 実際には、明示的な "**すべて拒否**" 規則は、最後に実行される規則となり、"**許可**" 規則を使用して明示的に許可されていないすべての IP アドレスへのアクセスをブロックします。

ユーザーが、単一の IP アドレスまたは IP アドレス ブロックを明示的にブロックしたいが、その他のすべてのアクセスを許可したいシナリオでは、明示的な "**すべて許可**" 規則を追加する必要があります。

![単一 IP アドレスをブロックする](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM サイト 

アプリへのアクセスを制御できるだけでなく、アプリによって使用される SCM サイトへのアクセスを制限することもできます。 SCM サイトは Web デプロイ エンドポイントであり、Kudu コンソールでもあります。 SCM サイトとアプリに異なるアクセス制限を割り当てることも、アプリと SCM サイトの両方に同じセットを使うこともできます。 チェック ボックスをオンにしてアプリと同じ制限にすると、すべてのものが空になります。チェック ボックスをオフにした場合は、以前に SCM サイトに対して行ったすべての設定が適用されます。 

![アクセス制限の一覧](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>アクセス制限規則のプログラムによる操作 ##

[Azure CLI](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) と [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) では、アクセス制限の編集がサポートされています。 Azure CLI を使用してアクセス制限を追加する例:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Azure PowerShell を使用してアクセス制限を追加する例:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

リソース マネージャーのアプリ構成で [Azure REST API](/rest/api/azure/) の PUT 操作を使用するか、Azure Resource Manager テンプレートを使用して、値を手動で設定することもできます。 たとえば、resources.azure.com を使用して ipSecurityRestrictions ブロックを編集して、必要な JSON を追加することができます。

Resource Manager におけるこの情報の場所は次のとおりです。

management.azure.com/subscriptions/ **<サブスクリプション ID>** /resourceGroups/ **<リソース グループ>** /providers/Microsoft.Web/sites/ **<Web アプリ名>** /config/web?api-version=2018-02-01

前の例の JSON 構文を次に示します。
```json
{
  "properties": {
    "ipSecurityRestrictions": [
      {
        "ipAddress": "122.133.144.0/24",
        "action": "Allow",
        "priority": 100,
        "name": "IP example rule"
      }
    ]
  }
}
```

## <a name="azure-functions-access-restrictions"></a>Azure Functions のアクセス制限

アクセス制限は、関数アプリでも App Service プランと同じ機能を利用できます。 アクセス制限を有効にすると、許可されていない IP に対するポータル コード エディターは無効になります。

## <a name="next-steps"></a>次のステップ
[Azure Functions のアクセス制限](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[サービス エンドポイントと Application Gateway の統合](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md