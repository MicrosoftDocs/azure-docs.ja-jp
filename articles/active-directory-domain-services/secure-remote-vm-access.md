---
title: Azure AD Domain Services でリモート VM アクセスをセキュリティで保護する | Microsoft Docs
description: Azure Active Directory Domain Services マネージド ドメインでリモート デスクトップ サービスがデプロイされているネットワーク ポリシー サーバー (NPS) と Azure AD Multi-Factor Authentication を使用して、VM へのリモートアクセスをセキュリティで保護する方法について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: f0605cbd81d8131014a1f6a6bf30e3db0ce9aa90
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618928"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services の仮想マシンへのリモートアクセスをセキュリティで保護する

Azure Active Directory Domain Services (Azure AD DS) マネージド ドメインで実行される仮想マシン (VM) へのリモート アクセスをセキュリティで保護するには、リモート デスクトップ サービス (RDS) とネットワーク ポリシー サーバー (NPS) を使用できます。 Azure AD DS は、RDS 環境経由でアクセスを要求するときにユーザーを認証します。 セキュリティを強化するために、Azure AD Multi-Factor Authentication を統合して、サインイン イベント時に追加の認証プロンプトを表示できます。 Azure AD Multi-Factor Authentication は、NPS の拡張機能を使用してこの機能を提供します。

> [!IMPORTANT]
> Azure AD DS マネージド ドメイン内の VM に安全に接続するには、仮想ネットワーク内でプロビジョニングするフル プラットフォーム マネージド PaaS サービスである Azure Bastion を使用することをお勧めします。 要塞ホストは、SSL 経由で Azure portal に直接、安全かつシームレスなリモート デスクトップ プロトコル (RDP) 接続を VM に提供します。 要塞ホスト経由で接続する場合、VM にはパブリック IP アドレスは必要ありません。また、ネットワーク セキュリティ グループを使用して、TCP ポート 3389 で RDP へのアクセスを公開する必要もありません。
>
> サポートされているすべてのリージョンで Azure Bastion を使用することを強くお勧めします。 Azure Bastion を利用できないリージョンでは、Azure Bastion を利用できるようになるまで、この記事で説明されている手順に従ってください。 Azure AD DS ではすべての受信 RDP トラフィックが許可されるため、参加している VM にパブリック IP アドレスを割り当てる際は注意してください。
>
> 詳細については、[Azure Bastion][bastion-overview] に関するページを参照してください。

この記事では Azure AD DS で RDS を構成し、必要に応じて Azure AD Multi-Factor Authentication NPS 拡張機能を使用する方法について説明します。

![リモート デスクトップ サービス (RDS) の概要](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下のリソースが必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services のマネージド ドメインを作成して構成][create-azure-ad-ds-instance]します。
* Azure Active Directory Domain Services 仮想ネットワーク内に作成された *ワークロード* サブネット。
    * 必要に応じて、[Azure Active Directory Domain Services マネージド ドメイン用の仮想ネットワークを構成します][configure-azureadds-vnet]。
* Azure AD テナントの *Azure AD DC administrators* グループのメンバーであるユーザー アカウント。

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>リモート デスクトップ環境のデプロイと構成

開始するには、Windows Server 2016 または Windows Server 2019 を実行する Azure VM を少なくとも 2 つ以上作成します。 リモート デスクトップ (RD) 環境の冗長性と高可用性を実現するために、後で追加のホストを追加して負荷分散することができます。

推奨される RDS デプロイには、次の 2 つの VM が含まれます。

* *RDGVM01* - RD 接続ブローカー サーバー、RD Web アクセス サーバー、および RD ゲートウェイ サーバーを実行します。
* *RDSHVM01* - RD セッション ホスト サーバーを実行します。

VM が Azure AD DS 仮想ネットワークの *ワークロード* サブネットにデプロイされていることを確認してから、VM をマネージド ドメインに参加させます。 詳細については、[Windows Server VM を作成してマネージド ドメインに参加させる方法][tutorial-create-join-vm]についての記事を参照してください。

RD 環境をデプロイするには、いくつかの手順が必要です。 既存の RD デプロイ ガイドは、特に変更することなくマネージド ドメインで使用できます。

