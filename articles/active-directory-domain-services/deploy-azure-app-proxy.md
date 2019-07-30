---
title: Azure Active Directory Domain Services:Azure Active Directory アプリケーション プロキシのデプロイ | Microsoft Docs
description: Azure Active Directory Domain Services マネージド ドメインで Azure AD アプリケーション プロキシを使用します
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: e9ccbd81a8efeb69ea073d89edd6579a6d59699a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473604"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインに Azure AD アプリケーション プロキシをデプロイします
Azure Active Directory (AD) アプリケーション プロキシを使用して、オンプレミス アプリケーションを発行してインターネット経由でアクセスできるようにすることで、リモート ワーカーをサポートできます。 Azure AD Domain Services を使用して、オンプレミスで実行しているレガシ アプリケーションを Azure インフラストラクチャ サービスにリフトアンドシフトできるようになりました。 その後、Azure AD アプリケーション プロキシを使用してこれらのアプリケーションを発行し、組織内のユーザーに安全なリモート アクセスを提供することができます。

Azure AD アプリケーション プロキシを初めて使用する場合は、次の記事でこの機能の詳細を確認してください:「[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](../active-directory/manage-apps/application-proxy.md)」。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始する前に
この記事に記載されているタスクを実行するには、次が必要です。

1. 有効な **Azure サブスクリプション**。
2. **Azure AD ディレクトリ** - オンプレミスのディレクトリと同期されているか、クラウドのみのディレクトリ。
3. Azure AD アプリケーション プロキシを使用するには、**Azure AD Basic または Premium のライセンス**が必要です。
4. **Azure AD ドメイン サービス** が Azure AD ディレクトリに対して有効である必要があります。 有効になっていない場合は、 [作業の開始に関するガイド](create-instance.md)に記載されているすべてのタスクを実行してください。

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>タスク 1 - Azure AD ディレクトリの Azure AD アプリケーション プロキシを有効にする
AD ディレクトリの Azure AD アプリケーション プロキシを有効にするには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。

2. **[Azure Active Directory]** をクリックして、ディレクトリの概要を表示します。 **[エンタープライズ アプリケーション]** をクリックします。

    ![Azure AD ディレクトリの選択](./media/app-proxy/app-proxy-enable-start.png)
