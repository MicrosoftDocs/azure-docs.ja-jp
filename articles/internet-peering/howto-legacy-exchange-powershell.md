---
title: PowerShell を使用してレガシの Exchange ピアリングを Azure リソースに変換する
titleSuffix: Azure
description: PowerShell を使用してレガシの Exchange ピアリングを Azure リソースに変換する
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 769522238939fe8ee786ae51a1b3b6051604451e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89071671"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>PowerShell を使用してレガシの Exchange ピアリングを Azure リソースに変換する

この記事では、PowerShell コマンドレットを使用して既存のレガシの Exchange ピアリングを Azure リソースに変換する方法について説明します。

必要に応じて、Azure [portal](howto-legacy-exchange-portal.md) を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)と [Exchange ピアリングのチュートリアル](walkthrough-exchange-all.md)を確認します。

### <a name="work-with-azure-powershell"></a>Azure PowerShell を使用する
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>レガシの Exchange ピアリングを Azure リソースに変換する

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure アカウントにサインインしてサブスクリプションを選択する
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>変換対象のレガシの Exchange ピアリングを取得する
次の例は、シアトルのピアリングの場所でレガシの Exchange ピアリングを取得する方法を示しています。

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

応答は次の例のようになります。
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>従来のピアリングを変換する
次のコマンドを使用して、レガシの Exchange ピアリングを Azure リソースに変換できます。

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> レガシのピアリングを Azure リソースに変換する場合、変更はサポートされません。
[https://github.com/mysqljs/mysql/](&nbsp;)

次の応答は、エンド ツー エンドのプロビジョニングが正常に完了した場合の例を示しています。

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>その他のリソース
次のコマンドを実行することで、すべてのパラメーターの詳細な説明を取得できます。

```powershell
Get-Help Get-AzPeering -detailed
```
詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [PowerShell を使用して Exchange ピアリングを作成または変更する](howto-exchange-powershell.md)
