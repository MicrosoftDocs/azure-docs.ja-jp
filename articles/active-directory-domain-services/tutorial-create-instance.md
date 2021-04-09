---
title: チュートリアル - Azure Active Directory Domain Services のマネージド ドメインを作成する | Microsoft Docs
description: このチュートリアルでは、Azure portal を使用して Azure Active Directory Domain Services のマネージド ドメインを作成、構成する方法について説明します。
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 945c131394a0a3c6273f79044c8500a2feba70fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618146"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-managed-domain"></a>チュートリアル:Azure Active Directory Domain Services のマネージド ドメインを作成して構成する

Azure Active Directory Domain Services (Azure AD DS) では、Windows Server Active Directory と完全に互換性のあるマネージド ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos 認証、NTLM 認証など) が提供されます。 ドメイン コントローラーのデプロイ、管理、パッチの適用を自分で行わなくても、これらのドメイン サービスを使用することができます。 Azure AD DS は、既存の Azure AD テナントと統合されます。 この統合により、ユーザーは、各自の会社の資格情報を使用してサインインすることができます。また管理者は、既存のグループとユーザー アカウントを使用してリソースへのアクセスをセキュリティで保護することができます。

ネットワークと同期に関して既定の構成オプションを使用してマネージド ドメインを作成するか、[それらの設定を手動で定義][tutorial-create-instance-advanced]することができます。 このチュートリアルでは、Azure portal を使用し、既定のオプションを使用して Azure AD DS のマネージド ドメインを作成、構成する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * マネージド ドメインの DNS の要件を理解する
> * マネージド ドメインの作成
> * パスワード ハッシュ同期を有効にする

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD DS を有効にするには、Azure AD テナントに "*全体管理者*" 特権が必要です。
* 必要な Azure AD DS リソースを作成するためには、ご利用の Azure サブスクリプションに "*共同作成者*" 特権が必要です。

Azure AD DS では必須ではありませんが、Azure AD テナントには、[SSPR (Self-Service Password Reset: セルフサービス パスワード リセット ) を構成][configure-sspr]することをお勧めします。 SSPR がなくても、ユーザーは自分のパスワードを変更できます。しかし、ユーザーがパスワードを紛失してしまってリセットする必要が生じた場合には SSPR が役立ちます。

> [!IMPORTANT]
> いったん作成したマネージド ドメインを別のリソース グループ、仮想ネットワーク、サブスクリプションなどに移動することはできません。マネージド ドメインをデプロイするときに、最適なサブスクリプション、リソース グループ、リージョン、仮想ネットワークを慎重に選択してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

このチュートリアルでは、Azure portal を使用してマネージド ドメインの作成と構成を行います。 最初に、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-managed-domain"></a>マネージド ドメインの作成

**[Azure AD Domain Services の有効化]** ウィザードを起動するには、次の手順を実行します。

1. Azure portal メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。
1. 検索バーに「*Domain Services*」と入力し、検索候補から *[Azure AD Domain Services]* を選択します。
1. [Azure AD Domain Services] ページで **[作成]** を選択します。 **[Azure AD Domain Services の有効化]** ウィザードが起動します。
1. マネージド ドメインを作成する Azure **サブスクリプション** を選択します。
1. マネージド ドメインが属する **リソース グループ** を選択します。 リソース グループを **新規作成** するか、既存のリソース グループを選択してください。

マネージド ドメインを作成する際は、DNS 名を指定します。 この DNS 名を選ぶ際のいくつかの考慮事項を次に示します。

* **組み込みドメイン名:** 既定では、ディレクトリの組み込みドメイン名が使用されます ( *.onmicrosoft.com* サフィックス)。 マネージド ドメインに対するインターネット経由での Secure LDAP アクセスを有効にしたい場合、デジタル証明書を作成して、この既定のドメインとの接続をセキュリティで保護することはできません。 *.onmicrosoft.com* ドメインを所有するのは Microsoft であるため、証明機関 (CA) からは証明書が発行されません。
* **カスタム ドメイン名:** 最も一般的な方法は、カスタム ドメイン名を指定することです。一般に、貴社が既に所有していて、なおかつルーティング可能なものを指定します。 ルーティング可能なカスタム ドメインを使用すれば、ご利用のアプリケーションをサポートするために必要なトラフィックを正しく送信することができます。
* **ルーティング不可能なドメインのサフィックス:** 一般に、ルーティング不可能なドメイン名サフィックス (*contoso.local* など) は避けることをお勧めします。 *.local* サフィックスはルーティングできないため、DNS 解決で問題の原因となることがあります。

> [!TIP]
> カスタム ドメイン名を作成する場合は、既存の DNS 名前空間に注意してください。 Azure およびオンプレミスの既存の DNS 名前空間とは別のドメイン名を使用することをお勧めします。
>
> たとえば、*contoso.com* の既存の DNS 名前空間がある場合、*aaddscontoso.com* というカスタム ドメイン名を使用してマネージド ドメインを作成します。 Secure LDAP を使用する必要がある場合は、このカスタム ドメイン名を登録して所有し、必要な証明書を生成する必要があります。
>
> 場合によっては、環境内の他のサービス用に追加で DNS レコードを作成したり、環境内に既に存在する 2 つの DNS 名前空間の間に条件付き DNS フォワーダーを作成したりする必要があります。 たとえば、ルート DNS 名を使用するサイトをホストする Web サーバーを実行する場合、名前の競合が発生して、追加の DNS エントリが必要になる可能性があります。
>
> これらのチュートリアルとハウツー記事では、簡略な例として *aaddscontoso.com* というカスタム ドメインを使用しています。 すべてのコマンドで、独自のドメイン名を指定してください。

DNS 名には、次の制限も適用されます。

* **ドメインのプレフィックスの制限:** プレフィックスが 15 文字より長いマネージド ドメインを作成できません。 指定するドメイン名のプレフィックス (たとえば、ドメイン名 *aaddscontoso.com* の *aaddscontoso* など) は、15 文字以内に収める必要があります。
* **ネットワーク名の競合:** マネージド ドメインの DNS ドメイン名は、まだ仮想ネットワークに存在していない必要があります。 具体的には、名前の競合につながる次のシナリオをチェックしてください。
    * 同じ DNS ドメイン名の Active Directory ドメインが Azure 仮想ネットワーク上に既にあるかどうか。
    * マネージド ドメインを有効にする仮想ネットワークに、オンプレミス ネットワークとの VPN 接続があるかどうか。 このシナリオでは、オンプレミス ネットワークに同じ DNS ドメイン名のドメインがないことを確認します。
    * その名前の付いた Azure クラウド サービスが Azure 仮想ネットワーク上に既にあるかどうか。

Azure portal の *[基本]* ウィンドウのフィールドに必要事項を入力してマネージド ドメインを作成します。

1. 前述のポイントを考慮しながらマネージド ドメインの **DNS ドメイン名** を入力します。
1. マネージド ドメインを作成する Azure の **場所** を選択します。 Azure Availability Zones がサポートされているリージョンを選択すると、Azure AD DS リソースが、冗長性強化のために複数のゾーンに分散されます。

    > [!TIP]
    > Availability Zones は、Azure リージョン内の一意の物理的な場所です。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 回復性を確保するため、有効になっているリージョンにはいずれも最低 3 つのゾーンが別個に存在しています。
    >
    > Azure AD DS を複数のゾーンに分散するために、ご自身で構成するものは何もありません。 Azure プラットフォームでは、ゾーンへのリソース分散が自動的に処理されます。 詳細情報および利用可能なリージョンについては、「[Azure の Availability Zones の概要][availability-zones]」を参照してください。

1. パフォーマンス、バックアップ頻度、作成できるフォレストの信頼の最大数は、**SKU** によって決まります。 マネージド ドメインの作成後、ビジネス上の需要や要件に変化が生じた場合は SKU を変更できます。 詳細については、[Azure AD DS SKU の概念][concepts-sku]に関するセクションを参照してください。

    このチュートリアルでは、*Standard* SKU を選択します。
1. "*フォレスト*" は、Active Directory Domain Services が 1 つまたは複数のドメインをグループ化するために使用する論理上の構成体です。 既定では、マネージド ドメインは "*ユーザー*" フォレストとして作成されます。 このタイプのフォレストでは、オンプレミスの AD DS 環境で作成されたユーザー アカウントも含め、Azure AD 内のすべてのオブジェクトが同期されます。

    "*リソース*" フォレストでは、Azure AD に直接作成されたユーザーとグループだけが同期されます。 リソース フォレストを使用する理由や、オンプレミスの AD DS ドメインを使用してフォレストの信頼を作成する方法など、"*リソース*" フォレストの詳細については、[Azure AD DS リソース フォレストの概要][resource-forests]に関するページを参照してください。

    このチュートリアルでは、"*ユーザー*" フォレストを作成します。

    ![Azure AD Domain Services マネージド ドメインの基本的な設定を構成する](./media/tutorial-create-instance/basics-window.png)

マネージド ドメインをすばやく作成するには、 **[確認および作成]** を選択して、追加となる既定の構成オプションをそのまま使用します。 この作成オプションを選択した場合は、次の既定値が構成されます。

