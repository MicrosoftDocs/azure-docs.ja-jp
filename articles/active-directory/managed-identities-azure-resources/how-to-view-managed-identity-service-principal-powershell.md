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
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 15ccc0faa4d74a2ef95aca00a6257f27b9a209c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91611947"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>PowerShell を使用してマネージド ID のサービス プリンシパルを表示する

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、PowerShell を使用してマネージド ID のサービス プリンシパルを表示する方法について説明します。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。
- まだ Azure アカウントを持っていない場合は、[無料アカウントを新規登録](https://azure.microsoft.com/free/)してください。
- [仮想マシン](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)または[アプリケーション](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity)でシステム割り当ての ID を有効にします。
- サンプル スクリプトを実行するには、次の 2 つのオプションがあります。
    - [Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。これは、コード ブロックの右上隅にある **[Try It]\(使ってみる\)** ボタンを使用して開くことができます。
    - 最新バージョンの [Azure PowerShell](/powershell/azure/install-az-ps) をインストールしてスクリプトをローカルで実行した後、`Connect-AzAccount` を使用して Azure にサインインします。

## <a name="view-the-service-principal"></a>サービス プリンシパルを表示する

次のコマンドは、システム割り当ての ID を有効にして VM またはアプリケーションのサービス プリンシパルを表示する方法を示しています。 `<Azure resource name>` を独自の値に置き換えます。

```azurepowershell-interactive
Get-AzADServicePrincipal -DisplayName <Azure resource name>
```

## <a name="next-steps"></a>次の手順

PowerShell を使用して Azure AD サービス プリンシパルを表示する方法の詳細については、[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) に関するページを参照してください。
