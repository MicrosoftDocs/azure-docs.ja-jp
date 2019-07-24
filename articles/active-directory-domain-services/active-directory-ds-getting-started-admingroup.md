---
title: Azure Active Directory Domain Services:はじめに | Microsoft Docs
description: Azure Portal を使用して Azure Active Directory Domain Services を有効にする
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ergreenl
ms.openlocfilehash: 734fb5ce641d48800cef68ea79cdb258e44ac267
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867101"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal を使用して Azure Active Directory Domain Services を有効にする

## <a name="task-3-configure-administrative-group"></a>タスク 3: 管理グループを構成する

この構成タスクでは、Azure AD ディレクトリの管理グループを作成します。 この特別な管理グループは、 *AAD DC 管理者*と呼ばれます。 このグループのメンバーには、マネージド ドメインにドメイン参加するコンピューターへの管理権限が付与されます。 ドメインに参加しているコンピューターでは、このグループは管理者グループに追加されます。 さらに、このグループのメンバーは、リモート デスクトップを使用して、ドメインに参加しているコンピューターにリモート接続できます。

> [!NOTE]
> Azure Active Directory Domain Services を使用して作成されたマネージド ドメインでは、ドメイン管理者権限またはエンタープライズ管理者権限がありません。 マネージド ドメインでは、これらの権限はサービスによって予約されており、テナント内でユーザーが使用することはできません。 ただし、この構成タスクで作成した特殊な管理者グループを使用して、特権操作の一部を実行することはできます。 これらの操作には、ドメインへのコンピューターの参加、ドメインに参加しているコンピューターでの管理グループへの所属、グループ ポリシーの構成などが含まれます。
>

管理グループは、ウィザードで Azure AD ディレクトリに自動的に作成されます。 このグループは、"AAD DC Administrators" という名前です。 Azure AD ディレクトリにこの名前のグループが既に存在している場合、ウィザードはこのグループを選択します。 グループ メンバーシップは、**[管理者グループ]** ウィザード ページを使用して構成できます。

1. グループ メンバーシップを構成するには、**[AAD DC Administrators]** をクリックします。

    ![グループ メンバーシップを構成する](./media/getting-started/domain-services-blade-admingroup.png)

2. **[メンバーの​​追加]** をクリックして、Azure AD ディレクトリから管理者グループにユーザーを追加します。

3. 完了したら、**[OK]** をクリックしてウィザードの **[概要]** ページに移動します。

## <a name="configure-synchronization"></a>同期の構成

Azure AD Domain Services では、Azure AD で使用できるすべてのユーザーとグループの完全同期を実行でき、範囲指定された同期を選択して特定のグループのみを同期することもできます。 完全同期を選択すると、範囲指定された同期を後で選択することは**できなくなります**。 範囲指定された同期の詳細については、[Azure AD Domain Services の範囲指定された同期に関する記事](active-directory-ds-scoped-synchronization.md)を参照してください。

### <a name="full-synchronization"></a>完全同期

1. 完全同期の場合は、完全があらかじめ選択されているため、画面の下部にある [OK] をクリックするだけです。
    ![完全同期](./media/active-directory-domain-services-admin-guide/create-sync-all.PNG)

### <a name="scoped-synchronization"></a>範囲指定された同期

1. 同期ボタンを "Scoped (範囲指定)" に切り替えると、グループの選択ページが表示されます。 これにより、マネージド ドメインに同期されるように既に選択されているグループがわかります。
    ![範囲指定された同期](media/active-directory-domain-services-admin-guide/create-sync-scoped.PNG)
2. 上部のナビゲーション バーの **[グループの選択]** をクリックします。 これにより、横にグループ ピッカーが表示されます。 これを使用して、Azure AD Domain Services と同期する追加のグループを選択します。 完了したら、**[選択]** をクリックしてグループ ピッカーを閉じて、選択した一覧にそれらのグループを追加します。
    ![範囲指定された同期のグループの選択](media/active-directory-domain-services-admin-guide/create-sync-scoped-groupselect.PNG)
3. **[OK]** をクリックして概要ページに移動します。

## <a name="deploy-your-managed-domain"></a>マネージド ドメインのデプロイ

1. ウィザードの **[概要]** ページで、マネージド ドメインの構成設定を確認します。 必要に応じて、ウィザードの任意の手順に戻り、変更を加えることができます。 完了したら、**[OK]** をクリックして、新しいマネージド ドメインを作成します。

    ![まとめ](./media/getting-started/domain-services-blade-summary.png)

2. Azure AD Domain Services のデプロイの進行状況を示す通知が表示されます。 デプロイの進行状況を詳しく表示するには、通知をクリックします。

    ![通知 - 進行中のデプロイ](./media/getting-started/domain-services-blade-deployment-in-progress.png)

## <a name="check-the-deployment-status-of-your-managed-domain"></a>マネージド ドメインのデプロイ ステータスの確認

マネージド ドメインのプロビジョニングのプロセスは、最大で 1 時間かかることがあります。

1. デプロイが進行中は、**[リソースの検索]** 検索ボックスで "Domain Services" を検索できます。 検索結果から **[Azure AD Domain Services]** を選択します。 **[Azure AD Domain Services]** ブレードには、プロビジョニング中のマネージド ドメインが一覧表示されます。

    ![プロビジョニング中のマネージド ドメインを見つける](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. マネージド ドメインの詳細を表示するには、マネージド ドメインの名前 (例: "contoso100.com") をクリックします。

    ![Domain Services - プロビジョニング状態](./media/getting-started/domain-services-provisioning-state.png)

3. **[概要]** タブは、マネージド ドメインが現在プロビジョニング中であることを示しています。 完全にプロビジョニングされるまで、マネージド ドメインを構成することはできません。 マネージド ドメインが完全にプロビジョニングされるまでに最大で 1 時間かかる場合があります。

    ![Domain Services - プロビジョニング状態のときの [概要] タブ](./media/getting-started/domain-services-provisioning-state-details.png)

4. マネージド ドメインが完全にプロビジョニングされると、**[概要]** タブには、ドメインの状態が **[実行中]** であることが示されます。

    ![Domain Services - 完全にプロビジョニングされた後の [概要] タブ](./media/getting-started/domain-services-provisioned.png)
    >[!NOTE]
    >プロビジョニング プロセスの間に、Azure AD Domain Services は、お使いのディレクトリ内に "Domain Controller Services" と "AzureActiveDirectoryDomainControllerServices" というエンタープライズ アプリケーションを作成します。 これらのエンタープライズ アプリケーションは、マネージド ドメインのサービスを提供するために使用されます。 どのような場合にも、これらが削除されないことが不可欠です。
    >

5. **[プロパティ]** タブに、仮想ネットワークでドメイン コント ローラーを使用できる 2 つの IP アドレスが表示されます。

    ![Domain Services - 完全にプロビジョニングされた後の [プロパティ] タブ](./media/getting-started/domain-services-provisioned-properties.png)

## <a name="need-help"></a>お困りの際は、

マネージド ドメインの両方のドメイン コントローラーがプロビジョニングされるまでに 1、2 時間かかる場合があります。 デプロイが失敗した場合、または 2 時間以上 "保留中" のままである場合は、[製品チームにお問い合わせください](active-directory-ds-contact-us.md)。

## <a name="next-step"></a>次のステップ

[タスク 4: Azure 仮想ネットワークの DNS 設定を更新する](active-directory-ds-getting-started-dns.md)
