---
title: 仮想マシン スケール セットでマネージド ID を構成する - Azure AD
description: Azure portal を使用して仮想マシン スケール セットで Azure リソースのマネージド ID を構成するための詳細な手順。
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73c2f4167d6ce5465a879f6b2ef7e96d91065c4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590921"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-vmss-using-the-azure-portal"></a>Azure portal を使用して仮想マシン スケール セット (VMSS) で Azure リソースのマネージド ID を構成する

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、PowerShell を使用して、仮想マシン スケール セットで Azure リソースのマネージド ID に対して次の操作を実行する方法について説明します。

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するアカウントには、次の Azure ロールの割り当てが必要です。

    > [!NOTE]
    > Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

    - 仮想マシン スケール セットからシステム割り当てマネージド ID を有効化および削除するための[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)。

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

このセクションでは、Azure portal を使用してシステム割り当てマネージド ID を有効および無効にする方法について説明します。

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの作成中にシステム割り当てマネージド ID を有効にする

現在 Azure portal では、仮想マシン スケール セット作成中のシステム割り当てマネージド ID の有効化はサポートされていません。 代わりに、仮想マシン スケール セットの作成に関する次のクイック スタート記事を参照して、まず仮想マシン スケール セットを作成してください。そして、仮想マシン スケール セットでシステム割り当てマネージド ID を有効にする方法について詳しく説明している、次のセクションに進んでください。

- [Azure Portal での仮想マシン スケール セットの作成](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>既存の仮想マシン スケール セットでシステム割り当てマネージド ID を有効にする

もともとシステム割り当てマネージド ID がプロビジョニングされていなかった仮想マシン スケール セットでそれを有効にするには:

1. 仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。

2. 目的の仮想マシン スケール セットに移動します。

3. **[システム割り当て済み]** にある **[状態]** で **[オン]** を選択して、 **[保存]** をクリックします。

   ![[ID (プレビュー)] ページのスクリーンショット。[システム割り当て済み] が選択され、[状態] が [オン] になっており、[保存] ボタンが強調表示されている。](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>仮想マシン スケール セットからシステム割り当てマネージド ID を削除する

システム割り当てマネージド ID が不要になった仮想マシン スケール セットがある場合:

1. 仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 また、お使いのアカウントが、仮想マシン スケール セット上の書き込みアクセス許可が提供されるロールに属していることを確認します。

2. 目的の仮想マシン スケール セットに移動します。

3. **[システム割り当て済み]** にある **[状態]** で **[オフ]** を選択して、 **[保存]** をクリックします。

   ![[構成] ページのスクリーンショット](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

このセクションでは、Azure portal を使用して、仮想マシン スケール セットとの間でユーザー割り当てマネージド ID を追加および削除する方法について説明します。

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの作成中にユーザー割り当てマネージド ID を割り当てる

現在 Azure portal では、仮想マシン スケール セット作成中のユーザー割り当てマネージド ID の割り当てはサポートされていません。 代わりに、仮想マシン スケール セットの作成に関する次のクイック スタート記事を参照して、まず仮想マシン スケール セットを作成してください。そして、その仮想マシン スケール セットにユーザー割り当てマネージド ID を割り当てる方法について詳しく説明している、次のセクションに進んでください。

- [Azure Portal での仮想マシン スケール セットの作成](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>既存の仮想マシン スケール セットにユーザー割り当てマネージド ID を割り当てる

1. 仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。
2. 目的の仮想マシン スケール セットに移動して、 **[ID]** 、 **[ユーザー割り当て済み]** 、 **\+[追加]** の順にクリックします。

   ![[ID] ページのスクリーンショット。[ユーザー割り当て済み] が選択され、[追加] ボタンが強調表示されている。](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. 仮想マシン スケール セットに追加したいユーザー割り当て ID をクリックして、 **[追加]** をクリックします。
   
   ![VMSS へのユーザー割り当て ID の追加](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>仮想マシン スケール セットからユーザー割り当てマネージド ID を削除する

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。
2. 目的の仮想マシン スケール セットに移動します。次に、 **[ID]** 、 **[ユーザー割り当て済み]** 、削除したいユーザー割り当てマネージド ID の名前を順にクリックしてから、 **[削除]** をクリックします (確認ウィンドウで **[はい]** をクリックします)。

   ![VMSS からのユーザー割り当て ID の削除](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>次のステップ

- Azure portal を使用して、Azure 仮想マシン スケール セットのマネージド ID に[別の Azure リソースへのアクセス](howto-assign-access-portal.md)を許可します。
