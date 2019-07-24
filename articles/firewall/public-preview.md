---
title: Azure Firewall のパブリック プレビューを有効にする
description: Azure PowerShell を使用して Azure Firewall のパブリック プレビューを有効にします
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: fe1b8f9d56b0f4faa0baa25463b2aa29a59715cb
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499592"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Azure Firewall のパブリック プレビューを有効にする

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Azure PowerShell を使用して有効にする

Azure Firewall のパブリック プレビューを有効にするには、次の Azure PowerShell コマンドを使用します。

```PowerShell
Register-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

機能の登録が完了するまで、最長で 30 分かかります。 次の Azure PowerShell コマンドを実行することで、登録状態を確認できます。

```powershell

Get-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
登録が完了した後、次のコマンドを実行します。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>次の手順

- [チュートリアル:Azure portal を使用して Azure Firewall をデプロイして構成する](tutorial-firewall-deploy-portal.md)

