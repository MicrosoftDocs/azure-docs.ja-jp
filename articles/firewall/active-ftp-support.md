---
title: Azure Firewall のアクティブ FTP のサポート
description: 既定では、アクティブ FTP は Azure Firewall で無効になっています。 PowerShell、CLI、および ARM テンプレートを使用して有効にできます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: victorh
ms.openlocfilehash: adbc2a9eb6cd3b054df84911604143ddb711ad20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499137"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Firewall のアクティブ FTP のサポート

アクティブ FTP では、FTP サーバーは、指定された FTP クライアント データ ポートへのデータ接続を開始します。 クライアント側ネットワーク上のファイアウォールは、通常、内部クライアント ポートに対する外部接続要求をブロックします。 詳細については、[アクティブ FTP とパッシブ FTP の明確な説明](https://slacksite.com/other/ftp.html)に関する記事を参照してください。

既定では、Azure Firewall のアクティブ FTP サポートは、FTP `PORT` コマンドを使用した FTP バウンス攻撃から保護するために無効になっています。 ただし、Azure PowerShell、Azure CLI、または Azure ARM テンプレートを使用してデプロイする場合は、アクティブ FTP を有効にすることができます。


## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してデプロイするには、`AllowActiveFTP` パラメーターを使用します。 詳細については、[アクティブ FTP の許可を使用してファイアウォールを作成する](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-)方法に関する記事を参照してください。

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してデプロイするには、`--allow-active-ftp` パラメーターを使用します。 詳細については、[Az.Network のファイアウォールの作成](/cli/azure/ext/azure-firewall/network/firewall#ext_azure_firewall_az_network_firewall_create-optional-parameters)に関する記事を参照してください。 

## <a name="azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) テンプレート

ARM テンプレートを使用してデプロイするには、`AdditionalProperties` フィールドを使用します。

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
詳細については、[Microsoft.Network の azureFirewalls](/azure/templates/microsoft.network/azurefirewalls) に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Firewall のデプロイ方法については、「[チュートリアル: Azure PowerShell を使用して Azure Firewall のデプロイと構成を行う](deploy-ps.md)」を参照してください。