1. *contosoadmin* などの *Azure AD DC 管理者* グループの一部であるアカウントを使用して、RD 環境用に作成された VM にサインインします。
1. RDS を作成および構成するには、既存の[リモート デスクトップ環境のデプロイ ガイド][deploy-remote-desktop]を参照してください。 必要に応じて、Azure VM 全体に RD サーバー コンポーネントを配布します。
    * Azure AD DS に固有 - RD ライセンスを構成するときは、導入ガイドに記載されているように、 **[ユーザー単位]** ではなく、 **[Per Device]\(デバイス単位\)** モードに設定します。
1. Web ブラウザーを使用してアクセスを提供する場合は、[ユーザーのリモート デスクトップ Web クライアントを設定します][rd-web-client]。

RD をマネージド ドメインにデプロイすると、オンプレミスの AD DS ドメインの場合と同様に、サービスを管理および使用できます。

## <a name="deploy-and-configure-nps-and-the-azure-ad-mfa-nps-extension"></a>NPS と Azure AD MFA NPS 拡張機能をデプロイして構成する

ユーザー サインイン エクスペリエンスのセキュリティを強化するには、必要に応じて RD 環境を Azure AD Multi-Factor Authentication と統合できます。 この構成では、サインイン時にユーザー ID を確認するための追加のプロンプトが表示されます。

この機能を提供するために、Azure AD Multi-Factor Authentication NPS 拡張機能と共に、追加のネットワーク ポリシー サーバー (NPS) が環境にインストールされます。 この拡張機能は Azure AD と統合され、多要素認証プロンプトの状態を要求して返すことができます。

ユーザーは [Azure AD Multi-Factor Authentication を使用するには登録する必要があります][user-mfa-registration]。これには、追加の Azure AD ライセンスが必要になる場合があります。

Azure AD Multi-Factor Authentication を Azure AD DS リモート デスクトップ環境に統合するには、NPS サーバーを作成し、拡張機能をインストールします。

1. Azure AD DS 仮想ネットワーク内の *ワークロード* サブネットに接続されている、追加の Windows Server 2016 または 2019 VM (*NPSVM01* など) を作成します。 VM をマネージド ドメインに参加させます。
1. *contosoadmin* などの *Azure AD DC 管理者* グループの一部であるアカウントとして、NPS VM にサインインします。
1. **サーバー マネージャー** で、 **[役割と機能の追加]** を選択し、 *[ネットワーク ポリシーとアクセス サービス]* ロールをインストールします。
1. [Azure AD MFA NPS 拡張機能のインストールと構成][nps-extension]については、既存のハウツー記事を参照してください。

NPS サーバーと Azure AD Multi-Factor Authentication NPS 拡張機能をインストールしたら、次のセクションを完了して RD 環境で使用できるように構成します。

## <a name="integrate-remote-desktop-gateway-and-azure-ad-multi-factor-authentication"></a>リモート デスクトップ ゲートウェイと Azure AD Multi-Factor Authentication の統合

Azure AD Multi-Factor Authentication NPS 拡張機能を統合するには、[ネットワーク ポリシー サーバー (NPS) 拡張機能と Azure AD を使用してリモート デスクトップ ゲートウェイ インフラストラクチャを統合する方法][azure-mfa-nps-integration]に関する既存のハウツー記事を使用してください。

マネージド ドメインと統合するには、次の追加の構成オプションが必要です。

1. [Active Directory に NPS サーバーを登録][register-nps-ad]しないでください。 マネージド ドメインでは、この手順は失敗します。
1. [ネットワーク ポリシーを構成した手順 4][create-nps-policy] で、 **[ユーザー アカウントのダイヤルイン プロパティを無視する]** チェック ボックスもオンにします。
1. NPS サーバーと Azure AD Multi-Factor Authentication NPS 拡張機能に Windows Server 2019 を使用する場合は、次のコマンドを実行して、NPS サーバーが正常に通信できるようにセキュリティで保護されたチャネルを更新します。

    ```powershell
    sc sidtype IAS unrestricted
    ```

ユーザーは、Microsoft Authenticator アプリでのテキスト メッセージやプロンプトなど、サインイン時に追加の認証要素の入力を求められるようになりました。

## <a name="next-steps"></a>次のステップ

デプロイの回復性を向上させる方法の詳細については、「[リモート デスクトップ サービス - 高可用性][rds-high-availability]」を参照してください。

ユーザー サインインをセキュリティで保護する方法の詳細については、「[しくみ:Azure AD Multi-Factor Authentication][concepts-mfa]」参照してください。

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability