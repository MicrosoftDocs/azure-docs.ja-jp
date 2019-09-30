---
title: チュートリアル - Azure Active Directory Domain Services インスタンスを作成する | Microsoft Docs
description: このチュートリアルでは、Azure portal を使用して Azure Active Directory Domain Services インスタンスを作成、構成する方法について説明します。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: 8c346b75b30737645721d8b39a655a85ed446fae
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71229535"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>チュートリアル:Azure Active Directory Domain Services インスタンスを作成して構成する

Azure Active Directory Domain Services (Azure AD DS) は、Windows Server Active Directory と完全に互換性のあるマネージド ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos 認証、NTLM 認証など) を提供します。 ドメイン コントローラーのデプロイ、管理、パッチの適用を自分で行わなくても、これらのドメイン サービスを使用することができます。 Azure AD DS は、既存の Azure AD テナントと統合されます。 この統合により、ユーザーは、各自の会社の資格情報を使用してサインインすることができます。また管理者は、既存のグループとユーザー アカウントを使用してリソースへのアクセスをセキュリティで保護することができます。

このチュートリアルでは、Azure portal を使用した Azure AD DS インスタンスの作成と構成の方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * マネージド ドメイン用に DNS と仮想ネットワークの設定を構成する
> * Azure AD DS インスタンスを作成する
> * 管理ユーザーをドメイン管理に追加する
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
* この Azure AD テナントは、[パスワード リセットのセルフサービス用に構成][configure-sspr]されている必要があります。

> [!IMPORTANT]
> Azure AD DS マネージド ドメインを作成した後は、そのインスタンスを別のリソース グループ、仮想ネットワーク、サブスクリプションなどに移動することはできません。Azure AD DS インスタンスをデプロイするときに、最適なサブスクリプション、リソース グループ、リージョン、および仮想ネットワークを慎重に選択してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

このチュートリアルでは、Azure portal を使用して Azure AD DS インスタンスの作成と構成を行います。 最初に、[Azure portal](https://portal.azure.com) にサインインしてください。

## <a name="create-an-instance-and-configure-basic-settings"></a>インスタンスを作成して基本的な設定を構成する

**[Azure AD Domain Services の有効化]** ウィザードを起動するには、次の手順を実行します。

1. Azure portal の左上隅にある **[リソースの作成]** を選択します。
1. 検索バーに「*Domain Services*」と入力し、検索候補から *[Azure AD Domain Services]* を選択します。
1. [Azure AD Domain Services] ページで **[作成]** を選択します。 **[Azure AD Domain Services の有効化]** ウィザードが起動します。

Azure AD DS インスタンスを作成する際は、DNS 名を指定します。 この DNS 名を選ぶ際のいくつかの考慮事項を次に示します。

* **組み込みドメイン名:** 既定では、ディレクトリの組み込みドメイン名が使用されます ( *.onmicrosoft.com* サフィックス)。 マネージド ドメインに対するインターネット経由での Secure LDAP アクセスを有効にしたい場合、デジタル証明書を作成して、この既定のドメインとの接続をセキュリティで保護することはできません。 *.onmicrosoft.com* ドメインを所有するのは Microsoft であるため、証明機関 (CA) からは証明書が発行されません。
* **カスタム ドメイン名:** 最も一般的な方法は、カスタム ドメイン名を指定することです。一般に、貴社が既に所有していて、なおかつルーティング可能なものを指定します。 ルーティング可能なカスタム ドメインを使用すれば、ご利用のアプリケーションをサポートするために必要なトラフィックを正しく送信することができます。
* **ルーティング不可能なドメインのサフィックス:** 一般に、ルーティング不可能なドメイン名サフィックス (*contoso.local* など) は避けることをお勧めします。 *.local* サフィックスはルーティングできないため、DNS 解決で問題の原因となることがあります。

> [!TIP]
> カスタム ドメイン名を作成する場合は、既存の DNS 名前空間に注意してください。 ドメイン名には一意のプレフィックスを含めることをお勧めします。 たとえば、DNS ルート名が *contoso.com* である場合、*corp.contoso.com* または *ds.contoso.com* というカスタム ドメイン名で Azure AD DS マネージド ドメインを作成します。 オンプレミスの AD DS 環境を持つハイブリッド環境では、これらのプレフィックスが既に使用されている場合があります。 Azure AD DS には、一意のプレフィックスを使用してください。
>
> Azure AD DS マネージド ドメインにルート DNS 名を使用できますが、環境内の他のサービス用にいくつかの DNS レコードを追加作成することが必要になる場合があります。 たとえば、ルート DNS 名を使用するサイトをホストする Web サーバーを実行する場合、名前の競合が発生して、追加の DNS エントリが必要になる可能性があります。
>
> これらのチュートリアルとハウツー記事では、簡略な例として *contoso.com* というカスタム ドメインを使用しています。 すべてのコマンドで、一意のプレフィックスを含む、独自のドメイン名を指定してください。
>
> 詳細については、[ドメインの名前付けプレフィックスの選択][naming-prefix]に関するページを参照してください。

DNS 名には、次の制限も適用されます。

* **ドメインのプレフィックスの制限:** プレフィックスが 15 文字より長いマネージド ドメインを作成できません。 指定するドメイン名のプレフィックス (たとえば、ドメイン名 *contoso.com* の *contoso* など) は、15 文字以内に収める必要があります。
* **ネットワーク名の競合:** マネージド ドメインの DNS ドメイン名は、まだ仮想ネットワークに存在していない必要があります。 具体的には、名前の競合につながる次のシナリオをチェックしてください。
    * 同じ DNS ドメイン名の Active Directory ドメインが Azure 仮想ネットワーク上に既にあるかどうか。
    * マネージド ドメインを有効にする仮想ネットワークに、オンプレミス ネットワークとの VPN 接続があるかどうか。 このシナリオでは、オンプレミス ネットワークに同じ DNS ドメイン名のドメインがないことを確認します。
    * その名前の付いた Azure クラウド サービスが Azure 仮想ネットワーク上に既にあるかどうか。

Azure portal の *[基本]* ウィンドウのフィールドに必要事項を入力して Azure AD DS インスタンスを作成します。

1. 前述のポイントを考慮しながらマネージド ドメインの **DNS ドメイン名**を入力します。
1. マネージド ドメインを作成する Azure **サブスクリプション**を選択します。
1. マネージド ドメインが属する**リソース グループ**を選択します。 リソース グループを**新規作成**するか、既存のリソース グループを選択してください。
1. マネージド ドメインを作成する Azure の**場所**を選択します。
1. **[OK]** をクリックして **[ネットワーク]** セクションに進みます。

![Azure AD Domain Services インスタンスの基本的な設定を構成する](./media/tutorial-create-instance/basics-window.png)

## <a name="create-and-configure-the-virtual-network"></a>仮想ネットワークを作成して構成する

接続を提供するには、Azure 仮想ネットワークと専用サブネットが必要となります。 この仮想ネットワーク サブネットの中で Azure AD DS は有効になります。 このチュートリアルでは仮想ネットワークを作成しますが、既存の仮想ネットワークを使用することもできます。 どちらのアプローチも、Azure AD DS のための専用のサブネットは作成する必要があります。

この専用仮想ネットワーク サブネットに関するいくつかの考慮事項を次に示します。

* Azure AD DS のリソースをサポートするために、サブネットのアドレス範囲には、使用できる IP アドレスが少なくとも 3 個から 5 個存在する必要があります。
* Azure AD DS のデプロイ先として "*ゲートウェイ*" サブネットは選択しないでください。 Azure AD DS を "*ゲートウェイ*" サブネットにデプロイすることはサポートされません。
* このサブネットに他の仮想マシンをデプロイしないでください。 アプリケーションと VM は、接続をセキュリティで保護するためにネットワーク セキュリティ グループを使用することがよくあります。 これらのワークロードを個別のサブネットで実行することによって、マネージド ドメインへの接続に支障をきたすことなく、それらのネットワーク セキュリティ グループを適用することができます。
* Azure AD DS を有効にした後で、マネージド ドメインを別の仮想ネットワークに移動することはできません。

仮想ネットワークを計画および構成する方法の詳細については、[Azure Active Directory Domain Services のネットワークに関する考慮事項][network-considerations]の記事を参照してください。

*[ネットワーク]* ウィンドウのフィールドに次のように入力します。

1. **[ネットワーク]** ウィンドウで **[仮想ネットワークの選択]** を選択します。
1. このチュートリアルでは **[新規作成]** を選択して、Azure AD DS のデプロイ先となる仮想ネットワークを作成します。
1. 仮想ネットワークの名前 (例: *myVnet*) を入力し、アドレスの範囲 (例: *10.1.0.0/16*) を指定します。
1. わかりやすい名前 (例: *DomainServices*) で専用サブネットを作成します。 アドレス範囲 (例: *10.1.0.0/24*) を指定します。

    ![Azure AD Domain Services で使用する仮想ネットワークとサブネットを作成する](./media/tutorial-create-instance/create-vnet.png)

    必ずプライベート IP アドレスの範囲内にあるアドレスの範囲を選んでください。 ご自身が所有していないパブリック アドレス空間内の IP アドレス範囲を指定すると、Azure AD DS 内でエラーが発生します。

    > [!TIP]
    > **[仮想ネットワークの選択]** ページには、前に選択したリソース グループと Azure の場所に属している既存の仮想ネットワークが表示されます。 Azure AD DS をデプロイする前に[専用サブネットを作成][create-dedicated-subnet]しておく必要があります。

1. 仮想ネットワークとサブネットが作成されていれば、そのサブネットが自動的に選択されます (例: *DomainServices*)。 代わりに、選択した仮想ネットワークに属している既存のサブネットを選択することもできます。

    ![仮想ネットワーク内の専用サブネットを選択する](./media/tutorial-create-instance/choose-subnet.png)

1. **[OK]** をクリックして仮想ネットワークの構成を確定します。

## <a name="configure-an-administrative-group"></a>管理グループを構成する

Azure AD DS ドメインの管理には、*AAD DC Administrators* という名前の特殊な管理グループを使用します。 このグループのメンバーには、マネージド ドメインに参加している VM の管理権限が付与されます。 ドメインに参加している VM では、このグループがローカル管理者グループに追加されます。 このグループのメンバーは、リモート デスクトップを使用して、ドメインに参加している VM にリモートで接続することもできます。

Azure AD DS を使用するマネージド ドメインに対する "*ドメイン管理者*" または "*エンタープライズ管理者*" アクセス許可はありません。 これらのアクセス許可はサービスによって予約されており、テナント内でユーザーが使用することはできません。 特権を要する一部の操作については、代わりに *AAD DC Administrators* グループを使用して実行できます。 これらの操作には、ドメインへのコンピューターの参加、ドメインに参加している VM での管理グループへの所属、グループ ポリシーの構成などが含まれます。

*AAD DC Administrators* グループは、ウィザードで Azure AD ディレクトリに自動的に作成されます。 Azure AD ディレクトリにこの名前のグループが既に存在している場合、ウィザードはこのグループを選択します。 この *AAD DC Administrators* グループには、デプロイ プロセス中、必要に応じてさらにユーザーを追加することもできます。 これらの手順は後から行うこともできます。

1. この *AAD DC Administrators* グループにさらにユーザーを追加するには、 **[グループ メンバーシップの管理]** を選択します。
1. **[メンバーの追加]** ボタンを選択し、Azure AD ディレクトリからユーザーを検索して選択します。 たとえば、ご自身のアカウントを検索して *AAD DC Administrators* グループに追加します。

    ![AAD DC Administrators グループのメンバーシップを構成する](./media/tutorial-create-instance/admin-group.png)

1. 終了したら、 **[OK]** を選択します。

## <a name="configure-synchronization"></a>同期の構成

Azure AD DS では、Azure AD に存在する "*すべて*" のユーザーとグループを同期できるほか、特定のグループのみを "*範囲指定*" して同期することもできます。 "*すべて*" のユーザーとグループを同期することにした場合は、指定した範囲のみの同期を後から選択することはできません。 範囲指定された同期の詳細については、[Azure AD Domain Services の範囲指定された同期][scoped-sync]に関するページを参照してください。

1. このチュートリアルでは、**すべて**のユーザーとグループを同期するように選択します。 この同期が既定のオプションとなります。

    ![Azure AD のユーザーとグループの完全同期を実行する](./media/tutorial-create-instance/sync-all.png)

1. **[OK]** を選択します。

## <a name="deploy-your-managed-domain"></a>マネージド ドメインのデプロイ

ウィザードの **[概要]** ページで、マネージド ドメインの構成設定を確認します。 ウィザードの任意の手順に戻り、変更を加えることができます。

1. マネージド ドメインを作成するには、 **[OK]** を選択します。
1. マネージド ドメインのプロビジョニングのプロセスは、最大で 1 時間かかることがあります。 Azure AD DS のデプロイの進行状況を示す通知がポータルに表示されます。 通知を選択すると、デプロイの詳細な進行状況が表示されます。

    ![デプロイが進行中であることを示す Azure portal の通知](./media/tutorial-create-instance/deployment-in-progress.png)

1. リソース グループ (例: *myResourceGroup*) を選択し、Azure リソースの一覧から Azure AD DS インスタンス (例: *contoso.com*) を選択します。 **[概要]** タブでは、マネージド ドメインが現在 "*デプロイ中*" であることが示されます。 完全にプロビジョニングされるまで、マネージド ドメインを構成することはできません。

    ![Domain Services の状態 (プロビジョニング中)](./media/tutorial-create-instance/provisioning-in-progress.png)

1. マネージド ドメインが完全にプロビジョニングされると、 **[概要]** タブには、ドメインの状態が *[実行中]* であることが示されます。

    ![Domain Services の状態 (プロビジョニングの正常完了後)](./media/tutorial-create-instance/successfully-provisioned.png)

プロビジョニング プロセスの間に、Azure AD DS は、お使いのディレクトリに *Domain Controller Services* と *AzureActiveDirectoryDomainControllerServices* というエンタープライズ アプリケーションを作成します。 これらのエンタープライズ アプリケーションは、マネージド ドメインのサービスを提供するために使用されます。 どのような場合にも、これらのアプリケーションが削除されないことが不可欠です。

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Azure 仮想ネットワークの DNS 設定を更新する

Azure AD DS のデプロイに成功したら、接続された他の VM やアプリケーションがマネージド ドメインを使用できるように仮想ネットワークを構成します。 この接続性を確保するには、Azure AD DS のデプロイ先である 2 つの IP アドレスを指すように仮想ネットワークの DNS サーバー設定を更新します。

1. マネージド ドメインの **[概要]** タブに、**必要な構成手順**がいくつか表示されます。 最初の構成手順は、仮想ネットワークの DNS サーバー設定を更新することです。 DNS 設定が正しく構成されると、この手順は表示されなくなります。

    列挙されているアドレスは、仮想ネットワークで使用するためのドメイン コントローラーです。 この例では、*10.1.0.4* と *10.1.0.5* がそれらのアドレスに該当します。 これらの IP アドレスは、後から **[プロパティ]** タブで確認できます。

    ![Azure AD Domain Services の IP アドレスを使用して仮想ネットワークの DNS 設定を構成する](./media/tutorial-create-instance/configure-dns.png)

1. 仮想ネットワークの DNS サーバー設定を更新するには、 **[構成]** ボタンを選択します。 仮想ネットワークの DNS 設定が自動的に構成されます。

> [!TIP]
> 前の手順で既存の仮想ネットワークを選択した場合、ネットワークに接続された VM が新しい DNS 設定を取得するのは、再起動後となります。 VM の再起動は、Azure portal、Azure PowerShell、または Azure CLI を使用して行うことができます。

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Azure AD DS のユーザー アカウントを有効にする

Azure AD DS でマネージド ドメインのユーザーを認証するためには、NT LAN Manager (NTLM) 認証および Kerberos 認証に適した形式のパスワード ハッシュが必要となります。 NTLM 認証と Kerberos 認証に必要な形式のパスワード ハッシュは、ご利用のテナントに対して Azure AD DS を有効にするまで、Azure AD で生成または保存されることはありません。 また、セキュリティ上の理由から、クリアテキスト形式のパスワード資格情報が Azure AD に保存されることもありません。 そのため、こうした NTLM または Kerberos のパスワード ハッシュをユーザーの既存の資格情報に基づいて Azure AD が自動的に生成することはできません。

> [!NOTE]
> 適切に構成されれば、使用可能なパスワード ハッシュが Azure AD DS のマネージド ドメインに保存されます。 Azure AD DS のマネージド ドメインを削除した場合、その時点で保存されていたパスワード ハッシュがあればすべて削除されます。 Azure AD DS のマネージド ドメインを後から作成した場合、Azure AD にある同期済みの資格情報は再利用できません。パスワード ハッシュを再度保存するには、パスワード ハッシュ同期を再構成する必要があります。 既にドメイン参加済みの VM またはユーザーがすぐに認証を行うことはできません。Azure AD が、新しい Azure AD DS のマネージド ドメインにパスワード ハッシュを生成して保存する必要があります。 詳細については、[Azure AD DS と Azure AD Connect のパスワード ハッシュ同期プロセス][password-hash-sync-process]に関するセクションを参照してください。

Azure AD に作成されたユーザー アカウントがクラウド専用のアカウントであるか、オンプレミス ディレクトリとの間で Azure AD Connect を使って同期されたアカウントであるかによって、パスワード ハッシュの生成と保存の手順は異なります。 クラウド専用ユーザー アカウントとは、Azure Portal または Azure AD PowerShell コマンドレットを使って Azure AD ディレクトリに作成されたアカウントです。 そのようなユーザー アカウントは、オンプレミス ディレクトリとの間で同期されません。 このチュートリアルでは、基本的なクラウド専用ユーザー アカウントを使用することにします。 Azure AD Connect を使用するために別途必要な手順の詳細については、[オンプレミス AD からマネージド ドメインに同期されたユーザー アカウントのパスワード ハッシュを同期する方法][on-prem-sync]に関するページを参照してください。

> [!TIP]
> Azure AD テナントにクラウド専用ユーザーとオンプレミス AD からのユーザーが混在している場合は、両方の手順を実行する必要があります。

クラウド専用ユーザー アカウントの場合、ユーザーは Azure AD DS を使用する前に各自のパスワードを変更する必要があります。 このパスワード変更プロセスによって、Kerberos 認証と NTLM 認証に使用されるパスワード ハッシュが Azure AD に生成されて保存されます。 テナント内のユーザーのうち、Azure AD DS を使用する必要がある全ユーザーのパスワードを期限切れにして、次回のサインイン時にパスワードの変更を強制するか、または、各自のパスワードを手動で変更するようユーザーに指示してください。 このチュートリアルでは、ユーザー パスワードを手動で変更しましょう。

ユーザーが自分のパスワードをリセットできるように、あらかじめ Azure AD テナントを[パスワード リセットのセルフサービス用に構成][configure-sspr]しておく必要があります。

クラウド専用ユーザーのパスワードを変更するには、ユーザーが次の手順を実行する必要があります。

1. Azure AD アクセス パネルのページ ([https://myapps.microsoft.com](https://myapps.microsoft.com)) に移動します。
1. 右上隅にあるご自身の名前を選択し、ドロップダウン メニューから **[プロファイル]** を選択します。

    ![プロファイルの選択](./media/tutorial-create-instance/select-profile.png)

1. **[プロファイル]** ページの **[パスワードの変更]** を選択します。
1. **[パスワードの変更]** ページで既存の (古い) パスワードを入力した後、新しいパスワードを入力して、それを確認します。
1. **[Submit]\(送信\)** をクリックします。

パスワードの変更後、Azure AD DS で新しいパスワードを使用したり、マネージド ドメインに参加しているコンピューターに正常にサインインしたりできるようになるまでには数分かかります。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * マネージド ドメイン用に DNS と仮想ネットワークの設定を構成する
> * Azure AD DS インスタンスを作成する
> * 管理ユーザーをドメイン管理に追加する
> * Azure AD DS のユーザー アカウントを有効にしてパスワード ハッシュを生成する

このマネージド ドメインの動作を確認するために、仮想マシンを作成してドメインに参加させます。

> [!div class="nextstepaction"]
> [Windows Server 仮想マシンをマネージド ドメインに参加させる](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
