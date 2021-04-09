---
title: Azure AD Domain Services に関してよく寄せられる質問 | Microsoft Docs
description: Azure Active Directory Domain Services の構成、管理、および可用性に関してよく寄せられる質問をいくつか読んで理解する
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/09/2021
ms.author: justinha
ms.openlocfilehash: 3d0f2b44f37cb318be2117b5dc5d8b42b418ff19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100090991"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Azure Active Directory (AD) Domain Services に関してよく寄せられる質問 (FAQ)

このページには、Azure Active Directory Domain Services に関してよく寄せられる質問への回答が記載されています。

## <a name="configuration"></a>構成

* [1 つの Azure AD ディレクトリに対して複数のマネージド ドメインを作成することはできますか。](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [クラシック仮想ネットワークで Azure AD Domain Services を有効にすることができますか。](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Azure Resource Manager 仮想ネットワークで Azure AD Domain Services を有効にすることはできますか。](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [既存のマネージド ドメインをクラシック仮想ネットワークから Resource Manager 仮想ネットワークに移行することはできますか。](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Azure AD Domain Services を Azure CSP (Cloud Solution Provider) サブスクリプション内で有効にできますか。](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [フェデレーション Azure AD ディレクトリの Azure AD Domain Services を有効にすることはできますか。Azure AD にパスワード ハッシュを同期していません。このディレクトリの Azure AD Domain Services を有効にすることはできますか。](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Azure AD Domain Services をサブスクリプション内の複数の仮想ネットワークで利用できますか。](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [PowerShell を使用して Azure AD Domain Services を有効にできますか。](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Resource Manager テンプレートを使用して Azure AD Domain Services を有効にできますか。](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Azure AD Domain Services のマネージド ドメインにドメイン コントローラーを追加することはできますか。](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [自分のディレクトリに招待したゲスト ユーザーは Azure AD Domain Services を使用できますか。](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [既存の Azure AD Domain Services マネージド ドメインを別のサブスクリプション、リソース グループ、リージョン、または仮想ネットワークに移動できますか。](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Azure AD Domain Services には高可用性オプションが含まれていますか。](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>1 つの Azure AD ディレクトリに対して複数のマネージド ドメインを作成することはできますか。
いいえ。 1 つの Azure AD ディレクトリに対して Azure AD Domain Services によって対応されるマネージド ドメインは 1 つだけ作成できます。

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>クラシック仮想ネットワークで Azure AD Domain Services を有効にすることができますか。
新しいデプロイでは、クラシック仮想ネットワークはサポートされません。 クラシック仮想ネットワークにデプロイされている既存のマネージド ドメインは、2023 年 3 月 1 日に廃止されるまで引き続きサポートされます。 既存のデプロイの場合、[クラシック仮想ネットワーク モデルから Resource Manager に Azure AD Domain Services を移行する](migrate-from-classic-vnet.md)ことができます。

詳細については、[公式の非推奨に関する通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)を参照してください。

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager 仮想ネットワークで Azure AD Domain Services を有効にすることはできますか。
はい。 Azure AD Domain Services は Azure Resource Manager 仮想ネットワークで有効にすることができます。 マネージド ドメインを作成するときに、クラシック Azure 仮想ネットワークは使用できなくなりました。

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>既存のマネージド ドメインをクラシック仮想ネットワークから Resource Manager 仮想ネットワークに移行することはできますか。
はい。 詳細については、「[クラシック仮想ネットワーク モデルから Resource Manager への Azure AD Domain Services の移行](migrate-from-classic-vnet.md)」を参照してください。

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Azure AD Domain Services を Azure CSP (Cloud Solution Provider) サブスクリプション内で有効にできますか。
はい。 詳細については、「[Azure Cloud Solution Provider (CSP) 用 Azure Active Directory (AD) Domain Services](csp.md)」を参照してください。

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>フェデレーション Azure AD ディレクトリの Azure AD Domain Services を有効にすることはできますか。 Azure AD にパスワード ハッシュを同期していません。 このディレクトリの Azure AD Domain Services を有効にすることはできますか。
いいえ。 NTLM または Kerberos を使用してユーザーを認証するには、Azure AD Domain Services でユーザー アカウントのパスワード ハッシュへのアクセスが必要です。 フェデレーション ディレクトリでは、パスワード ハッシュは Azure AD ディレクトリに格納されません。 そのため、Azure AD Domain Services は、このような Azure AD ディレクトリでは機能しません。

ただし、パスワード ハッシュの同期に Azure AD Connect を使用している場合は、パスワード ハッシュ値が Azure AD に格納されているため、Azure AD Domain Services を使用できます。

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Azure AD Domain Services をサブスクリプション内の複数の仮想ネットワークで利用できますか。
サービス自体は、このシナリオを直接サポートしていません。 マネージド ドメインは一度に 1 つの仮想ネットワークでのみ利用できます。 ただし、複数の仮想ネットワーク間で接続を構成して、Azure AD Domain Services を他の仮想ネットワークに公開することができます。 詳細については、[VPN ゲートウェイを使用して Azure の仮想ネットワークを接続する方法](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md)または[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)に関する記事を参照してください。

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>PowerShell を使用して Azure AD ドメイン サービスを有効にできますか。
はい。 詳細については、「[PowerShell を使用した Azure Active Directory Domain Services の有効化](powershell-create-instance.md)」を参照してください。

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Azure AD Domain Services を有効にできますか？
はい。Resource Manager テンプレートを使用して Azure AD Domain Services マネージド ドメインを作成できます。 管理用のサービス プリンシパルと Azure AD グループは、テンプレートをデプロイする前に、Azure portal または Azure PowerShell を使用して作成する必要があります。 詳細については、[Azure Resource Manager テンプレートを使用した Azure AD DS マネージド ドメインの作成](template-create-instance.md)に関するページを参照してください。 Azure portal で Azure AD Domain Services マネージド ドメインを作成する場合、追加のデプロイで使用するテンプレートをエクスポートするオプションもあります。

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインにドメイン コント ローラーを追加することはできますか。
いいえ。 マネージド ドメインは Azure AD Domain Services によって提供されるドメインです。 このドメインに対してドメイン コントローラーをプロビジョニング、構成、管理する必要はありません。 これらの管理アクティビティは、Microsoft からサービスとして提供されています。 そのため、マネージド ドメインにドメイン コントローラー (読み取り/書き込みや読み取り専用) をさらに追加することはできません。

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>自分のディレクトリに招待したゲスト ユーザーは Azure AD Domain Services を使用できますか。
いいえ。 [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md) 招待プロセスを使用して Azure AD ディレクトリに招待されたゲスト ユーザーは、Azure AD Domain Services の管理対象ドメインに同期されます。 ただし、これらのユーザーのパスワードは Azure AD ディレクトリに格納されません。 そのため、Azure AD Domain Services では、これらのユーザーの NTLM と Kerberos のハッシュをマネージド ドメインに同期することはできません。 このようなユーザーは、サインインすることも、マネージド ドメインにコンピューターを参加させることもできません。

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>既存の Azure AD Domain Services マネージド ドメインを別のサブスクリプション、リソース グループ、リージョン、または仮想ネットワークに移動できますか。
いいえ。 Azure AD Domain Services マネージド ドメインを作成した後は、そのマネージド ドメインを別のリソース グループ、仮想ネットワーク、サブスクリプションなどに移動することはできません。マネージド ドメインをデプロイするときに、最適なサブスクリプション、リソース グループ、リージョン、仮想ネットワークを慎重に選択してください。

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Azure AD Domain Services には高可用性オプションが含まれていますか。

はい。 各 Azure AD Domain Services のマネージド ドメインには、2 つのドメイン コントローラーが含まれています。 これらのドメイン コントローラーについては、お客様が管理または接続することはありません。これらはマネージド サービスの一部です。 Azure AD Domain Services を Availability Zones をサポートするリージョンにデプロイすると、ドメイン コントローラーはゾーン間に分散されます。 Availability Zones をサポートしていないリージョンの場合、ドメイン コントローラーは可用性セット間に分散されます。 この分散に対する構成オプションや管理制御はありません。 詳細については、「[Azure の仮想マシンの可用性オプション](../virtual-machines/availability.md)」を参照してください。

## <a name="administration-and-operations"></a>管理と操作

* [リモート デスクトップを使用してマネージド ドメインのドメイン コントローラーに接続できますか。](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Azure AD Domain Services を有効にしています。このドメインに参加しているドメイン コンピューターでは、どのユーザー アカウントを使用できますか。](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Azure AD Domain Services によって提供されるマネージド ドメインに対するドメイン管理者特権はありますか。](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [マネージド ドメインで LDAP または他の AD 管理ツールを使用してグループ メンバーシップを変更できますか。](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Azure AD ディレクトリに対して行った変更がマネージド ドメインに反映されるまで、どのくらいの時間がかかりますか。](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Azure AD Domain Services によって提供されるマネージド ドメインのスキーマは拡張できますか。](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [マネージド ドメインの DNS レコードを変更または追加できますか。](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [マネージド ドメインのパスワード有効期間ポリシーとはどのようなものですか。](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Azure AD Domain Services では、AD アカウントのロックアウトによる保護は提供されていますか。](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [Azure AD Domain Services 内で分散ファイル システム (DFS) とレプリケーションを構成できますか。](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Windows の更新プログラムは Azure AD Domain Services でどのように適用されますか。](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>リモート デスクトップを使用してマネージド ドメインのドメイン コントローラーに接続できますか。
いいえ。 リモート デスクトップを使用してマネージド ドメインのドメイン コントローラーに接続する権限はありません。 "*AAD DC 管理者*" グループのメンバーは、Active Directory 管理センター (ADAC) や AD PowerShell などの AD 管理ツールを使用して、マネージド ドメインを管理できます。 これらのツールは、"*リモート サーバー管理ツール*" 機能を使用して、マネージド ドメインに参加している Windows サーバーにインストールされます。 詳細については、「[チュートリアル:Azure Active Directory Domain Services のマネージド ドメインを構成および管理するための管理 VM を作成する](tutorial-create-management-vm.md)」を参照してください。

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Azure AD Domain Services を有効にしています。 このドメインに参加しているドメイン コンピューターでは、どのユーザー アカウントを使用できますか。
マネージド ドメインの一部であるすべてのユーザー アカウントを VM に参加させることができます。 "*AAD DC 管理者*" グループのメンバーには、マネージド ドメインに参加しているマシンへのリモート デスクトップ アクセス権が付与されます。

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services によって提供されるマネージド ドメインにドメイン管理者特権はありますか。
いいえ。 マネージド ドメインの管理特権は付与されません。 "*ドメイン管理者*" 特権と "*エンタープライズ管理者*" 特権は、ドメイン内では使用できません。 また、オンプレミスの Active Directory 内のドメイン管理者グループまたはエンタープライズ管理者グループのメンバーにも、マネージド ドメインのドメイン/エンタープライズ管理者特権は付与されません。

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>マネージド ドメインで LDAP または他の AD 管理ツールを使用してグループ メンバーシップを変更できますか。
Azure Active Directory から Azure AD Domain Services に同期されるユーザーとグループは、元のソースが Azure Active Directory であるため変更できません。 これには、AADDC Users のマネージド組織単位からカスタム組織単位へのユーザーまたはグループの移動が含まれます。 マネージド ドメインがソースのユーザーまたはグループは変更できます。  

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Azure AD ディレクトリに対して行った変更がマネージド ドメインに反映されるまで、どのくらいの時間がかかりますか。
Azure AD UI または PowerShell を使用して Azure AD ディレクトリに対して行った変更は、マネージド ドメインに自動的に同期されます。 この同期プロセスはバック グラウンドで実行されます。 この同期ですべてのオブジェクトの変更を完了するための期間は定義されていません。

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services によって提供されるマネージド ドメインのスキーマは拡張できますか。
いいえ。 スキーマは、Microsoft がマネージド ドメインを管理することで管理されます。 Azure AD Domain Services では、スキーマの拡張機能はサポートされていません。

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>マネージド ドメインの DNS レコードを変更または追加できますか。
はい。 "*AAD DC 管理者*" グループのメンバーには、マネージド ドメインの DNS レコードを変更できる "*DNS 管理者*" 特権が付与されています。 これらのユーザーは、マネージド ドメインに参加している Windows Server を実行しているマシン上で DNS マネージャー コンソールを使用して、DNS を管理できます。 DNS マネージャー コンソールを使用するには、"*リモート サーバー管理ツール*" オプション機能の一部である "*DNS サーバー ツール*" をサーバーにインストールします。 詳細については、「[Azure AD Domain Services マネージド ドメインで DNS を管理する](manage-dns.md)」を参照してください。

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>マネージド ドメインのパスワード有効期間ポリシーとはどのようなものですか。
Azure AD Domain Services のマネージド ドメインの既定のパスワード有効期間は 90 日間です。 このパスワード有効期間は、Azure AD で構成されているパスワード有効期間と同期されません。 そのため、ユーザーのパスワードが、マネージド ドメインでは期限切れだが、Azure AD ではまだ有効なことがあります。 このような場合、ユーザーは Azure AD のパスワードを変更する必要があり、この新しいパスワードがマネージド ドメインに同期されます。 マネージド ドメインの既定のパスワードの有効期間を変更する場合、[カスタム パスワード ポリシーを作成して構成](password-policy.md)することができます。

さらに、*DisablePasswordExpiration* の Azure AD パスワード ポリシーはマネージド ドメインに同期されます。 *DisablePasswordExpiration* が Azure AD のユーザーに適用されると、マネージド ドメインの同期されたユーザーの *UserAccountControl* 値に *DONT_EXPIRE_PASSWORD* が適用されます。

ユーザーが Azure AD でパスワードをリセットすると、*forceChangePasswordNextSignIn=True* 属性が適用されます。 マネージド ドメインでは、この属性が Azure AD から同期されます。 マネージド ドメインで、Azure AD から同期されたユーザーに *forceChangePasswordNextSignIn* が設定されていることが検出されると、マネージド ドメインの *pwdLastSet* 属性は *0* に設定され、これによって、現在設定されているパスワードが無効になります。

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD Domain Services は、AD アカウントに、 ロックアウトから保護する機能を提供しますか?
はい。 管理対象ドメインに、2 分以内に無効なパスワードの試行が5回行われると、ユーザーのアカウントは、30 分ロックアウトされます。 30 分後、ユーザー アカウントは、自動的にロック解除されます。 マネージド ドメインでの無効なパスワードの試行によっては、Azure AD のユーザー アカウントはロックアウトされません。 ユーザー アカウントは、Azure AD Domain Services の管理対象ドメイン内でだけ、ロックアウトされます。 詳細については、「[マネージド ドメインに関するパスワードとアカウントのロックアウト ポリシー](password-policy.md)」を参照してください。

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>Azure AD Domain Services 内で分散ファイル システムとレプリケーションを構成できますか。
いいえ。 Azure AD Domain Services を使用する場合、分散ファイル システム (DFS) とレプリケーションは使用できません。

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Windows の更新プログラムは Azure AD Domain Services でどのように適用されますか。
マネージド ドメインのドメイン コントローラーにより、必須の Windows 更新プログラムが自動的に適用されます。 ユーザー側では何も構成したり、管理したりする必要がありません。 Windows 更新プログラムへの送信トラフィックをブロックするネットワーク セキュリティ グループ規則を作成しないようにしてください。 独自の VM がマネージド ドメインに参加している場合、OS やアプリケーションに必須の更新プログラムがある場合、それを構成したり、適用したりするのはユーザー側の責任となります。

## <a name="billing-and-availability"></a>課金と可用性

* [Azure AD Domain Services は有料のサービスですか。](#is-azure-ad-domain-services-a-paid-service)
* [サービスの無料試用版はありますか。](#is-there-a-free-trial-for-the-service)
* [Azure AD Domain Services のマネージド ドメインを一時停止することはできますか。](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [DR イベント時に Azure AD Domain Services を別のリージョンにフェールオーバーできますか。](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Enterprise Mobility Suite (EMS) の一部として Azure AD Domain Services を取得できますか。Azure AD Domain Services を使用するのに Azure AD Premium が必要ですか。](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [このサービスは、どの Azure リージョンで利用できますか。](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Azure AD Domain Services は有料のサービスですか。
はい。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/active-directory-ds/)を参照してください。

### <a name="is-there-a-free-trial-for-the-service"></a>サービスの無料試用版はありますか。
Azure AD Domain Services は Azure の無料試用版に含まれています。 [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインを一時停止することはできますか。
いいえ。 Azure AD Domain Services のマネージド ドメインを有効にすると、マネージド ドメインを削除するまで、選択した仮想ネットワーク内でサービスを使用できます。 サービスを一時停止する方法はありません。 課金は、マネージド ドメインを削除するまで、1 時間ごとに続行されます。

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>DR イベント時に Azure AD Domain Services を別のリージョンにフェールオーバーできますか。
はい。マネージド ドメインの地理的な回復性を実現するために、Azure AD DS をサポートする任意の Azure リージョン内のピアリングされた仮想ネットワークに対して追加の[レプリカ セット](tutorial-create-replica-set.md)を作成できます。 レプリカ セットとマネージド ドメインとで、同じ名前空間および構成が共有されます。

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Enterprise Mobility Suite (EMS) の一部として Azure AD Domain Services を取得できますか。 Azure AD Domain Services を使用するのに Azure AD Premium が必要ですか。
いいえ。 Azure AD Domain Services は従量課金制の Azure サービスであり、EMS の一部ではありません。 Azure AD Domain Services は、Azure AD のすべてのエディション (Free および Premium) で使用できます。 使用量に応じて、時間単位で課金されます。

### <a name="what-azure-regions-is-the-service-available-in"></a>このサービスは、どの Azure のリージョンで利用できますか。
Azure AD Domain Services を使用できる Azure リージョンの一覧については、[リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services/)に関するページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

Azure ADドメイン サービスを 構成するか、または管理する際に生じる、一般的な問題の解決策については、｢[トラブルシューティングガイド](troubleshoot.md)｣を参照してください。

## <a name="next-steps"></a>次のステップ

Azure AD Domain Services の詳細については、「[Azure Active Directory Domain Services とは](overview.md)」を参照してください。

作業を開始するには、「[Azure Active Directory Domain Services のマネージド ドメインを作成して構成する](tutorial-create-instance.md)」を参照してください。
