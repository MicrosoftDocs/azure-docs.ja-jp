---
title: 最小 TLS バージョンを構成する - マネージド インスタンス
description: マネージド インスタンス用に最小 TLS バージョンを構成する方法について学習する
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 17d430946f3cba1aa4680d1eaf8979fa4338bc22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788402"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance で最小 TLS バージョンを構成する
最小[トランスポート層セキュリティ (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) バージョン設定を使用すると、お客様は Azure SQL Managed Instance で使用される TLS のバージョンを制御することができます。

現時点では、TLS 1.0、1.1、1.2 がサポートされています。 最小 TLS バージョンを設定すると、以降の新しい TLS バージョンがサポートされるようになります。 たとえば、1.1 よりも大きい TLS バージョンを選択する ことは、TLS 1.1 および 1.2 との接続のみが受け入れられ、TLS 1.0 は拒否されることを意味します。 テスト後、アプリケーションでこれがサポートされていることを確認するために、最小 TLS バージョンを 1.2 に設定することをお勧めします。これは、以前のバージョンの脆弱性に対する修正プログラムが含まれており、Azure SQL Managed Instance でサポートされる TLS の最高バージョンであるためです。

以前のバージョンの TLS に依存するアプリケーションを使用しているお客様には、アプリケーションの要件に従って最小 TLS バージョンを設定することをお勧めします。 暗号化されていない接続を使用して接続するアプリケーションに依存しているお客様は、最少 TLS バージョンを設定しないことをお勧めします。 

詳細については、「[SQL Database の接続に関する TLS の考慮事項](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)」を参照してください。

最小 TLS バージョンを設定すると、サーバーの最小 TLS バージョンより低い TLS バージョンを使用しているクライアントからのログイン試行は、次のエラーで失敗します。

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>PowerShell を使用して最小 TLS バージョンを設定する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 次のスクリプトは [Azure PowerShell](/powershell/azure/install-az-ps) モジュールを必要とします。

次の PowerShell スクリプトは、**最少 TLS バージョン** プロパティをインスタンス レベルで `Get` および `Set` する方法を示しています。

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Azure CLI 経由で最小 TLS バージョンを設定する

> [!IMPORTANT]
> このセクションのすべてのスクリプトでは [Azure CLI](/cli/azure/install-azure-cli) が必要です。

### <a name="azure-cli-in-a-bash-shell"></a>bash シェルでの Azure CLI

次の CLI スクリプトは、bash シェルでの **最少 TLS バージョン** の変更方法を示しています。

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```