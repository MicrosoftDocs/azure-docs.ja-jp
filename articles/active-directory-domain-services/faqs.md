---
title: Azure AD Domain Services に関してよく寄せられる質問 | Microsoft Docs
description: Azure Active Directory Domain Services の構成、管理、および可用性に関してよく寄せられる質問をいくつか読んで理解する
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: iainfou
ms.openlocfilehash: 64e1095466f9494ccd8aa052a739729542920784
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305031"
---
# <a name="frequently-asked-questions-faqs"></a>よく寄せられる質問 (FAQ)

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
* [自分のディレクトリに招待したゲスト ユーザーは Azure AD Domain Services を使用できますか。](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [既存の Azure AD Domain Services マネージド ドメインを別のサブスクリプション、リソース グループ、リージョン、または仮想ネットワークに移動できますか。](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>1 つの Azure AD ディレクトリに対して複数のマネージド ドメインを作成することはできますか。
いいえ。 1 つの Azure AD ディレクトリに対して Azure AD Domain Services によって対応されるマネージド ドメインは 1 つだけ作成できます。

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>クラシック仮想ネットワークで Azure AD Domain Services を有効にすることができますか。
新しいデプロイでは、クラシック仮想ネットワークはサポートされません。 クラシック仮想ネットワークにデプロイされている既存のマネージド ドメインは、引き続きサポートされます。

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager 仮想ネットワークで Azure AD Domain Services を有効にすることはできますか。
はい。 Azure AD Domain Services は Azure Resource Manager 仮想ネットワークで有効にすることができます。 新しいマネージド ドメインを作成する場合、クラシック Azure 仮想ネットワークはサポートされなくなりました。

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>既存のマネージド ドメインをクラシック仮想ネットワークから Resource Manager 仮想ネットワークに移行することはできますか。
現時点ではありません。 既存のマネージド ドメインをクラシック仮想ネットワークから Resource Manager 仮想ネットワークに移行するためのエンジニアリング作業が進行中です。

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Azure AD Domain Services を Azure CSP (Cloud Solution Provider) サブスクリプション内で有効にできますか。
はい。 詳細については、「[Azure Cloud Solution Provider (CSP) 用 Azure Active Directory (AD) Domain Services](csp.md)」を参照してください。

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>フェデレーション Azure AD ディレクトリの Azure AD Domain Services を有効にすることはできますか。 Azure AD にパスワード ハッシュを同期していません。 このディレクトリの Azure AD Domain Services を有効にすることはできますか。
いいえ。 NTLM または Kerberos を使用してユーザーを認証するには、Azure AD Domain Services でユーザー アカウントのパスワード ハッシュへのアクセスが必要です。 フェデレーション ディレクトリでは、パスワード ハッシュは Azure AD ディレクトリに格納されません。 そのため、Azure AD Domain Services は、このような Azure AD ディレクトリでは機能しません。

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Azure AD Domain Services をサブスクリプション内の複数の仮想ネットワークで利用できますか。
サービス自体は、このシナリオを直接サポートしていません。 マネージド ドメインは一度に 1 つの仮想ネットワークでのみ利用できます。 ただし、複数の仮想ネットワーク間で接続を構成して、Azure AD Domain Services を他の仮想ネットワークに公開することができます。 詳細については、[VPN ゲートウェイを使用して Azure の仮想ネットワークを接続する方法](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)または[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)に関する記事を参照してください。

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>PowerShell を使用して Azure AD ドメイン サービスを有効にできますか。
はい。 詳細については、「[PowerShell を使用した Azure Active Directory Domain Services の有効化](powershell-create-instance.md)」を参照してください。

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Azure AD Domain Services を有効にできますか？
いいえ。現在、テンプレートを使用して Azure AD Domain Services を有効にすることはできません。 スクリプトを使った方法については、「[PowerShell を使用した Azure Active Directory Domain Services の有効化](powershell-create-instance.md)」を参照してください。

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインにドメイン コント ローラーを追加することはできますか。
いいえ。 マネージド ドメインは Azure AD Domain Services によって提供されるドメインです。 このドメインに対してドメイン コントローラーをプロビジョニング、構成、管理する必要はありません。 これらの管理アクティビティは、Microsoft からサービスとして提供されています。 そのため、マネージド ドメインにドメイン コントローラー (読み取り/書き込みや読み取り専用) をさらに追加することはできません。

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>自分のディレクトリに招待したゲスト ユーザーは Azure AD Domain Services を使用できますか。
いいえ。 [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) 招待プロセスを使用して Azure AD ディレクトリに招待されたゲスト ユーザーは、Azure AD Domain Services の管理対象ドメインに同期されます。 ただし、これらのユーザーのパスワードは Azure AD ディレクトリに格納されません。 そのため、Azure AD Domain Services では、これらのユーザーの NTLM と Kerberos のハッシュをマネージド ドメインに同期することはできません。 このようなユーザーは、サインインすることも、マネージド ドメインにコンピューターを参加させることもできません。

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>既存の Azure AD Domain Services マネージド ドメインを別のサブスクリプション、リソース グループ、リージョン、または仮想ネットワークに移動できますか。
いいえ。 Azure AD Domain Services マネージド ドメインを作成した後は、そのインスタンスを別のリソース グループ、仮想ネットワーク、サブスクリプションなどに移動することはできません。Azure AD DS インスタンスをデプロイするときに、最適なサブスクリプション、リソース グループ、リージョン、および仮想ネットワークを慎重に選択してください。

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

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>リモート デスクトップを使用してマネージド ドメインのドメイン コントローラーに接続できますか。
いいえ。 リモート デスクトップを使用してマネージド ドメインのドメイン コントローラーに接続する権限はありません。 "*AAD DC 管理者*" グループのメンバーは、Active Directory 管理センター (ADAC) や AD PowerShell などの AD 管理ツールを使用して、マネージド ドメインを管理できます。 これらのツールは、"*リモート サーバー管理ツール*" 機能を使用して、マネージド ドメインに参加している Windows サーバーにインストールされます。 詳細については、「[チュートリアル:Azure Active Directory Domain Services のマネージド ドメインを構成および管理するための管理 VM を作成する](tutorial-create-management-vm.md)」を参照してください。

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Azure AD Domain Services を有効にしています。 このドメインに参加しているドメイン コンピューターでは、どのユーザー アカウントを使用できますか。
管理グループの "*AAD DC 管理者*" のメンバーが、コンピューターをドメインに参加させることができます。 さらに、このグループのメンバーには、ドメインに参加しているコンピューターへのリモート デスクトップ アクセス権が付与されます。

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services によって提供されるマネージド ドメインにドメイン管理者特権はありますか。
いいえ。 マネージド ドメインの管理特権は付与されません。 "*ドメイン管理者*" 特権と "*エンタープライズ管理者*" 特権は、ドメイン内では使用できません。 また、オンプレミスの Active Directory 内のドメイン管理者グループまたはエンタープライズ管理者グループのメンバーにも、マネージド ドメインのドメイン/エンタープライズ管理者特権は付与されません。

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>マネージド ドメインで LDAP または他の AD 管理ツールを使用してグループ メンバーシップを変更できますか。
いいえ。 Azure AD Domain Services によって提供されるドメインでは、グループ メンバーシップを変更できません。 ユーザー属性に対しても同様です。 グループ メンバーシップまたはユーザー属性は、Azure AD またはオンプレミス ドメインで変更できます。 変更は、Azure AD Domain Services に自動的に同期されます。

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Azure AD ディレクトリに対して行った変更がマネージド ドメインに反映されるまで、どのくらいの時間がかかりますか。
Azure AD UI または PowerShell を使用して Azure AD ディレクトリに対して行った変更は、マネージド ドメインに自動的に同期されます。 この同期プロセスはバック グラウンドで実行されます。 この同期ですべてのオブジェクトの変更を完了するための期間は定義されていません。

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services によって提供されるマネージド ドメインのスキーマは拡張できますか。
いいえ。 スキーマは、Microsoft がマネージド ドメインを管理することで管理されます。 Azure AD Domain Services では、スキーマの拡張機能はサポートされていません。

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>マネージド ドメインの DNS レコードを変更または追加できますか。
はい。 "*AAD DC 管理者*" グループのメンバーには、マネージド ドメインの DNS レコードを変更できる "*DNS 管理者*" 特権が付与されています。 これらのユーザーは、マネージド ドメインに参加している Windows Server を実行しているコンピューター上で DNS マネージャー コンソールを使用して、DNS を管理できます。 DNS マネージャー コンソールを使用するには、"*リモート サーバー管理ツール*" オプション機能の一部である "*DNS サーバー ツール*" をサーバーにインストールします。 詳細については、「[Azure AD Domain Services マネージド ドメインで DNS を管理する](manage-dns.md)」を参照してください。

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>マネージド ドメインのパスワード有効期間ポリシーとはどのようなものですか。
Azure AD Domain Services のマネージド ドメインの既定のパスワード有効期間は 90 日間です。 このパスワード有効期間は、Azure AD で構成されているパスワード有効期間と同期されません。 そのため、ユーザーのパスワードが、マネージド ドメインでは期限切れだが、Azure AD ではまだ有効なことがあります。 このような場合、ユーザーは Azure AD のパスワードを変更する必要があり、この新しいパスワードがマネージド ドメインに同期されます。 また、ユーザー アカウントの "*password-does-not-expire*" 属性と "*user-must-change-password-at-next-logon*" 属性は、マネージド ドメインに同期されません。

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD Domain Services は、AD アカウントに、 ロックアウトから保護する機能を提供しますか?
はい。 管理対象ドメインに、2 分以内に無効なパスワードの試行が5回行われると、ユーザーのアカウントは、30 分ロックアウトされます。 30 分後、ユーザー アカウントは、自動的にロック解除されます。 マネージド ドメインでの無効なパスワードの試行によっては、Azure AD のユーザー アカウントはロックアウトされません。 ユーザー アカウントは、Azure AD Domain Services の管理対象ドメイン内でだけ、ロックアウトされます。 詳細については、「[マネージド ドメインに関するパスワードとアカウントのロックアウト ポリシー](password-policy.md)」を参照してください。

## <a name="billing-and-availability"></a>課金と可用性

* [Azure AD Domain Services は有料のサービスですか。](#is-azure-ad-domain-services-a-paid-service)
* [サービスの無料試用版はありますか。](#is-there-a-free-trial-for-the-service)
* [Azure AD Domain Services のマネージド ドメインを一時停止することはできますか。](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [DR イベント時に Azure AD Domain Services を別のリージョンにフェールオーバーできますか。](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Enterprise Mobility Suite (EMS) の一部として Azure AD Domain Services を取得できますか。Azure AD Domain Services を使用するのに Azure AD Premium が必要ですか。](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [このサービスは、どの Azure リージョンで利用できますか。](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Azure AD Domain Services は有料のサービスですか。
はい。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/active-directory-ds/)を参照してください。

### <a name="is-there-a-free-trial-for-the-service"></a>サービスの無料試用版はありますか。
Azure AD Domain Services は Azure の無料試用版に含まれています。 [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインを一時停止することはできますか。
いいえ。 Azure AD Domain Services のマネージド ドメインを有効にすると、マネージド ドメインを削除するまで、選択した仮想ネットワーク内でサービスを使用できます。 サービスを一時停止する方法はありません。 課金は、マネージド ドメインを削除するまで、1 時間ごとに続行されます。

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>DR イベント時に Azure AD Domain Services を別のリージョンにフェールオーバーできますか。
いいえ。 Azure AD Domain Services では現在、geo 冗長デプロイ モデルは提供されていません。 Azure リージョン内の 1 つの仮想ネットワークに限定されます。 複数の Azure リージョンを利用する場合、Azure IaaS VM 上で Active Directory ドメイン コントローラーを実行する必要があります。 アーキテクチャのガイダンスは[こちら](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)を参照してください。

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Enterprise Mobility Suite (EMS) の一部として Azure AD Domain Services を取得できますか。 Azure AD Domain Services を使用するのに Azure AD Premium が必要ですか。
いいえ。 Azure AD Domain Services は従量課金制の Azure サービスであり、EMS の一部ではありません。 Azure AD Domain Services は、Azure AD のすべてのエディション (Free および Premium) で使用できます。 使用量に応じて、時間単位で課金されます。

### <a name="what-azure-regions-is-the-service-available-in"></a>このサービスは、どの Azure のリージョンで利用できますか。
Azure AD Domain Services を使用できる Azure リージョンの一覧については、[リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services/)に関するページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

Azure ADドメイン サービスを 構成するか、または管理する際に生じる、一般的な問題の解決策については、｢[トラブルシューティングガイド](troubleshoot.md)｣を参照してください。

## <a name="next-steps"></a>次の手順

Azure AD Domain Services の詳細については、「[Azure Active Directory Domain Services とは](overview.md)」を参照してください。

作業を開始するには、「[チュートリアル:Azure Active Directory Domain Services インスタンスを作成して構成する](tutorial-create-instance.md)」を参照してください。
