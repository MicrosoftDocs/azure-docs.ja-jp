---
title: FAQ - Azure Active Directory Domain Services | Microsoft Docs
description: Azure Active Directory Domain Services についてよく寄せられる質問
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: a91c015561ca173412e4d6710d5531a7d084bfd0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193014"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services:よく寄せられる質問 (FAQ)
このページでは、Azure Active Directory Domain Services に関してよく寄せられる質問への回答が記載されています。 常に最新情報をチェックしてください。

## <a name="troubleshooting-guide"></a>トラブルシューティング ガイド
Azure ADドメイン サービスを 構成するか、または管理する際に生じる、一般的な問題の解決策については、｢[トラブルシューティングガイド](active-directory-ds-troubleshooting.md)｣を参照してください。

## <a name="configuration"></a>構成
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>1 つの Azure AD ディレクトリに対して複数のマネージド ドメインを作成することはできますか。
いいえ。 1 つの Azure AD ディレクトリに対して Azure AD Domain Services によって対応されるマネージド ドメインは 1 つだけ作成できます。  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager 仮想ネットワークで Azure AD Domain Services を有効にすることはできますか。
はい。 Azure AD Domain Services は Azure Resource Manager 仮想ネットワークで有効にすることができます。 クラシックの Azure 仮想ネットワークは、マネージド ドメインの新規作成のサポートをまもなく終了します。

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>既存のマネージド ドメインをクラシック仮想ネットワークから Resource Manager 仮想ネットワークに移行することはできますか。
現時点では連携しません。 将来的には、既存のマネージド ドメインをクラシック仮想ネットワークから Resource Manager 仮想ネットワークに移行するメカニズムを提供します。

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Azure AD Domain Services を Azure CSP (Cloud Solution Provider) サブスクリプション内で有効にできますか。
はい。 [Azure CSP サブスクリプションの Azure AD Domain Services ](active-directory-ds-csp.md) を有効にする方法をご覧ください。

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>フェデレーション Azure AD ディレクトリの Azure AD Domain Services を有効にすることはできますか。 Azure AD にパスワード ハッシュを同期していません。 このディレクトリの Azure AD Domain Services を有効にすることはできますか。
いいえ。 Azure AD Domain Services では、NTLM または Kerberos を通じたユーザーの認証で、ユーザー アカウントのパスワード ハッシュへのアクセスが必要になります。 フェデレーション ディレクトリでは、パスワード ハッシュは Azure AD ディレクトリに格納されません。 そのため、Azure AD Domain Services では、このような Azure AD ディレクトリは使用されません。

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Azure AD Domain Services をサブスクリプション内の複数の仮想ネットワークで利用できますか。
このサービスそのものが、そのようなシナリオを直接サポートすることはありません。 マネージド ドメインは一度に 1 つの仮想ネットワークでのみ利用できます。 ただし、Azure AD Domain Services を他の仮想ネットワークに公開するために複数の仮想ネットワーク間の接続を構成できます。 [Azure で仮想ネットワークに接続する](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)方法をご覧ください。

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>PowerShell を使用して Azure AD ドメイン サービスを有効にできますか。
はい。 [PowerShell を使って Azure AD Domain Services を有効にする方法](active-directory-ds-enable-using-powershell.md)に関する記事をご覧ください。

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Azure AD Domain Services を有効にできますか？
いいえ。現在のところ、テンプレートを使用して Azure AD Domain Services を有効にすることはできません。 代わりに PowerShell を使用してください ([PowerShell を使って Azure AD Domain Services を有効にする方法](active-directory-ds-enable-using-powershell.md)に関する記事をご覧ください)。

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインにドメイン コント ローラーを追加することはできますか。
いいえ。 マネージド ドメインは Azure AD Domain Services によって提供されるドメインです。 このドメインに対してドメイン コントローラーをプロビジョニング、構成、または管理する必要はありません。これらの管理作業は Microsoft によるサービスとして提供されます。 そのため、マネージド ドメインに追加のドメイン コント ローラー (読み取り/書き込みも読み取り専用も) を追加することはできません。

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>自分のディレクトリに招待したゲスト ユーザーは Azure AD Domain Services を使用できますか。
いいえ。 [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) 招待プロセスを使用して Azure AD ディレクトリに招待されたゲスト ユーザーは、Azure AD Domain Services の管理対象ドメインに同期されます。 ただし、これらのユーザーのパスワードは Azure AD ディレクトリに保存されません。 そのため、Azure AD Domain Services で、これらのユーザーの NTLM と Kerberos のハッシュをマネージド ドメインに同期できません。 その結果、当該ユーザーは、管理対象ドメインにログインすることや、コンピューターを管理対象ドメインに参加させることができません。

