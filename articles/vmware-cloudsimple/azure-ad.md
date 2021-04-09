---
title: Azure VMware Solution by CloudSimple - Private Cloud 上で ID ソースとして Azure AD を使用する
description: CloudSimple Private Cloud 上で ID プロバイダーとして Azure AD を追加し、Azure から CloudSimple にアクセスするユーザーを認証する方法について説明します。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f55a0f52f5e028f9cbf7a9fabbb3c24ad43c3800
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898608"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>CloudSimple Private Cloud 上で vCenter の ID プロバイダーとして Azure AD を使用する

VMware 管理者が vCenter にアクセスするには、Azure Active Directory (Azure AD) で認証できるように CloudSimple Private Cloud vCenter を設定します。 シングル サインオン ID ソースが設定されると、**cloudowner** ユーザーは ID ソースから vCenter にユーザーを追加できるようになります。  

Active Directory ドメインとドメイン コントローラーは、次のいずれかの方法で設定できます。

* オンプレミスで実行される Active Directory ドメインとドメイン コントローラー
* ご利用の Azure サブスクリプションで仮想マシンとして Azure 上で実行される Active Directory ドメインとドメイン コントローラー
* CloudSimple Private Cloud で実行される新しい Active Directory ドメインとドメイン コントローラー
* Azure Active Directory サービス

このガイドでは、Azure AD を ID ソースとして設定するために必要なタスクについて説明します。  オンプレミスの Active Directory または Azure で実行されている Active Directory を使用した ID ソースの設定の詳細な手順については、「[Active Directory を使用するように vCenter ID ソースを設定する](set-vcenter-identity.md)」を参照してください。

## <a name="about-azure-ad"></a>Azure AD の概要

Azure AD は、マイクロソフトが提供する、マルチテナントに対応したクラウド ベースのディレクトリと ID の管理サービスです。  Azure AD には、ユーザーが Azure 上のさまざまなサービスを認証およびアクセスできる、スケーラブルで一貫性のある信頼性の高い認証メカニズムが用意されています。  また、すべてのサードパーティ サービスに対して、認証/ID ソースとして Azure AD を使用する Secure LDAP サービスが提供されます。  Azure AD は、コア ディレクトリ サービス、高度な ID ガバナンス、およびアプリケーション アクセス管理を組み合わせたもので、Private Cloud を管理するユーザーに Private Cloud へのアクセス権を付与するために使用できます。

vCenter で ID ソースとして Azure AD を使用するには、Azure AD と Azure AD Domain Services を設定する必要があります。 以下の手順に従います。

