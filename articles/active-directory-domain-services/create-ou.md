---
title: Azure AD Domain Services の組織単位 (OU) を作成する | Microsoft Docs
description: Azure AD Domain Services のマネージド ドメインにカスタム組織単位 (OU) を作成して管理する方法について説明します。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: iainfou
ms.openlocfilehash: a3f9ad20e4bfba6e0bb858c82ccce73bb687a826
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69613110"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインに組織単位 (OU) を作成する

Active Directory Domain Services (AD DS) の組織単位 (OU) を使用すると、ユーザー アカウント、サービス アカウント、コンピューター アカウントなどのオブジェクトを論理的にグループ化できます。 その後、特定の OU に管理者を割り当て、グループ ポリシーを適用して対象となる構成設定を強制できます。

Azure AD DS マネージド ドメインには、*AADDC Computers* と *AADDC Users* の 2 つの組み込みの OU が含まれています。 *AADDC Computers* OU には、マネージド ドメインに参加しているすべてのコンピューターに関するコンピューター オブジェクトが含まれています。 *AADDC Users* OU には、Azure AD テナントから同期されたユーザーとグループが含まれています。 Azure AD DS を使用するワークロードを作成して実行するときは、各アプリケーションが自身を認証するためのサービス アカウントを作成することが必要になる場合があります。 これらのサービス アカウントを整理するために、多くの場合は、Azure AD DS マネージド ドメインにカスタム OU を作成した後、その OU 内にサービス アカウントを作成します。

この記事では、Azure AD DS マネージド ドメインに OU を作成する方法について説明します。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、次のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services インスタンスを作成して構成する][create-azure-ad-ds-instance]チュートリアルを完了します。
* Azure AD DS マネージド ドメインに参加している Windows Server 管理 VM。
    * 必要に応じて、[管理 VM を作成する][tutorial-create-management-vm]チュートリアルを完了します。
* Azure AD テナント内の *Azure AD DC 管理者*グループのメンバーであるユーザー アカウント。

## <a name="custom-ou-considerations-and-limitations"></a>カスタム OU に関する考慮事項と制限

Azure AD DS マネージド ドメインにカスタム OU を作成すると、ユーザー管理やグループ ポリシーの適用のための管理の柔軟性が向上します。 オンプレミスの AD DS 環境と比較して、Azure AD DS 内にカスタム OU の構造を作成して管理する場合にはいくつかの制限と考慮事項が存在します。

* カスタム OU を作成するには、ユーザーが *AAD DC 管理者*グループのメンバーである必要があります。
* カスタム OU を作成するユーザーにはその OU に対する管理者特権 (フル コントロール) が付与され、そのユーザーがリソース所有者になります。
    * 既定では、*AAD DC 管理者*グループにもカスタム OU のフル コントロールが与えられます。
* Azure AD テナントから同期されたユーザー アカウントが含まれている、*AADDC Users* の既定の OU が作成されます。
    * *AADDC Users* OU から、作成したカスタム OU にユーザーまたはグループを移動することはできません。 カスタム OU に移動できるのは、Azure AD DS マネージド ドメインに作成されたユーザー アカウントまたはリソースだけです。
* カスタム OU に作成したユーザー アカウント、グループ、サービス アカウント、およびコンピューター オブジェクトは Azure AD テナントでは使用できません。
    * これらのオブジェクトは Azure AD Graph API を使用した場合も、あるいは Azure AD UI にも表示されず、Azure AD DS マネージド ドメインでのみ使用できます。

## <a name="create-a-custom-ou"></a>カスタム OU を作成する

カスタム OU を作成するには、ドメインに参加している VM から Active Directory 管理ツールを使用します。 Active Directory 管理センターを使用すると、Azure AD DS マネージド ドメイン (OU を含む) 内のリソースを表示、編集、および作成できます。

> [!NOTE]
> Azure AD DS マネージド ドメインにカスタム OU を作成するには、*AAD DC 管理者*グループのメンバーであるユーザー アカウントにサインインしている必要があります。

1. スタート画面から、 **[管理ツール]** を選択します。 [管理 VM を作成する][tutorial-create-management-vm]ためのチュートリアルでインストールされた使用可能な管理ツールの一覧が表示されます。
1. OU を作成して管理するには、管理ツールの一覧から **[Active Directory 管理センター]** を選択します。
1. 左側のウィンドウで、Azure AD DS マネージド ドメイン (*contoso.com* など) を選択します。 既存の OU とリソースの一覧が表示されます。

    ![Active Directory 管理センターで Azure AD DS マネージド ドメインを選択する](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

1. Active Directory 管理センターの右側に **[タスク]** ウィンドウが表示されます。 ドメイン (*contoso.com* など) で、 **[新規] > [組織単位]** を選択します。

    ![Active Directory 管理センターで、新しい OU を作成するオプションを選択する](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

1. **[Create Organizational Unit] (組織単位の作成)** ダイアログで、新しい OU の **[名前]** (*MyCustomOu* など) を指定します。 OU の簡単な説明 (*サービス アカウント用のカスタム OU* など) を指定します。 必要に応じて、OU の **[Managed By] (管理者)** フィールドを設定することもできます。 カスタム OU を作成するには、 **[OK]** を選択します。

    ![Active Directory 管理センターからカスタム OU を作成する](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

1. Active Directory 管理センターに戻ると、カスタム OU が一覧表示され、使用可能になっています。

    ![Active Directory 管理センターで使用可能なカスタム OU](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="next-steps"></a>次の手順

管理ツールの使用またはサービス アカウントの作成と使用の詳細については、次の記事を参照してください。

* [Active Directory 管理センター: はじめに](https://technet.microsoft.com/library/dd560651.aspx)
* [サービス アカウントのステップ バイ ステップ ガイド](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