## <a name="administration-and-operations"></a>管理と操作
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>リモート デスクトップを使用してマネージド ドメインのドメイン コントローラーに接続できますか。
いいえ。 マネージド ドメインのドメイン コントローラーにリモート デスクトップで接続する権限はありません。 "AAD DC 管理者" グループのメンバーは、Active Directory 管理センター (ADAC) や AD PowerShell などの AD 管理ツールでマネージド ドメインを管理できます。 これらのツールは、"リモート サーバー管理ツール" 機能を使用して、マネージド ドメインに参加している Windows サーバーにインストールされます。

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Azure AD Domain Services を有効にしています。 このドメインに参加しているドメイン コンピューターでは、どのユーザー アカウントを使用できますか。
管理グループのメンバー ("AAD DC 管理者" など) は、ドメインに参加しているコンピューターで使用できます。 さらに、このグループのメンバーには、ドメインに参加しているコンピューターへのリモート デスクトップ アクセス権が付与されます。

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services によって提供されるマネージド ドメインにドメイン管理者特権はありますか。
いいえ。 マネージド ドメイン上に管理者特権は付与されません。 "ドメイン管理者" 特権と "エンタープライズ管理者" 特権はどちらも、ドメインでは利用できません。 オンプレミスのアクティブディレクトリ内のドメイン管理者またはエンタープライズ管理者グループのメンバーには、管理対象ドメインのドメイン/エンタープライズ管理者特権は付与されません。

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>マネージド ドメインで LDAP または他の AD 管理ツールを使用してグループ メンバーシップを変更できますか。
いいえ。 Azure AD Domain Services によってサービスされるドメインのグループ メンバーシップは変更できません。 ユーザー属性に対しても同様です。 ただし、Azure AD またはオンプレミスのドメインのいずれかで、グループ メンバーシップまたはユーザー属性を変更できます。 このような変更は、Azure AD Domain Services に自動的に同期されます。

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Azure AD ディレクトリに対して行った変更がマネージド ドメインに反映されるまで、どのくらいの時間がかかりますか。
Azure AD UI または PowerShell を使用して Azure AD ディレクトリに対して行った変更は、マネージド ドメインに同期されます。 この同期プロセスはバック グラウンドで実行されます。 1度、初期同期が完了されると、Azure AD で行われた変更が管理対象ドメインに反映されるまで、通常は約 20 分かかります。

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services によって提供されるマネージド ドメインのスキーマは拡張できますか。
いいえ。 スキーマは、Microsoft がマネージド ドメインを管理することで管理されます。 Azure AD ドメイン サービスでは、スキーマの拡張機能はサポートされていません。

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>マネージド ドメインの DNS レコードを変更または追加できますか。
はい。 "AAD DC 管理者" グループのメンバーには、マネージド ドメインの DNS レコードを変更できるよう "DNS 管理者" 特権が付与されます。 これらのメンバーは、マネージド ドメインに参加している Windows Server を実行しているコンピューター上で DNS マネージャー コンソールを使用して、DNS を管理できます。 DNS マネージャー コンソールを使用するには、"リモート サーバー管理ツール" オプション機能の一部である "DNS サーバー ツール" をサーバーにインストールします。 [DNS の管理、監視、およびトラブルシューティングを行うためのユーティリティ](https://technet.microsoft.com/library/cc753579.aspx) の詳細については、TechNet をご覧ください。

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>マネージド ドメインのパスワード有効期間ポリシーとはどのようなものですか。
Azure AD Domain Services のマネージド ドメインの既定のパスワード有効期間は 90 日間です。 このパスワード有効期間は、Azure AD で構成されているパスワード有効期間と同期されません。 そのため、ユーザーのパスワードが、マネージド ドメインでは期限切れだが、Azure AD ではまだ有効なことがあります。 このような場合、ユーザーは Azure AD のパスワードを変更する必要があり、この新しいパスワードがマネージド ドメインに同期されます。 また、ユーザー アカウントの "password-does-not-expire" 属性と "user-must-change-password-at-next-logon" 属性は、マネージド ドメインに同期されません。

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD Domain Services は、AD アカウントに、 ロックアウトから保護する機能を提供しますか?
はい。 管理対象ドメインに、2 分以内に無効なパスワードの試行が5回行われると、ユーザーのアカウントは、30 分ロックアウトされます。 30 分後、ユーザー アカウントは、自動的にロック解除されます。 管理対象ドメインでの無効なパスワードの試行は、 Azure AD のユーザー アカウントをロックしません。 ユーザー アカウントは、Azure AD Domain Services の管理対象ドメイン内でだけ、ロックアウトされます。

## <a name="billing-and-availability"></a>課金と可用性
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Azure AD Domain Services は有料のサービスですか。
はい。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/active-directory-ds/)を参照してください。

### <a name="is-there-a-free-trial-for-the-service"></a>サービスの無料試用版はありますか。
このサービスは、Azure の無料試用版に含まれています。 [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインを一時停止することはできますか。 
いいえ。 Azure AD Domain Services のマネージド ドメインを有効にすると、マネージド ドメインを無効化/削除するまで、選択した仮想ネットワーク内でサービスを使用できます。 サービスを一時停止する方法はありません。 課金は、マネージド ドメインを削除するまで、1 時間ごとに続行されます。

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>DR イベント時に Azure AD Domain Services を別のリージョンにフェールオーバーできますか。
いいえ。  Azure AD Domain Services では現在、Geo 冗長デプロイ モデルを提供していません。 Azure リージョン内の 1 つの仮想ネットワークに限定されています。 複数の Azure リージョンを利用する場合、Azure IaaS VM 上で Active Directory ドメイン コントローラーを実行する必要があります。  アーキテクチャのガイダンスは[こちら](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)を参照してください。

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Enterprise Mobility Suite (EMS) の一部として Azure AD Domain Services を取得できますか。 Azure AD Domain Services を使用するのに Azure AD Premium が必要ですか。
いいえ。 Azure AD Domain Services は従量課金制の Azure サービスであり、EMS には含まれていません。 Azure AD Domain Services は、Azure AD のすべてのエディション (Free、Basic、および Premium) で使用できます 。 使用状況に応じて、時間単位で課金されます。

### <a name="what-azure-regions-is-the-service-available-in"></a>このサービスは、どの Azure のリージョンで利用できますか。
Azure AD Domain Services を使用できる Azure リージョンの一覧については、[リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services/)に関するページを参照してください。
