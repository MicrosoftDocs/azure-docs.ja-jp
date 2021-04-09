---
title: マネージド ID のサービス プリンシパルを表示する - Azure CLI - Azure AD
description: Azure CLI を使用してマネージド ID のサービス プリンシパルを表示するための段階的手順。
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
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce3a35562bdef34f44ef1093a3196ea7afb0bd9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92892045"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Azure CLI を使用してマネージド ID のサービス プリンシパルを表示する

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure CLI を使用してマネージド ID のサービス プリンシパルを表示する方法について説明します。

まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID について不明な場合は、「[Azure リソースのマネージド ID とは](overview.md)」を参照してください。

- [仮想マシン](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)または[アプリケーション](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity)でシステム割り当ての ID を有効にします。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="view-the-service-principal"></a>サービス プリンシパルを表示する

次のコマンドは、マネージド ID を有効にして VM またはアプリケーションのサービス プリンシパルを表示する方法を示しています。 `<Azure resource name>` を独自の値に置き換えます。

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>次のステップ

Azure CLI を使用して Azure AD サービス プリンシパルを管理する方法の詳細については、[az ad sp](/cli/azure/ad/sp) を参照してください。
