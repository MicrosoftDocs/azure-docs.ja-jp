---
title: "Azure ポータルを使用して Azure VM で MSI を構成する方法"
description: "Azure ポータルを使用して、Azure VM で管理対象サービス ID (MSI) を構成する方法をステップ バイ ステップで説明します。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: bryanla
ms.openlocfilehash: 169417530da21e0c8c58cbf770fd1d26660387f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Azure ポータルを使用して、VM 管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure Portal を使用して Azure VM の MSI を有効化および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Azure VM の作成中に MSI を有効にする

この記事の執筆時点で、Azure ポータルで VM の作成中に MSI を有効にすることはサポートされていません。 代わりに、最初に VM を作成するには、VM 作成に関する次のいずれかのクイックスタートの記事を参照してください。

- [Azure Portal で Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Azure Portal で Linux 仮想マシンを作成する](../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

続いて、次のセクションに進み、VM で MSI を有効にする方法の詳細を参照します。

## <a name="enable-msi-on-an-existing-azure-vm"></a>既存の Azure VM で MSI を有効にする

最初にプロビジョンされた VM (MSI なし) が存在する場合は、次のようにします。

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 また、お使いのアカウントが、「仮想マシンの共同作業者」など、VM 上の書き込みアクセス許可が提供されるロールに属していることを確認します。

2. 目的の仮想マシンに移動します。

2. [構成] ページをクリックして、[管理対象サービス ID] で [はい] を選択して VM で MSI を有効にし、**[保存]** をクリックします。 この操作が完了するまでに 60 秒以上かかる場合があります。

   ![[構成] ページのスクリーンショット](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Azure VM から MSI を削除する

MSI が不要になった仮想マシンがある場合は、次のようにします。

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 また、お使いのアカウントが、「仮想マシンの共同作業者」など、VM 上の書き込みアクセス許可が提供されるロールに属していることを確認します。

2. 目的の仮想マシンに移動します。

3. [構成] ページをクリックして、[管理対象サービス ID] で [いいえ] を選択して VM から MSI を削除し、**[保存]** をクリックします。 この操作が完了するまでに 60 秒以上かかる場合があります。

   ![[構成] ページのスクリーンショット](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>関連コンテンツ

- MSI の概要については、[管理対象サービス ID の概要](msi-overview.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

- Azure ポータルを使用して、Azure VM の MSI に[別の Azure リソースへのアクセス](msi-howto-assign-access-portal.md)を許可します。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
