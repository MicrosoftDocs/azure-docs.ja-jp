---
title: PowerShell を使用してピア ASN を Azure サブスクリプションに関連付ける
titleSuffix: Azure
description: PowerShell を使用してピア ASN を Azure サブスクリプションに関連付ける
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b463293899dc2586404d68145943caff3105e89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590190"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>PowerShell を使用してピア ASN を Azure サブスクリプションに関連付ける

ピアリング要求を送信する前にまず、次の手順に従って、ASN を Azure サブスクリプションに関連付ける必要があります。

必要に応じて、[ポータル](howto-subscription-association-portal.md)を使用してこのガイドを完了することもできます。

### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerASN を作成して ASN を Azure サブスクリプションに関連付ける

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure アカウントにサインインしてサブスクリプションを選択する
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>ピアリング リソース プロバイダーに登録する
次のコマンドを使用して、サブスクリプションのピアリング リソース プロバイダーに登録します。 これを実行しないと、ピアリングのセットアップに必要な Azure リソースにアクセスできません。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

次のコマンドを使用して、登録の状態を確認できます。
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> *RegistrationState* が "Registered" になるまで待ってから、次に進みます。 コマンドを実行した後、5 から 30 分かかる場合があります。

### <a name="update-the-peer-information-associated-with-this-subscription"></a>このサブスクリプションに関連付けられているピア情報を更新する

次に、ピア情報を更新する例を示します。

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Name は、リソース名に対応し、任意の名前を選択できます。 一方、-peerName は会社の名前に対応しており、PeeringDB プロファイルにできるだけ近いものにする必要があります。 -peerName の値では、文字 a-z、A-Z、およびスペースのみがサポートされていることに注意してください。

1 つのサブスクリプションで複数の ASN を使用できます。 各 ASN のピアリング情報を更新します。 "name" が各 ASN に対して一意であることを確認します。

ピアの [PeeringDB](https://www.peeringdb.com) には、完全な最新のプロファイルが必要です。 登録時にこの情報を使用して、NOC 情報、技術担当者の連絡先情報、ピアリング設備での存在など、ピアの詳細が検証されます。

上記の出力の **{subscriptionId}** の代わりに、実際のサブスクリプション ID が表示されます。

## <a name="view-status-of-a-peerasn"></a>PeerASN の状態を表示する

ASN 検証の状態を確認するには、次のコマンドを使用します。

```powershell
Get-AzPeerAsn
```

応答の例を以下に示します。
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> ValidationState が "Approved" になるのを待ってから、ピアリング要求を送信します。 この承認には、最大で 12 時間かかる場合があります。

## <a name="modify-peerasn"></a>PeerASN を変更する
NOC の連絡先情報は、いつでも変更できます。

次に例を示します。

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>PeerASN を削除する
現在、PeerASN の削除はサポートされていません。 PeerASN を削除する必要がある場合は、[Microsoft ピアリング](mailto:peering@microsoft.com)にお問い合わせください。

## <a name="next-steps"></a>次のステップ

* [Direct ピアリングを作成または変更する](howto-direct-powershell.md)
* [従来の Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-powershell.md)
* [Exchange ピアリングを作成または変更する](howto-exchange-powershell.md)
* [従来の Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>その他のリソース

詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください
