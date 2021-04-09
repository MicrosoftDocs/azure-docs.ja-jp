---
title: Azure AD Domain Services 用の Azure AD アプリケーション プロキシをデプロイする | Microsoft Docs
description: Azure Active Directory Domain Services マネージド ドメインに Azure Active Directory アプリケーション プロキシをデプロイして構成することにより、リモート ワーカーに対して内部アプリケーションへのセキュリティで保護されたアクセスを提供する方法について説明します
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 8305b2190cf3b157973f5844c3237ffe73adba66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619918"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services マネージド ドメイン内の内部アプリケーションに安全にアクセスするために Azure AD アプリケーション プロキシをデプロイする

Azure AD Domain Services (Azure AD DS) を使用して、オンプレミスで実行されているレガシ アプリケーションを Azure にリフトアンドシフトすることができます。 その後、Azure Active Directory (AD) アプリケーション プロキシにより、インターネット経由でアクセスできるように、Azure AD DS マネージド ドメインの一部として内部アプリケーションを安全に公開することで、リモート ワーカーをサポートできます。

Azure AD アプリケーション プロキシを使用したことがなく、詳細を確認したい場合は、[内部アプリケーションに安全なリモート アクセスを提供する方法](../active-directory/manage-apps/application-proxy.md)に関する記事を参照してください。

この記事では、Azure AD アプリケーション プロキシ コネクタを作成および構成して、マネージド ドメイン内のアプリケーションへの安全なアクセスを提供する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
    * Azure AD アプリケーション プロキシを使用するには、**Azure AD Premium のライセンス** が必要です。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services のマネージド ドメインを作成して構成][create-azure-ad-ds-instance]します。

## <a name="create-a-domain-joined-windows-vm"></a>ドメインに参加している Windows VM を作成する

環境内で実行されているアプリケーションにトラフィックをルーティングするには、Azure AD アプリケーション プロキシ コネクタ コンポーネントをインストールします。 この Azure AD アプリケーション プロキシ コネクタは、マネージド ドメインに参加している Windows Server 仮想マシン (VM) にインストールする必要があります。 アプリケーションによっては、それぞれにコネクタがインストールされている複数のサーバーをデプロイできます。 このデプロイ オプションにより、可用性が向上し、負荷の高い認証を処理できます。

Azure AD アプリケーション プロキシ コネクタを実行する VM は、同じか、またはピアリングされた仮想ネットワーク上に、マネージド ドメインとして存在する必要があります。 アプリケーション プロキシを使用して発行するアプリケーションをホストする VM も、同じ Azure 仮想ネットワークにデプロイする必要があります。

Azure AD アプリケーション プロキシ コネクタ用の VM を作成するには、次の手順を実行します。

1. [カスタム OU を作成します](create-ou.md)。 このカスタム OU を管理する権限を、マネージド ドメイン内のユーザーに委任できます。 Azure AD アプリケーション プロキシ用の VM とアプリケーションを実行する VM は、既定の *AAD DC Computers* OU ではなく、カスタム OU の一部である必要があります。
1. [仮想マシンをドメインに参加させます][create-join-windows-vm]。Azure AD アプリケーション プロキシ コネクタを実行しているものと、アプリケーションを実行しているものの両方をマネージド ドメインに参加させます。 前の手順のカスタム OU 内に、これらのコンピューター アカウントを作成します。

## <a name="download-the-azure-ad-application-proxy-connector"></a>Azure AD アプリケーション プロキシ コネクタをダウンロードする

次の手順を実行して、Azure AD アプリケーション プロキシ コネクタをダウンロードします。 ダウンロードしたセットアップ ファイルは、次のセクションでアプリケーション プロキシ VM にコピーします。

