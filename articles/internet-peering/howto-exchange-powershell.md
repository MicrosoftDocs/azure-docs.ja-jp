---
title: PowerShell を使用して Exchange ピアリングを作成または変更する
titleSuffix: Azure
description: PowerShell を使用して Exchange ピアリングを作成または変更する
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 59d098f1ff0f78e37601544619c603b776e0cf22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89071756"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>PowerShell を使用して Exchange ピアリングを作成または変更する

この記事では、PowerShell コマンドレットと Resource Manager デプロイ モデルを使用して、 Microsoft Exchange ピアリングを作成する方法について説明します。 またこの記事では、リソースの状態確認、更新、削除およびプロビジョニング解除の方法も紹介します。

必要に応じて、Azure [portal](howto-exchange-portal.md) を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)と [Exchange ピアリングのチュートリアル](walkthrough-exchange-all.md)を確認します。
* Microsoft との Exchange ピアリングが既にあり、Azure リソースに変換されていない場合は、「[PowerShell を使用してレガシの Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-powershell.md)」を参照してください。

### <a name="work-with-azure-powershell"></a>Azure PowerShell を使用する
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Exchange ピアリングを作成・プロビジョニングする

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure アカウントにサインインしてサブスクリプションを選択する
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Exchange ピアリングでサポートされているピアリングの場所の一覧を取得する
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Exchange ピアリングを作成する
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Exchange ピアリングを取得する
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Exchange ピアリングを変更する
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Exchange ピアリングをプロビジョニング解除する

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>次のステップ

* [PowerShell を使用して Direct ピアリングを作成または変更する](howto-direct-powershell.md)
* [PowerShell を使用してレガシの Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>その他のリソース
次のコマンドを実行することで、すべてのパラメーターの詳細な説明を取得できます。

```powershell
Get-Help Get-AzPeering -detailed
```

詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください。
