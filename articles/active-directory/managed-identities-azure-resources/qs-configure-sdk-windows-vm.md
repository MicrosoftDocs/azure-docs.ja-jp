---
title: SDK を使用して VM のマネージド ID を構成する - Azure AD
description: Azure SDK を使用して、Azure VM で Azure リソースのマネージド ID を構成および使用するため手順について説明します。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41d1be35d2add24cbbc171c3da3bd4d06bce4337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93359665"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Azure SDK で Azure リソースのマネージド ID を使用して VM を構成する

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure リソースのマネージド ID は、Azure Active Directory (AD) で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure SDK を使用して Azure VM のマネージド ID を有効にする方法と削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure リソースのマネージド ID をサポートする Azure SDK 

Azure は、一連の [Azure SDK](https://azure.microsoft.com/downloads) によって、複数のプログラミング プラットフォームをサポートしています。 その一部は、Azure リソースのマネージド ID をサポートするために更新されています。また、使用方法を示す対応するサンプルが用意されています。 次の一覧は、サポートが追加されると更新されます。

| SDK | サンプル |
| --- | ------ | 
| .NET   | [Azure リソースのマネージド ID が有効な VM からリソースを管理する](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Azure リソースのマネージド ID が有効な VM からストレージを管理する](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [システム割り当てマネージド ID が有効な VM を作成する](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [システム割り当てマネージド ID が有効な VM を作成する](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [システム割り当て ID が有効な Azure VM を作成する](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>次のステップ

- Azure portal、PowerShell、CLI、およびリソース テンプレートを使用する方法については、**Azure VM 用の ID の構成** に関するページを参照してください。
