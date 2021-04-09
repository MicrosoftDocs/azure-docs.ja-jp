---
title: チュートリアル - Azure AD Domain Services でレプリカ セットを作成する | Microsoft Docs
description: Azure AD Domain Services でのサービスの回復性のために Azure portal でレプリカ セットを作成して使用する方法について説明します
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: justinha
ms.openlocfilehash: a016287fedbd303a5571100130769ce4299828bc
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798561"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services"></a>チュートリアル:Azure Active Directory Domain Services で回復性または位置情報のためにレプリカ セットを作成して使用する

Azure Active Directory Domain Services (Azure AD DS) のマネージド ドメインの回復性を向上させたり、アプリケーションに地理的に近い場所に追加デプロイしたりするには、"*レプリカ セット*" を使用できます。 Azure AD DS のマネージド ドメインの名前空間 (*aaddscontoso.com* など) には、1 つの初期レプリカ セットが含まれています。 他の Azure リージョンに追加のレプリカ セットを作成する機能により、マネージド ドメインの地理的な回復性が提供されます。

Azure AD DS がサポートされている任意の Azure リージョンの、ピアリングされた任意の仮想ネットワークに、レプリカ セットを追加できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想ネットワークの要件を理解する
> * レプリカ セットを作成する
> * レプリカ セットを削除する

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure Resource Manager デプロイ モデルを使用して作成され、ご利用の Azure AD テナント内で構成された Azure Active Directory Domain Services マネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services のマネージド ドメインを作成して構成][tutorial-create-instance]します。

    > [!IMPORTANT]
    > クラシック デプロイ モデルを使用して作成されたマネージド ドメインでは、レプリカ セットを使用できません。 また、マネージド ドメインに対して *Enterprise* SKU を少なくとも使用する必要があります。 必要に応じて、[マネージド ドメインの SKU を変更][howto-change-sku]します。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

このチュートリアルでは、Azure portal を使用してレプリカ セットの作成と管理を行います。 最初に、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="networking-considerations"></a>ネットワークに関する考慮事項

レプリカ セットがホストされている仮想ネットワークは、相互に通信できる必要があります。 Azure AD DS に依存するアプリケーションやサービスでは、レプリカ セットがホストされている仮想ネットワークへのネットワーク接続も必要です。 完全にメッシュされたネットワークを作成するには、すべての仮想ネットワークの間に、Azure 仮想ネットワーク ピアリングを構成する必要があります。 これらのピアリングにより、レプリカ セット間の有効なサイト内レプリケーションが可能になります。

Azure AD DS でレプリカ セットを使用する前に、Azure 仮想ネットワークに関する次の要件を確認してください。

* 仮想ネットワークのピアリングとルーティングを可能にするため、IP アドレス空間が重複しないようにします。
* シナリオをサポートするのに十分な IP アドレスを使用してサブネットを作成します。
* Azure AD DS に専用のサブネットがあることを確認します。 この仮想ネットワーク サブネットをアプリケーションの VM およびサービスと共有しないでください。
* ピアリングされた仮想ネットワークは推移的ではありません。

> [!TIP]
> Azure portal でレプリカ セットを作成すると、仮想ネットワーク間のネットワーク ピアリングが自動的に作成されます。
>
> 必要な場合は、Azure portal でレプリカ セットを追加するときに、仮想ネットワークとサブネットを作成できます。 または、レプリカ セットのターゲット リージョンにある既存の仮想ネットワーク リソースを選択し、まだ存在していない場合はピアリングを自動的に作成することができます。

## <a name="create-a-replica-set"></a>レプリカ セットを作成する

*aaddscontoso.com* などのマネージド ドメインを作成すると、初期レプリカ セットが作成されます。 追加のレプリカ セットは同じ名前空間と構成を共有します。 構成、ユーザー ID と資格情報、グループ、グループ ポリシー オブジェクト、コンピューター オブジェクト、その他の変更など、Azure AD DS に対する変更は、AD DS レプリケーションを使用して、マネージド ドメイン内のすべてのレプリカ セットに適用されます。