* IP アドレス範囲 *10.0.2.0/24* を使用する *aadds-vnet* という名前の仮想ネットワークを作成します。
* IP アドレス範囲 *10.0.2.0/24* を使用して、*aadds-subnet* という名前のサブネットを作成します。
* Azure AD の "*すべて*" のユーザーをマネージド ドメインに同期させます。

**[確認および作成]** を選択して、これらの既定の構成オプションをそのまま使用します。

## <a name="deploy-the-managed-domain"></a>マネージド ドメインをデプロイする

ウィザードの **[概要]** ページで、マネージド ドメインの構成設定を確認します。 ウィザードの任意の手順に戻り、変更を加えることができます。 これらの構成オプションを使用し、マネージド ドメインを一貫した方法で別の Azure AD テナントに再デプロイしたければ、**Automation のテンプレートをダウンロードする** こともできます。

1. マネージド ドメインを作成するには、 **[作成]** を選択します。 Azure AD DS のマネージド ドメインの作成後は特定の構成オプション (DNS 名、仮想ネットワークなど) を変更できないという注意が表示されます。 続行するには、 **[OK]** を選択します。
1. マネージド ドメインのプロビジョニングのプロセスは、最大で 1 時間かかることがあります。 Azure AD DS のデプロイの進行状況を示す通知がポータルに表示されます。 通知を選択すると、デプロイの詳細な進行状況が表示されます。

    ![デプロイが進行中であることを示す Azure portal の通知](./media/tutorial-create-instance/deployment-in-progress.png)

1. ディレクトリに新しいリソースが作成されたなど、デプロイ プロセスに関する最新情報がページに読み込まれます。
1. リソース グループ (例: *myResourceGroup*) を選択し、Azure リソースの一覧からマネージド ドメイン (例: *aaddscontoso.com*) を選択します。 **[概要]** タブでは、マネージド ドメインが現在 "*デプロイ中*" であることが示されます。 完全にプロビジョニングされるまで、マネージド ドメインを構成することはできません。

    ![Domain Services の状態 (プロビジョニング中)](./media/tutorial-create-instance/provisioning-in-progress.png)

1. マネージド ドメインが完全にプロビジョニングされると、 **[概要]** タブには、ドメインの状態が *[実行中]* であることが示されます。

    ![Domain Services の状態 (プロビジョニングの正常完了後)](./media/tutorial-create-instance/successfully-provisioned.png)

> [!IMPORTANT]
> マネージド ドメインは、Azure AD テナントに関連付けられています。 プロビジョニング プロセスの間に、*Domain Controller Services* と *AzureActiveDirectoryDomainControllerServices* という 2 つのエンタープライズ アプリケーションが、Azure AD DS によって Azure AD テナントに作成されます。 これらのエンタープライズ アプリケーションは、マネージド ドメインのサービスを提供するために使用されます。 これらのアプリケーションは削除しないでください。

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Azure 仮想ネットワークの DNS 設定を更新する

Azure AD DS のデプロイに成功したら、接続された他の VM やアプリケーションがマネージド ドメインを使用できるように仮想ネットワークを構成します。 この接続性を確保するには、マネージド ドメインのデプロイ先である 2 つの IP アドレスを指すように仮想ネットワークの DNS サーバー設定を更新します。

1. マネージド ドメインの **[概要]** タブに、**必要な構成手順** がいくつか表示されます。 最初の構成手順は、仮想ネットワークの DNS サーバー設定を更新することです。 DNS 設定が正しく構成されると、この手順は表示されなくなります。

    列挙されているアドレスは、仮想ネットワークで使用するためのドメイン コントローラーです。 この例では、*10.0.2.4* と *10.0.2.5* がそれらのアドレスに該当します。 これらの IP アドレスは、後から **[プロパティ]** タブで確認できます。

    ![Azure AD Domain Services の IP アドレスを使用して仮想ネットワークの DNS 設定を構成する](./media/tutorial-create-instance/configure-dns.png)

1. 仮想ネットワークの DNS サーバー設定を更新するには、 **[構成]** ボタンを選択します。 仮想ネットワークの DNS 設定が自動的に構成されます。

> [!TIP]
> 前の手順で既存の仮想ネットワークを選択した場合、ネットワークに接続された VM が新しい DNS 設定を取得するのは、再起動後となります。 VM の再起動は、Azure portal、Azure PowerShell、または Azure CLI を使用して行うことができます。

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Azure AD DS のユーザー アカウントを有効にする