1. [Azure AD と Azure AD Domain Services を設定する方法](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Private Cloud vCenter 上で ID ソースを設定する方法](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Azure AD と Azure AD Domain Services を設定する

始める前に、全体管理者特権を使用して Azure サブスクリプションにアクセスする必要があります。  次の手順では、一般的なガイドラインを示します。 詳細については、Azure のドキュメントを参照してください。

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> 既に Azure AD をお持ちの場合は、このセクションを省略できます。

1. [Azure AD のドキュメント](../active-directory/fundamentals/active-directory-whatis.md)で説明されているように、サブスクリプションに Azure AD を設定します。
2. 「[Azure Active Directory Premium エディションにサインアップする](../active-directory/fundamentals/active-directory-get-started-premium.md)」の説明に従って、サブスクリプション上で Azure Active Directory Premium を有効にします。
3. [Azure Active Directory へのカスタム ドメイン名の追加](../active-directory/fundamentals/add-custom-domain.md)に関する記事の説明に従って、カスタム ドメイン名を設定し、カスタム ドメイン名を確認します。
    1. Azure で提供される情報を使用して、ドメイン レジストラー上で DNS レコードを設定します。
    2. カスタム ドメイン名をプライマリ ドメインに設定します。

必要に応じて、その他の Azure AD 機能を構成することができます。  これらは、Azure AD での vCenter 認証を有効にするために必須ではありません。

### <a name="azure-ad-domain-services"></a>Azure AD Domain Services

> [!NOTE]
> これは、vCenter の ID ソースとして Azure AD を有効にする重要な手順です。  問題を回避するために、必ずすべての手順を正しく実行してください。

1. [Azure portal を使用して Azure Active Directory Domain Services を有効にする](../active-directory-domain-services/tutorial-create-instance.md)方法の記事の説明に従って Azure AD Domain Services を有効にします。
2. [Azure portal を使用して Azure Active Directory Domain Services を有効にする](../active-directory-domain-services/tutorial-create-instance.md)方法の記事の説明に従って、Azure AD Domain Services によって使用されるネットワークを設定します。
3. [Azure portal を使用して Azure Active Directory Domain Services を有効にする](../active-directory-domain-services/tutorial-create-instance.md)方法の記事の説明に従って、Azure AD Domain Services を管理する管理者グループを構成します。
4. [Azure Active Directory Domain Services を有効にする](../active-directory-domain-services/tutorial-create-instance.md)方法の記事の説明に従って、Azure AD Domain Services の DNS 設定を更新します。  インターネット経由で AD に接続する場合は、Azure AD Domain Services のパブリック IP アドレスの DNS レコードをドメイン名に設定します。
5. ユーザーのパスワード ハッシュ同期を有効にします。  この手順で、NT LAN Manager (NTLM) および Kerberos 認証に必要なパスワード ハッシュが Azure AD Domain Services との間で同期されるようになります。 パスワード ハッシュの同期をセットアップすると、ユーザーは自社の資格情報を使用して、マネージド ドメインにサインインできます。 「[Azure Active Directory Domain Services とのパスワード ハッシュの同期を有効にする](../active-directory-domain-services/tutorial-create-instance.md)」を参照してください。
    1. クラウド専用ユーザーが存在する場合は、<a href="https://myapps.microsoft.com/" target="_blank">Azure AD アクセス パネル</a>を使用してパスワードを変更し、パスワード ハッシュが NTLM または Kerberos で必要な形式で格納されるようにする必要があります。  [クラウド専用ユーザー アカウントのマネージド ドメインとのパスワード ハッシュ同期を有効にする方法](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)の記事の手順に従います。  この手順は、個々のユーザーと、Azure portal または Azure AD PowerShell コマンドレットを使用して Azure AD ディレクトリに作成される新しいユーザーに対して行う必要があります。 Azure AD Domain Services へのアクセスを必要とするユーザーは、<a href="https://myapps.microsoft.com/" target="_blank">Azure AD アクセス パネル</a>を使用し、そのプロファイルにアクセスしてパスワードを変更する必要があります。

        > [!NOTE]
        > 組織にクラウド専用ユーザー アカウントが存在する場合、Azure Active Directory Domain Services を使用する必要があるすべてのユーザーは、自分のパスワードを変更しなければなりません。 クラウド専用ユーザー アカウントとは、Azure Portal または Azure AD PowerShell コマンドレットを使って Azure AD ディレクトリに作成されたアカウントです。 そのようなユーザー アカウントは、オンプレミス ディレクトリとの間で同期されません。

    2. オンプレミスの Active Directory からパスワードを同期する場合は、[Active Directory のドキュメント](../active-directory-domain-services/tutorial-configure-password-hash-sync.md)の手順に従ってください。

6.  [Azure AD Domain Services のマネージド ドメイン用の Secure LDAP (LDAPS) の構成](../active-directory-domain-services/tutorial-configure-ldaps.md)に関する記事の説明に従って、Azure Active Directory Domain Services 上で Secure LDAP を構成します。
    1. [Secure LDAP 用の証明書の取得](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)に関する Azure のトピックの説明に従って、Secure LDAP に使用する証明書をアップロードします。  CloudSimple では、証明機関から発行された署名済み証明書を使用して、vCenter が証明書を信頼できるようにすることをお勧めします。
    2. [Azure AD Domain Services のマネージド ドメイン用に Secure LDAP (LDAPS) を有効にする](../active-directory-domain-services/tutorial-configure-ldaps.md)方法の説明に従って、Secure LDAP を有効にします。
    3. ID ソースの構成時に、vCenter に使用する証明書の公開部分 (秘密キーなし) を .cer 形式で保存します。
    4. Azure AD Domain Services へのインターネット アクセスが必要な場合は、[Allow secure access to LDAP over internet]\(インターネット経由での LDAP へのセキュリティで保護されたアクセスを許可する\) オプションを有効にします。
    5. TCP ポート 636 の Azure AD Domain Services NSG の受信セキュリティ規則を追加します。

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Private Cloud vCenter 上で ID ソースを設定する

1. Private Cloud vCenter の[特権を昇格](escalate-private-cloud-privileges.md)します。
2. ID ソースの設定に必要な構成パラメーターを収集します。

    | **オプション** | **説明** |
    |------------|-----------------|
    | **名前** | ID ソースの名前。 |
    | **Base DN for users** (ユーザーのベース DN) | ユーザーのベース識別名。  Azure AD の場合、次のように使用します。`OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  例: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`。|
    | **ドメイン名** | ドメインの FQDN (例: example.com)。 このテキスト ボックスには IP アドレスを指定しないでください。 |
    | **Domain alias** (ドメイン エイリアス) | *(省略可能)* ドメインの NetBIOS 名。 SSPI 認証を使用している場合は、Active Directory ドメインの NetBIOS 名を ID ソースのエイリアスとして追加します。 |
    | **Base DN for groups** (グループのベース DN) | グループのベース識別名。 Azure AD の場合、次のように使用します。`OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  例: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **Primary Server URL** (プライマリ サーバーの URL) | ドメインのプライマリ ドメイン コントローラー LDAP サーバー。<br><br>「`ldaps://hostname:port`」の形式を使用します。 LDAPS 接続の場合、通常、このポートは 636 です。 <br><br>プライマリまたはセカンダリの LDAP URL で `ldaps://` を使用するときは、Active Directory サーバーの LDAPS エンドポイントに対して信頼を確立する証明書が必要です。 |
    | **Secondary server URL** (セカンダリ サーバーの URL) | フェールオーバーに使用するセカンダリ ドメイン コントローラー LDAP サーバーのアドレス。 |
    | **Choose certificate** (証明書の選択) | Active Directory LDAP サーバーまたは OpenLDAP サーバーの ID ソースで LDAPS を使用する場合は、URL テキスト ボックスに「`ldaps://`」と入力すると、[証明書の選択] ボタンが表示されます。 セカンダリの URL は必須ではありません。 |
    | **ユーザー名** | ユーザーおよびグループのベース DN への最小限の読み取り専用アクセス権を持つドメイン内のユーザーの ID。 |
    | **パスワード** | [ユーザー名] で指定したユーザーのパスワード。 |

3. 特権が昇格されたら、Private Cloud vCenter にサインインします。
4. 前の手順の値を使用して、「[vCenter にで ID ソースとして追加する](set-vcenter-identity.md#add-an-identity-source-on-vcenter)」の手順に従って、ID ソースとして Azure Active Directory を設定します。
5. VMware のトピック「[Add Members to a vCenter Single Sign-On Group (vCenter のシングル サインオン グループにメンバーを追加する)](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)」の説明に従って、Azure AD から vCenter グループにユーザーまたはグループを追加します。

> [!CAUTION]
> 新しいユーザーは、*Cloud-Owner-Group*、*Cloud-Global-Cluster-Admin-Group*、*Cloud-Global-Storage-Admin-Group*、*Cloud-Global-Network-Admin-Group*、または *Cloud-Global-VM-Admin-Group* にのみ追加する必要があります。  *Administrators* グループに追加されたユーザーは自動的に削除されます。  *Administrators* グループには、サービス アカウントのみを追加する必要があります。

## <a name="next-steps"></a>次のステップ

* [プライベート クラウド アクセス許可モデルの詳細](learn-private-cloud-permissions.md)
