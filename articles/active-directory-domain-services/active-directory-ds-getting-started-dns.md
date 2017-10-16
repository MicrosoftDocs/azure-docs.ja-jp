---
title: "Azure Active Directory Domain Services: Azure 仮想ネットワークの DNS 設定を更新する | Microsoft Docs"
description: "Azure Active Directory Domain Services の概要"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: ab8e3215e8e73d3943af06cffafa730cf1b7744b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="enable-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services を有効にする

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>タスク 4: Azure 仮想ネットワークの DNS 設定を更新する
前の構成タスクでは、ディレクトリに対して Azure Active Directory Domain Services を有効にしました。 次のタスクでは、仮想ネットワーク内のコンピューターがこれらのサービスに接続してそれを使用できるようにします。 この記事では、仮想ネットワーク上で Azure Active Directory Domain Services を利用できる 2 つの IP アドレスを指すように、仮想ネットワークの DNS サーバー設定を更新します。

Azure Active Directory Domain Services を有効にした仮想ネットワークの DNS サーバー設定を更新するには、次の手順に従います。

1. **[概要]** タブの **[必要な構成手順]** に、管理対象ドメインが完全にプロビジョニングされた後で行う一連の手順が表示されます。 最初の構成手順は "**仮想ネットワークの DNS サーバー設定の更新**" です。

    ![Domain Services - 完全にプロビジョニングされた後の [概要] タブ](./media/getting-started/domain-services-provisioned-overview.png)

2. 対象のドメインが完全にプロビジョニングされると、このタイルに 2 つの IP アドレスが表示されます。 その各 IP アドレスは、管理対象ドメインのドメイン コントローラーに相当します。

3. 1 つ目の IP アドレスの横にあるコピー ボタンをクリックして、そのアドレスをクリップボードにコピーします。 次に、**[DNS サーバーの構成]** をクリックします。

4. **[DNS サーバー]** ブレードの **[DNS サーバーの追加]** ボックスに 1 つ目の IP アドレスを貼り付けます。 左へ水平スクロールして 2 つ目の IP アドレスをコピーし、**[DNS サーバーの追加]** ボックスに貼り付けます。

    ![Domain Services - DNS の更新](./media/getting-started/domain-services-update-dns.png)

5. 仮想ネットワークの DNS サーバーを更新したら、**[保存]** をクリックします。

> [!NOTE]
> 新しい DNS 設定は、再起動するまでネットワーク内の仮想マシンに適用されません。 すぐに更新後の DNS 設定を取得する必要がある場合は、ポータル、PowerShell、または CLI のいずれかの再起動をトリガーします。
>
>

## <a name="next-step"></a>次のステップ
[タスク 5: Azure Active Directory Domain Services とのパスワード同期を有効にする](active-directory-ds-getting-started-password-sync.md)
