---
title: Azure AD Domain Services の組織単位 (OU) を作成する | Microsoft Docs
description: Azure AD Domain Services のマネージド ドメインにカスタム組織単位 (OU) を作成して管理する方法について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: fbdfcc23553a27aaa4d7bbd4bff9c4f33cb8fd19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620020"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services のマネージド ドメインに組織単位 (OU) を作成する

Active Directory Domain Services (AD DS) マネージド ドメインの組織単位 (OU) を使用すると、ユーザー アカウント、サービス アカウント、コンピューター アカウントなどのオブジェクトを論理的にグループ化できます。 その後、特定の OU に管理者を割り当て、グループ ポリシーを適用して対象となる構成設定を強制できます。

Azure AD DS マネージド ドメインには、次の 2 つの組み込み OU が含まれています。

* *AADDC Computers* - マネージド ドメインに参加しているすべてのコンピューターに関するコンピューター オブジェクトが含まれています。
* *AADDC Users* - Azure AD テナントから同期されたユーザーとグループが含まれています。

Azure AD DS を使用するワークロードを作成して実行するときは、各アプリケーションが自身を認証するためのサービス アカウントを作成することが必要になる場合があります。 これらのサービス アカウントを整理するために、多くの場合は、マネージド ドメインにカスタム OU を作成した後、その OU 内にサービス アカウントを作成します。

ハイブリッド環境では、オンプレミスの AD DS 環境で作成された OU は、マネージド ドメインに同期されません。 マネージド ドメインでは、フラットな OU 構造が使用されます。 そこで階層的な OU 構造を構成した場合、ユーザー アカウントとグループは、異なるオンプレミス ドメインまたはフォレストから同期されても、すべて *AADDC Users* コンテナー内に格納されます。

この記事では、マネージド ドメインに OU を作成する方法を説明します。

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services マネージド ドメインを作成して構成する][create-azure-ad-ds-instance]チュートリアルを完了します。
* Azure AD DS マネージド ドメインに参加している Windows Server 管理 VM。
    * 必要に応じて、[管理 VM を作成する][tutorial-create-management-vm]チュートリアルを完了します。
* Azure AD テナントの *Azure AD DC administrators* グループのメンバーであるユーザー アカウント。

## <a name="custom-ou-considerations-and-limitations"></a>カスタム OU に関する考慮事項と制限

マネージド ドメインにカスタム OU を作成すると、ユーザー管理やグループ ポリシーの適用のための管理の柔軟性が向上します。 オンプレミスの AD DS 環境と比較して、マネージド ドメインにカスタムの OU 構造を作成して管理する場合にはいくつかの制限と考慮事項があります。

* カスタム OU を作成するには、ユーザーが *AAD DC 管理者* グループのメンバーである必要があります。
* カスタム OU を作成するユーザーにはその OU に対する管理者特権 (フル コントロール) が付与され、そのユーザーがリソース所有者になります。
    * 既定では、*AAD DC 管理者* グループにもカスタム OU のフル コントロールが与えられます。
* Azure AD テナントから同期されたすべてのユーザー アカウントが含まれている、*AADDC Users* の既定の OU が作成されます。
    * *AADDC Users* OU から、作成したカスタム OU にユーザーまたはグループを移動することはできません。 カスタム OU に移動できるのは、マネージド ドメインに作成されたユーザー アカウントまたはリソースだけです。
* カスタム OU に作成したユーザー アカウント、グループ、サービス アカウント、およびコンピューター オブジェクトは Azure AD テナントでは使用できません。
    * これらのオブジェクトは Microsoft Graph API を使用した場合も、あるいは Azure AD UI にも表示されず、お使いのマネージド ドメインでのみ使用できます。

## <a name="create-a-custom-ou"></a>カスタム OU を作成する

カスタム OU を作成するには、ドメインに参加している VM から Active Directory 管理ツールを使用します。 Active Directory 管理センターを使用すると、マネージド ドメイン内のリソース (OU を含む) を表示、編集、および作成できます。

> [!NOTE]
> マネージド ドメインにカスタム OU を作成するには、*AAD DC 管理者* グループのメンバーであるユーザー アカウントにサインインする必要があります。

1. 管理 VM にサインインします。 Azure portal を使用した接続方法については、「[Windows Server VM に接続する][connect-windows-server-vm]」を参照してください。
1. スタート画面で **[管理ツール]** を選択します。 [管理 VM を作成する][tutorial-create-management-vm]ためのチュートリアルでインストールされた使用可能な管理ツールの一覧が表示されます。
1. OU を作成して管理するには、管理ツールの一覧から **[Active Directory 管理センター]** を選択します。
1. 左側のペインで、マネージド ドメイン (例: *aaddscontoso.com*) を選択します。 既存の OU とリソースの一覧が表示されます。

    ![Active Directory 管理センターでマネージド ドメインを選択する](./media/create-ou/create-ou-adac-overview.png)

1. Active Directory 管理センターの右側に **[タスク]** ウィンドウが表示されます。 ドメイン (例: *aaddscontoso.com*) で、 **[新規] > [組織単位]** を選択します。

    ![Active Directory 管理センターで、新しい OU を作成するオプションを選択する](./media/create-ou/create-ou-adac-new-ou.png)

1. **[Create Organizational Unit] (組織単位の作成)** ダイアログで、新しい OU の **[名前]** (*MyCustomOu* など) を指定します。 OU の簡単な説明 (*サービス アカウント用のカスタム OU* など) を指定します。 必要に応じて、OU の **[Managed By] (管理者)** フィールドを設定することもできます。 カスタム OU を作成するには、 **[OK]** を選択します。

    ![Active Directory 管理センターからカスタム OU を作成する](./media/create-ou/create-ou-dialog.png)

1. Active Directory 管理センターに戻ると、カスタム OU が一覧表示され、使用可能になっています。

    ![Active Directory 管理センターで使用可能なカスタム OU](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>次のステップ

管理ツールの使用またはサービス アカウントの作成と使用の詳細については、次の記事を参照してください。

* [Active Directory 管理センター: はじめに](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560651(v=ws.10))
* [サービス アカウントのステップ バイ ステップ ガイド](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm