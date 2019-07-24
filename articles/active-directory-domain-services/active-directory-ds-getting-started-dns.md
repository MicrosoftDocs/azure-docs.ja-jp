---
title: Azure Active Directory Domain Services:Azure 仮想ネットワークの DNS 設定を更新する | Microsoft Docs
description: Azure Active Directory Domain Services の概要
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: 4727c24c603e95aeee6214546e25a273aa652f4c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182831"
---
# <a name="enable-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services を有効にする

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>タスク 4: Azure 仮想ネットワークの DNS 設定を更新する
前の構成タスクでは、ディレクトリに対して Azure Active Directory Domain Services を有効にしました。 次に、仮想ネットワーク内のコンピューターがこれらのサービスに接続してそれを使用できるようにします。 この記事では、仮想ネットワーク上で Azure Active Directory Domain Services を利用できる 2 つの IP アドレスを指すように、仮想ネットワークの DNS サーバー設定を更新します。

Azure Active Directory Domain Services を有効にした仮想ネットワークの DNS サーバー設定を更新するには、次の手順に従います。


1. **[概要]** タブの **[必要な構成手順]** に、マネージド ドメインが完全にプロビジョニングされた後で行う一連の手順が表示されます。 最初の構成手順は "**仮想ネットワークの DNS サーバー設定の更新**" です。

    ![ドメイン サービス - [概要] タブ](./media/getting-started/domain-services-provisioned-overview.png)

    > [!TIP]
    > この構成手順が表示されませんか。 仮想ネットワークの DNS サーバーの設定が最新の状態である場合、[概要] タブに [仮想ネットワークの DNS サーバー設定の更新] タイルは表示されません。
    >
    >

2. **[構成]** をクリックして、仮想ネットワークの DNS サーバー設定を更新します。

> [!NOTE]
> 新しい DNS 設定は、再起動するまでネットワーク内の仮想マシンに適用されません。 すぐに更新後の DNS 設定を取得する必要がある場合は、ポータル、PowerShell、または CLI のいずれかの再起動をトリガーします。
>
>

## <a name="next-step"></a>次のステップ
[タスク 5: Azure Active Directory Domain Services とのパスワード同期を有効にする](active-directory-ds-getting-started-password-sync.md)