このチュートリアルでは、初期 Azure AD DS レプリカ セットとは異なる Azure リージョンに、追加のレプリカ セットを作成します。

追加のレプリカ セットを作成するには、次の手順のようにします。

1. Azure portal で、**Azure AD Domain Services** を検索して選択します。
1. 目的のマネージド ドメインを選択します (例: *aaddscontoso.com* )。
1. 左側で **[レプリカ セット]** を選択します。 次のスクリーンショットの例に示すように、各マネージド ドメインには、選択したリージョンの 1 つの初期レプリカ セットが含まれています。

    ![Azure portal でのレプリカ セットの表示と追加の例のスクリーンショット](./media/tutorial-create-replica-set/replica-set-list.png)

    追加のレプリカ セットを作成するには、 **[+ 追加]** を選択します。

1. *[レプリカ セットの追加]* ウィンドウで、 *[米国東部]* などのターゲット リージョンを選択します。

    ターゲット リージョンで仮想ネットワーク (*vnet-eastus* など) を選択してから、サブネット (*aadds-subnet* など) を選択します。 必要な場合は、 **[新規作成]** を選択してターゲット リージョンに仮想ネットワークを追加します。次に、 **[管理]** を選択して、Azure AD DS 用のサブネットを作成します。

    まだ存在していない場合は、既存のマネージド ドメインの仮想ネットワークとターゲット仮想ネットワークの間に、Azure 仮想ネットワーク ピアリングが自動的に作成されます。

    次の例のスクリーンショットでは、"*米国東部*" に新しいレプリカ セットを作成するプロセスが示されています。

    ![Azure portal でのレプリカ セットの作成の例のスクリーンショット](./media/tutorial-create-replica-set/create-replica-set.png)

1. 準備ができたら、 **[保存]** を選択します。

レプリカ セットを作成するプロセスでは、リソースがターゲット リージョンに作成されるため、多少時間がかかります。 その後、マネージド ドメイン自体が AD DS レプリケーションを使用してレプリケートされます。

次のスクリーンショットの例に示すように、デプロイが行われている間、レプリカ セットは "*プロビジョニング中*" と報告されます。 完了すると、レプリカ セットは "*実行中*" と表示されます。

![Azure portal でのレプリカ セットのデプロイ状態の例のスクリーンショット](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>レプリカ セットを削除する

現在、マネージド ドメインのレプリカは、最初のレプリカ セットと 3 つの追加レプリカ セットの 4 つに制限されています。 レプリカ セットが不要になった場合、または別のリージョンにレプリカ セットを作成する必要がある場合は、不要なレプリカ セットを削除できます。

> [!IMPORTANT]
> マネージド ドメインで最後に残ったレプリカ セットを削除することはできません。

レプリカ セットを削除するには、次の手順のようにします。

1. Azure portal で、**Azure AD Domain Services** を検索して選択します。
1. 目的のマネージド ドメインを選択します (例: *aaddscontoso.com* )。
1. 左側で **[レプリカ セット]** を選択します。 レプリカ セットの一覧で、削除するレプリカ セットの横にある **[...]** コンテキスト メニューを選択します。
1. コンテキスト メニューから **[削除]** を選択し、レプリカ セットを削除することを確認します。

> [!NOTE]
> レプリカ セットの削除操作には、時間がかかる場合があります。

レプリカ セットによって使用されていた仮想ネットワークまたはピアリングが不要になった場合は、それらのリソースを削除することもできます。 削除する前に、他のリージョンの他のアプリケーション リソースでネットワーク接続が必要ではないことを確認してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 仮想ネットワーク ピアリングを構成する
> * 別の地理的リージョンにレプリカ セットを作成する
> * レプリカ セットを削除する

詳細な概念については、Azure AD DS でのレプリカ セットの動作に関するページを参照してください。

> [!div class="nextstepaction"]
> [レプリカ セットの概念と機能][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
