---
title: Azure SDK を使用して Azure VM でマネージド ID を使用する - Azure AD
description: Azure リソースのマネージド ID を持つ Azure VM に対して Azure SDK を使用するコード サンプル。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eb5f1d81874c8303d095aec320f60cc9ae6a7dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360566"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Azure SDK を使用して Azure VM で Azure リソースのマネージド ID を使用する方法 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
この記事では、各 Azure SDK の Azure リソースのマネージド ID のサポートの使用例を示す SDK サンプルの一覧を示します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - この記事のすべてのサンプル コード/スクリプトは、Azure リソースのマネージド ID が有効になっている VM でクライアントが実行されていることを前提としています。 お使いの VM にリモート接続するには、Azure ポータルで VM への "接続" 機能を使用します。 VM で Azure リソースのマネージド ID を有効にする方法の詳細については、「[Azure Portal を使用して VM 上に Azure リソースのマネージド ID を構成する](qs-configure-portal-windows-vm.md)」、または関連する記事 (PowerShell、CLI、テンプレート、または Azure SDK の使用) のいずれかを参照してください。 

## <a name="sdk-code-samples"></a>SDK コード サンプル

| SDK             | コード サンプル |
| --------------- | ----------- |
| .NET            | [Azure リソースのマネージド ID を使用して、Windows VM から Azure Resource Manager テンプレートをデプロイする](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Azure リソースのマネージド ID を使用して、Linux VM から Azure サービスを呼び出す](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Azure リソースのマネージド ID を使用して、リソースを管理する](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Azure リソースのマネージド ID を使用して、VM 内から単純に認証する](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Azure リソースのマネージド ID が有効になっている VM からリソースを管理する](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>次のステップ

- ライブラリのダウンロード、ドキュメントなどを含む Azure SDK リソースの完全な一覧については、「[Azure SDK](https://azure.microsoft.com/downloads/)」を参照してください。
- Azure VM 上で Azure リソースのマネージド ID を有効にするには、「[Configure managed identities for Azure resources on a VM using the Azure portal](qs-configure-portal-windows-vm.md)」 (Azure portal を使用して VM 上で Azure リソースのマネージド ID を構成する) を参照してください。