Azure AD DS でマネージド ドメインのユーザーを認証するためには、NT LAN Manager (NTLM) 認証および Kerberos 認証に適した形式のパスワード ハッシュが必要となります。 NTLM 認証と Kerberos 認証に必要な形式のパスワード ハッシュは、ご利用のテナントに対して Azure AD DS を有効にするまで、Azure AD で生成または保存されることはありません。 また、セキュリティ上の理由から、クリアテキスト形式のパスワード資格情報が Azure AD に保存されることもありません。 そのため、こうした NTLM または Kerberos のパスワード ハッシュをユーザーの既存の資格情報に基づいて Azure AD が自動的に生成することはできません。

> [!NOTE]
> 適切に構成されれば、使用可能なパスワード ハッシュがマネージド ドメインに保存されます。 マネージド ドメインを削除した場合、その時点で保存されていたパスワード ハッシュがあればすべて削除されます。
>
> マネージド ドメインを後から作成した場合、Azure AD にある同期済みの資格情報は再利用できません。パスワード ハッシュを再度保存するには、パスワード ハッシュ同期を再構成する必要があります。 既にドメイン参加済みの VM またはユーザーがすぐに認証を行うことはできません。Azure AD が、新しいマネージド ドメインにパスワード ハッシュを生成して保存する必要があります。
>
> 詳細については、[Azure AD DS と Azure AD Connect のパスワード ハッシュ同期プロセス][password-hash-sync-process]に関するセクションを参照してください。

Azure AD に作成されたユーザー アカウントがクラウド専用のアカウントであるか、オンプレミス ディレクトリとの間で Azure AD Connect を使って同期されたアカウントであるかによって、パスワード ハッシュの生成と保存の手順は異なります。

クラウド専用ユーザー アカウントとは、Azure Portal または Azure AD PowerShell コマンドレットを使って Azure AD ディレクトリに作成されたアカウントです。 そのようなユーザー アカウントは、オンプレミス ディレクトリとの間で同期されません。

> このチュートリアルでは、基本的なクラウド専用ユーザー アカウントを使用することにします。 Azure AD Connect を使用するために別途必要な手順の詳細については、[オンプレミス AD からマネージド ドメインに同期されたユーザー アカウントのパスワード ハッシュを同期する方法][on-prem-sync]に関するページを参照してください。

> [!TIP]
> Azure AD テナントにクラウド専用ユーザーとオンプレミス AD からのユーザーが混在している場合は、両方の手順を実行する必要があります。

クラウド専用ユーザー アカウントの場合、ユーザーは Azure AD DS を使用する前に各自のパスワードを変更する必要があります。 このパスワード変更プロセスによって、Kerberos 認証と NTLM 認証に使用されるパスワード ハッシュが Azure AD に生成されて保存されます。 パスワードが変更されるまで、アカウントは Azure AD から Azure AD DS に同期されません。 テナント内のクラウド ユーザーのうち、Azure AD DS を使用する必要がある全ユーザーのパスワードを期限切れにして、次回のサインイン時にパスワードの変更を強制するか、または、各自のパスワードを手動で変更するようクラウド ユーザーに指示してください。 このチュートリアルでは、ユーザー パスワードを手動で変更しましょう。

ユーザーが自分のパスワードをリセットできるように、あらかじめ Azure AD テナントを[セルフサービス パスワード リセット 用に構成][configure-sspr]しておく必要があります。

クラウド専用ユーザーのパスワードを変更するには、ユーザーが次の手順を実行する必要があります。

1. Azure AD アクセス パネルのページ ([https://myapps.microsoft.com](https://myapps.microsoft.com)) に移動します。
1. 右上隅にあるご自身の名前を選択し、ドロップダウン メニューから **[プロファイル]** を選択します。

    ![プロファイルの選択](./media/tutorial-create-instance/select-profile.png)

1. **[プロファイル]** ページの **[パスワードの変更]** を選択します。
1. **[パスワードの変更]** ページで既存の (古い) パスワードを入力した後、新しいパスワードを入力して、それを確認します。
1. **[Submit]\(送信\)** をクリックします。

パスワードの変更後、Azure AD DS で新しいパスワードを使用したり、マネージド ドメインに参加しているコンピューターに正常にサインインしたりできるようになるまでには数分かかります。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * マネージド ドメインの DNS の要件を理解する
> * マネージド ドメインの作成
> * 管理ユーザーをドメイン管理に追加する
> * Azure AD DS のユーザー アカウントを有効にしてパスワード ハッシュを生成する

VM をドメインに参加させて、マネージド ドメインを使用するアプリケーションをデプロイする前に、アプリケーション ワークロード用の Azure 仮想ネットワークを構成します。

> [!div class="nextstepaction"]
> [マネージド ドメインを使用するアプリケーション ワークロード用の Azure 仮想ネットワークを構成する](tutorial-configure-networking.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/tutorial-enable-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[skus]: overview.md
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
