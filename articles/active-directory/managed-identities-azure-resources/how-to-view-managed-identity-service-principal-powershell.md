---
title: PowerShell を使用してマネージド ID のサービス プリンシパルを表示する - Azure AD
description: PowerShell を使用してマネージド ID のサービス プリンシパルを表示するための段階的手順。
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
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a9da4689a1b5579f90a1df0feb487e50d57a9d98
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269218"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>PowerShell を使用してマネージド ID のサービス プリンシパルを表示する

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、PowerShell を使用してマネージド ID のサービス プリンシパルを表示する方法について説明します。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。
- まだ Azure アカウントを持っていない場合は、[無料アカウントを新規登録](https://azure.microsoft.com/free/)してください。
- [仮想マシン](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)または[アプリケーション](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity)でシステム割り当ての ID を有効にします。
- 最新バージョンの [Azure PowerShell](/powershell/azure/install-az-ps) をインストールします

## <a name="view-the-service-principal"></a>サービス プリンシパルを表示する

次のコマンドは、システム割り当ての ID を有効にして VM またはアプリケーションのサービス プリンシパルを表示する方法を示しています。 `<VM or application name>` を独自の値に置き換えます。

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>次のステップ

PowerShell を使用して Azure AD サービス プリンシパルを表示する方法の詳細については、[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) に関するページを参照してください。