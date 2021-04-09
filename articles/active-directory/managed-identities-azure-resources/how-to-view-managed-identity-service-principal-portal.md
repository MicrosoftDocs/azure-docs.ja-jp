---
title: Azure portal でマネージド ID のサービス プリンシパルを表示する - Azure AD
description: Azure portal でマネージド ID のサービス プリンシパルを表示するための段階的手順。
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
ms.openlocfilehash: 9b25f2691646192faf4d4c133835b456038abd94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003737"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Azure portal でマネージド ID のサービス プリンシパルを表示する

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure portal でマネージド ID のサービス プリンシパルを表示する方法について説明します。

 > [!NOTE] 
 > サービス プリンシパルは、エンタープライズ アプリケーションです。 

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。
- まだ Azure アカウントを持っていない場合は、[無料アカウントを新規登録](https://azure.microsoft.com/free/)してください。
- [仮想マシン](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)または[アプリケーション](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity)でシステム割り当ての ID を有効にします。

## <a name="view-the-service-principal"></a>サービス プリンシパルを表示する

この手順を行うと、VM のサービス プリンシパルが表示されます。また、システム割り当ての ID が有効になっていることを確認できます。アプリケーションの場合も同じ手順が適用されます。

1. **[Azure Active Directory]** をクリックしてから、 **[エンタープライズ アプリケーション]** をクリックします。
2. **[アプリケーションの種類]** で **[すべてのアプリケーション]** を選択して、 **[適用]** をクリックします。
3. 検索フィルター ボックスに、マネージド ID が有効になっている Azure リソースの名前を入力するか、表示された一覧から選択します。

   ![ポータルでマネージド ID サービス プリンシパルを表示する](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>次のステップ

[Azure リソースのマネージド ID](./overview.md)