1. Azure AD で *エンタープライズ管理者* のアクセス許可を持っているユーザー アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。
1. ポータルの上部で **[Azure Active Directory]** を検索して選択し、次に **[エンタープライズ アプリケーション]** を選択します。
1. 左側のメニューから **[アプリケーション プロキシ]** を選択します。 最初のコネクタを作成してアプリケーション プロキシを有効にするには、**コネクタをダウンロードする** ためのリンクを選択します。
1. ダウンロード ページでライセンス条項とプライバシー アグリーメントに同意し、 **[使用条件に同意してダウンロードする]** を選択します。

    ![Azure AD アプリケーション プロキシ コネクタをダウンロードする](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Azure AD アプリケーション プロキシ コネクタをインストールして登録する

VM を Azure AD アプリケーション プロキシ コネクタとして使用できる状態になったら、Azure portal からダウンロードしたセットアップ ファイルをコピーして実行します。

1. Azure AD アプリケーション プロキシ コネクタのセットアップ ファイルを VM にコピーします。
1. *AADApplicationProxyConnectorInstaller.exe* などのセットアップ ファイルを実行します。 ソフトウェア ライセンス条項に同意します。
1. インストール時に、Azure AD ディレクトリのアプリケーション プロキシにコネクタを登録するように求められます。
   * Azure AD ディレクトリの全体管理者の資格情報を指定します。 Azure AD の全体管理者の資格情報は、ポータルの Azure 資格情報とは異なる場合があります

        > [!NOTE]
        > コネクタの登録に使用する全体管理者アカウントは、アプリケーション プロキシ サービスを有効にしたのと同じディレクトリに属している必要があります。
        >
        > たとえば、Azure AD ドメインが *contoso.com* の場合、全体管理者は `admin@contoso.com` か、そのドメインの別の有効なエイリアスである必要があります。

   * コネクタをインストールする VM で [Internet Explorer セキュリティ強化の構成] がオンになっていると、登録画面がブロックされることがあります。 アクセスを許可するには、エラー メッセージの指示に従うか、インストールプロセス中に Internet Explorer のセキュリティ強化をオフにします。
   * コネクタの登録が失敗する場合は、[アプリケーション プロキシのトラブルシューティング](../active-directory/manage-apps/application-proxy-troubleshoot.md)に関する記事をご覧ください。
1. セットアップの最後に、送信プロキシを使用している環境に対してメモが表示されます。 送信プロキシ経由で動作するように Azure AD アプリケーション プロキシ コネクタを構成するには、提供されたスクリプト (`C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` など) を実行します。
1. Azure portal の [アプリケーション プロキシ] ページで、次の例に示すように、新しいコネクタが *[アクティブ]* の状態で一覧表示されます。

    ![Azure portal でアクティブと表示されている新しい Azure AD アプリケーション プロキシ コネクタ](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Azure AD アプリケーション プロキシを介して認証を行うアプリケーションの高可用性を実現するために、複数の VM にコネクタをインストールできます。 前のセクションと同じ手順を繰り返して、マネージド ドメインに参加している他のサーバーにコネクタをインストールします。

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>リソースベースの Kerberos の制約付き委任を有効にする

統合 Windows 認証 (IWA) を使用してアプリケーションへのシングル サインオンを使用するには、Azure AD アプリケーション プロキシ コネクタに、ユーザーに偽装して代理でトークンを送受信するためのアクセス許可を付与します。 これらのアクセス許可を付与するには、コネクタに対してマネージド ドメインのリソースにアクセスするための Kerberos の制約付き委任 (KCD) を構成します。 マネージド ドメインのドメイン管理者特権がないため、マネージド ドメインに従来のアカウントレベルの KCD を構成することはできません。 代わりに、リソースベースの KCD を使用します。

詳細については、「[Azure Active Directory Domain Services で Kerberos の制約付き委任 (KCD) を構成する](deploy-kcd.md)」を参照してください。

> [!NOTE]
> 次の PowerShell コマンドレットを実行するには、Azure AD テナントの *Azure AD DC 管理者* グループのメンバーであるユーザー アカウントにサインインする必要があります。
>
> アプリケーション プロキシ コネクタ VM とアプリケーション VM 用のコンピューター アカウントは、リソースベースの KCD を構成するアクセス許可を持つカスタム OU 内にある必要があります。 組み込みの *AAD DC Computers* コンテナー内にあるコンピューター アカウントに対して、リソースベースの KCD を構成することはできません。

[Get-ADComputer][Get-ADComputer] を使用して、Azure AD アプリケーション プロキシ コネクタがインストールされているコンピューターの設定を取得します。 ドメインに参加している管理 VM で、"*Azure AD DC 管理者*" グループのメンバーであるユーザー アカウントとしてログインし、次のコマンドレットを実行します。

次の例では、*appproxy.aaddscontoso.com* という名前のコンピューター アカウントに関する情報を取得します。 前の手順で構成した Azure AD アプリケーション プロキシ VM に独自のコンピューター名を指定します。

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Azure AD アプリケーション プロキシの背後にあるアプリを実行するアプリケーション サーバーごとに [Set-ADComputer][Set-ADComputer] PowerShell コマンドレットを使用して、リソースベースの KCD を構成します。 次の例では、Azure AD アプリケーション プロキシ コネクタに *appserver.aaddscontoso.com* コンピューターを使用するためのアクセス許可が付与されます。

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

複数の Azure AD アプリケーション プロキシ コネクタをデプロイする場合は、コネクタ インスタンスごとにリソースベースの KCD を構成する必要があります。

## <a name="next-steps"></a>次のステップ

Azure AD DS と統合された Azure AD アプリケーション プロキシを使用して、ユーザーがアクセスできるようにアプリケーションを発行します。 詳しくは、[Azure AD アプリケーション プロキシを使用したアプリケーションの発行](../active-directory/manage-apps/application-proxy-add-on-premises-application.md)に関する記事をご覧ください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer