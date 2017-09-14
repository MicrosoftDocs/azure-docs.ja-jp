---
title: "Azure Active Directory Domain Services: 概要 | Microsoft Docs"
description: "Azure Portal を使って Azure Active Directory Domain Services を有効にする (プレビュー)"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f87bcf33d3b1eb21c7d84814e4c4086f664e293d
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal-preview"></a>Azure Portal を使って Azure Active Directory Domain Services を有効にする (プレビュー)


## <a name="task-3-configure-administrative-group"></a>タスク 3: 管理グループを構成する
この構成タスクでは、Azure AD ディレクトリの管理グループを作成します。 この特別な管理グループは、 *AAD DC 管理者*と呼ばれます。 このグループのメンバーには、管理対象ドメインにドメイン参加するコンピューターへの管理権限が付与されます。 ドメインに参加しているコンピューターでは、このグループは管理者グループに追加されます。 さらに、このグループのメンバーは、リモート デスクトップを使用して、ドメインに参加しているコンピューターにリモート接続できます。

> [!NOTE]
> Azure Active Directory Domain Services を使用して作成された管理対象ドメインでは、ドメイン管理者権限またはエンタープライズ管理者権限がありません。 管理対象ドメインでは、これらの権限はサービスによって予約されており、テナント内でユーザーが使用することはできません。 ただし、この構成タスクで作成した特殊な管理者グループを使用して、特権操作の一部を実行することはできます。 これらの操作には、ドメインへのコンピューターの参加、ドメインに参加しているコンピューターでの管理グループへの所属、グループ ポリシーの構成などが含まれます。
>

管理グループは、ウィザードで Azure AD ディレクトリに自動的に作成されます。 このグループは、"AAD DC Administrators" という名前です。 Azure AD ディレクトリにこの名前のグループが既に存在している場合、ウィザードはこのグループを選択します。 グループ メンバーシップは、**[管理者グループ]** ウィザード ページを使用して構成できます。

1. グループ メンバーシップを構成するには、**[AAD DC Administrators]** をクリックします。

    ![グループ メンバーシップを構成する](./media/getting-started/domain-services-blade-admingroup.png)

2. **[メンバーの​​追加]** をクリックして、Azure AD ディレクトリから管理者グループにユーザーを追加します。

3. 完了したら、**[OK]** をクリックしてウィザードの **[概要]** ページに移動します。

4. ウィザードの **[概要]** ページで、管理対象ドメインの構成設定を確認します。 必要に応じて、ウィザードの任意の手順に戻り、変更を加えることができます。 完了したら、**[OK]** をクリックして、新しい管理対象ドメインを作成します。

    ![概要](./media/getting-started/domain-services-blade-summary.png)

5. Azure AD Domain Services のデプロイの進行状況を示す通知が表示されます。 デプロイの進行状況を詳しく表示するには、通知をクリックします。

    ![通知 - 進行中のデプロイ](./media/getting-started/domain-services-blade-deployment-in-progress.png)


## <a name="provision-your-managed-domain"></a>管理対象ドメインをプロビジョニングする
管理対象ドメインのプロビジョニングのプロセスは、最大で 1 時間かかることがあります。

1. デプロイが進行中は、**[リソースの検索]** 検索ボックスで "Domain Services" を検索できます。 検索結果から **[Azure AD Domain Services]** を選択します。 **[Azure AD Domain Services]** ブレードには、プロビジョニング中の管理対象ドメインが一覧表示されます。

    ![プロビジョニング中の管理対象ドメインを見つける](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. ドメインの詳細を表示するには、管理対象ドメインの名前 (例: "contoso100.com") をクリックします。

    ![Domain Services - プロビジョニング状態](./media/getting-started/domain-services-provisioning-state.png)

3. **[概要]** タブは、ドメインが現在プロビジョニング中であることを示しています。 完全にプロビジョニングされるまで、管理対象ドメインを構成することはできません。 管理対象ドメインが完全にプロビジョニングされるまでに最大で 1 時間かかる場合があります。

    ![Domain Services - プロビジョニング状態のときの [概要] タブ ](./media/getting-started/domain-services-provisioning-state-details.png)

4. 管理対象ドメインが完全にプロビジョニングされると、**[概要]** タブには、ドメインの状態が **[実行中]** であることが示されます。

    ![Domain Services - 完全にプロビジョニングされた後の [概要] タブ](./media/getting-started/domain-services-provisioned.png)

5. **[プロパティ]** タブに、仮想ネットワークでドメイン コント ローラーを使用できる 2 つの IP アドレスが表示されます。

    ![Domain Services - 完全にプロビジョニングされた後の [プロパティ] タブ](./media/getting-started/domain-services-provisioned-properties.png)


## <a name="next-step"></a>次のステップ
[タスク 4: Azure 仮想ネットワークの DNS 設定を更新する](active-directory-ds-getting-started-dns.md)