3. **[アプリケーション プロキシ]** をクリックします。 Azure AD Basic または Azure AD Premium サブスクリプションがない場合は、試用版を有効にするためのオプションが表示されます。 **[Enable Application Proxy? (アプリケーション プロキシの有効化)]** を **[Enable (有効化)]** に切り替え、 **[保存]** をクリックします。

    ![アプリケーション プロキシを有効にする](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. コネクタをダウンロードするには、 **[コネクタ]** ボタンをクリックします。

    ![コネクタのダウンロード](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. ダウンロード ページでライセンス条項とプライバシー アグリーメントに同意し、 **[ダウンロード]** ボタンをクリックします。

    ![ダウンロードの確認](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>タスク 2 - Azure AD アプリケーション プロキシ コネクタをデプロイするためのドメイン参加済み Windows Server をプロビジョニングする
Azure AD アプリケーション プロキシ コネクタをインストールできる、ドメイン参加済みの Windows Server 仮想マシンが必要です。 一部のアプリケーションでは、コネクタをインストールするサーバーを複数プロビジョニングすることもできます。 このデプロイ オプションにより、可用性が向上し、負荷の高い認証を処理できます。

Azure AD Domain Services のマネージド ドメインを有効化済みの同じ仮想ネットワーク (または接続/ピアリングされている仮想ネットワーク) 上でコネクタ サーバーをプロビジョニングします。 同様に、アプリケーション プロキシを経由して発行するアプリケーションをホストするサーバーを同じ Azure 仮想ネットワークにインストールする必要があります。

コネクタ サーバーをプロビジョニングするには、[Windows 仮想マシンのマネージド ドメインへの参加](active-directory-ds-admin-guide-join-windows-vm.md)に関するページで説明されているタスクに従います。


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>タスク 3 - Azure AD アプリケーション プロキシ コネクタをインストールして登録する
これまでに、Windows Server 仮想マシンをプロビジョニングしてマネージド ドメインに参加させました。 このタスクでは、Azure AD アプリケーション プロキシ コネクタをこの仮想マシンにインストールします。

1. Azure AD Web アプリケーション プロキシ コネクタをインストールする VM に、コネクタのインストール パッケージをコピーします。

2. 仮想マシンで **AADApplicationProxyConnectorInstaller.exe** を実行します。 ソフトウェア ライセンス条項に同意します。

    ![インストールの条項に同意する](./media/app-proxy/app-proxy-install-connector-terms.png)
3. インストール時に、Azure AD ディレクトリのアプリケーション プロキシにコネクタを登録するように求められます。
   * **Azure AD グローバル管理者の資格情報**を指定します。 グローバル管理者のテナントは、Microsoft Azure の資格情報とは異なる場合があります。
   * コネクタの登録に使用する管理者アカウントは、アプリケーション プロキシ サービスを有効にしたディレクトリと同じディレクトリに属している必要があります。 たとえば、テナントのドメインが contoso.com の場合、管理者は admin@contoso.com またはそのドメイン上の他の有効なエイリアスであることが必要です。
   * コネクタをインストールするサーバーで [IE セキュリティ強化の構成] がオンになっていると、登録画面がブロックされることがあります。 アクセスを許可するには、エラー メッセージに示された指示に従ってください。 Internet Explorer セキュリティ強化の構成が無効になっていることを確認します。
   * コネクタの登録が成功しない場合は、「 [アプリケーション プロキシのトラブルシューティング](../active-directory/manage-apps/application-proxy-troubleshoot.md)」をご覧ください。

     ![コネクタのインストール完了](./media/app-proxy/app-proxy-connector-installed.png)
4. コネクタが適切に動作していることを確認するには、Azure AD アプリケーション プロキシ コネクタのトラブルシューティング ツールを実行します。 トラブルシューティング ツールを実行すると、正常であることを示すレポートが表示されます。

    ![トラブルシューティング ツールの正常終了](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Azure AD ディレクトリのアプリケーション プロキシのページに、新しくインストールしたコネクタが表示されます。

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> 複数のサーバーにコネクタをインストールして、Azure AD アプリケーション プロキシ経由で発行したアプリケーションを認証するための高可用性を保証することができます。 マネージド ドメインに参加している他のサーバーで、この記事で説明したコネクタのインストール手順を同様に実行します。
>
>

## <a name="next-steps"></a>次の手順
Azure AD アプリケーション プロキシを設定し、Azure AD Domain Services のマネージド ドメインと統合しました。

* **アプリケーションを Azure 仮想マシンに移行する:** オンプレミスのサーバーからマネージド ドメインに参加している Azure 仮想マシンにアプリケーションをリフトアンドシフトすることができます。 これは、サーバーをオンプレミスで実行するインフラストラクチャ コストをなくすのに役立ちます。

* **Azure AD アプリケーション プロキシを使用してアプリケーションを発行する:** Azure AD アプリケーション プロキシを使用して、Azure 仮想マシンで実行されるアプリケーションを発行します。 詳細については、[Azure AD アプリケーション プロキシを使用したアプリケーションの発行](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)に関するページを参照してください。


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>デプロイの注意 - Azure AD アプリケーション プロキシを使用して IWA (統合 Windows 認証) アプリケーションを発行する
統合 Windows 認証 (IWA) を使用してアプリケーションへのシングル サインオンができるようにするには、ユーザーに偽装して代理でトークンを送受信できるように、アプリケーション プロキシ コネクタにアクセス許可を付与します。 コネクタに Kerberos の制約付き委任 (KCD) を構成して、マネージド ドメインのリソースへのアクセスに必要なアクセス許可を付与します。 セキュリティ強化のため、マネージド ドメインでリソースベースの KCD メカニズムを使用します。


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Azure AD アプリケーション プロキシ コネクタでリソースベースの Kerberos の制約付き委任を有効にする
Azure アプリケーション プロキシ コネクタで Kerberos の制約付き委任 (KCD) を構成し、マネージド ドメインでユーザーに偽装できるようにする必要があります。 Azure AD Domain Services のマネージド ドメインでは、ドメイン管理者特権がありません。 したがって、**マネージド ドメインで従来のアカウント レベルの KCD を構成することはできません**。

この[記事](deploy-kcd.md)で説明されているように、リソースベースの KCD を使用します。

> [!NOTE]
> AD PowerShell コマンドレットを使用してマネージド ドメインを管理するには、"AAD DC Administrators" グループのメンバーである必要があります。
>
>

Get-ADComputer PowerShell コマンドレットを使用して、Azure AD アプリケーション プロキシ コネクタがインストールされているコンピューターの設定を取得します。
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

その後、Set-ADComputer コマンドレットを使用して、リソース サーバーにリソースベースの KCD を設定します。
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

マネージド ドメインに複数のアプリケーション プロキシ コネクタをデプロイした場合は、そのコネクタ インスタンスごとにリソースベースの KCD を構成する必要があります。


## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](create-instance.md)
* [マネージド ドメインで Kerberos の制約付き委任を構成する](deploy-kcd.md)
* [Kerberos の制約付き委任の概要](https://technet.microsoft.com/library/jj553400.aspx)
