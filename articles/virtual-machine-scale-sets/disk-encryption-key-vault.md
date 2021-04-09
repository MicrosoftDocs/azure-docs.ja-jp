---
title: Azure Disk Encryption 用のキー コンテナーの作成と構成
description: この記事では、Azure Disk Encryption で使用するためのキー コンテナーを作成および構成する手順について説明します
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3d30d506ae3b49b2e247ee77ef17d101f94854c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501016"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>Azure Disk Encryption のキー コンテナーを作成して構成する

Azure Disk Encryption では、Azure Key Vault を使用して、ディスク暗号化キーとシークレットを制御および管理します。  キー コンテナーの詳細については、「[Azure Key Vault の概要](../key-vault/general/overview.md)」と「[キー コンテナーのセキュリティ保護](../key-vault/general/secure-your-key-vault.md)」を参照してください。

Azure Disk Encryption で使用するためのキー コンテナーの作成と構成には、次の 3 つの手順が必要です。

1. 必要に応じて、リソース グループを作成する。
2. キー コンテナーを作成する。 
3. キー コンテナーの高度なアクセス ポリシーを設定する。

これらの手順は、次のクイックスタートで説明されています。

また、必要に応じて、キー暗号化キー (KEK) を生成またはインポートすることもできます。

## <a name="install-tools-and-connect-to-azure"></a>ツールをインストールし、Azure に接続する

この記事の手順を完了するには、[Azure CLI](/cli/azure/)、[Azure PowerShell Az モジュール](/powershell/azure/)、または [Azure portal](https://portal.azure.com) のいずれかを使用します。

### <a name="connect-to-your-azure-account"></a>Azure アカウントに接続する

Azure CLI または Azure PowerShell を使用する前に、まず Azure サブスクリプションに接続する必要があります。 これは、[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)、[Azure PowerShell を使用してサインインする](/powershell/azure/authenticate-azureps)、または、メッセージが表示されたときに資格情報を Azure portal に提供する、のいずれかの方法で行います。

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>次のステップ

- [Azure Disk Encryption の概要](disk-encryption-overview.md)
- [Azure CLI を使用して仮想マシン スケール セットを暗号化する](disk-encryption-cli.md)
- [Azure PowerShell を使用して仮想マシン スケール セットを暗号化する](disk-encryption-powershell.md)
