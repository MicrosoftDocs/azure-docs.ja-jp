---
title: "Azure のセルフサービス サインアップについて  | Microsoft Docs"
description: "Azure のセルフサービス サインアップの概要、サインアップ プロセスの管理方法、および DNS ドメイン名の引き継ぎ方法。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d517f434fe81617061d37a5b60f4e49806948005


---
# <a name="what-is-self-service-signup-for-azure"></a>Azure のセルフサービス サインアップについて
このトピックでは、セルフサービス サインアップ プロセスのほか、DNS ドメイン名を引き継ぐ方法について説明します。  

## <a name="why-use-self-service-signup"></a>セルフサービス サインアップを使用する理由
* 顧客が求めるサービスを迅速に提供できる。
* サービス提供の電子メール ベース プランを構築できる。
* 覚えやすい職場の電子メールの別名を使用した迅速な ID 作成を可能にする、電子メール ベースのサインアップ フローを作成できる。
* 管理されていない Azure ディレクトリを後で管理されているディレクトリにすることができ、他のサービスで再利用できる。

## <a name="terms-and-definitions"></a>用語と定義
* **セルフサービス サインアップ**: ユーザーがクラウド サービスを使用するためにこの方法でサインアップすると、電子メール ドメインに基づいた ID が Azure Active Directory (Azure AD) で自動作成されます。
* **管理されていない Azure ディレクトリ**: これは、前述の ID が作成されるディレクトリです。 管理されていないディレクトリは、グローバル管理者がいないディレクトリです。
* **電子メール検証済みユーザー**: これは Azure AD のユーザー アカウントの 1 種です。 セルフサービス プランへのサインアップ後に自動作成された ID を持つユーザーは、電子メール検証済みユーザーです。 電子メール検証済みユーザーは、creationmethod=EmailVerified でタグ付けされたディレクトリの通常メンバーです。

## <a name="user-experience"></a>ユーザー エクスペリエンス
たとえば、 Dan@BellowsCollege.com という電子メールを持つユーザーが、電子メール経由で機密性の高いファイルを受信したとします。 これらのファイルは、Azure Rights Management (Azure RMS) によって保護されています。 しかしダンが所属するべロウズ大学は、 Azure RMS にサインアップしていないだけでなく、Active Directory RMS もデプロイしていません。 この場合、ダンは個人向け RMS の無料サブスクリプションにサインアップして、保護されたファイルを参照することができます。

ダンが、BellowsCollege.com の電子メール アドレスを使用してこのセルフサービス プランにサインアップした最初のユーザーであるならば、BellowsCollege.com の管理されていないディレクトリが Azure AD に作成されます。 BellowsCollege.com ドメインの他のユーザーがこのプランまたは類似のセルフサービス プランにサインアップすると、Azure にある管理されていない同じディレクトリに、電子メール検証済みアカウントが作成されます。

## <a name="admin-experience"></a>管理者エクスペリエンス
管理されていない Azure ディレクトリの DNS ドメイン名を所有する管理者は、所有権の証明後にディレクトリを引き継ぐか、マージすることができます。 次のセクションでは管理者エクスペリエンスを詳細に説明しますが、概要を以下に示します。

* 管理されていない Azure ディレクトリを引き継ぐと、その管理されていないディレクトリのグローバル管理者になります。 これは、内部の引き継ぎとも呼ばれます。
* 管理されていない Azure ディレクトリをマージすると、その管理されていないディレクトリの DNS ドメイン名が管理対象の Azure ディレクトリに追加され、ユーザーが引き続き中断されずにサービスにアクセスできるように、ユーザーからリソースへのマッピングが作成されます。 これは、外部の引き継ぎとも呼ばれます。

## <a name="what-gets-created-in-azure-active-directory"></a>Azure Active Directory 内に作成されるもの
#### <a name="directory"></a>ディレクトリ
* 1 つのドメインにつき 1 つの Azure Active Directory ディレクトリが作成されます。
* Azure AD ディレクトリにはグローバル管理者はいません。

#### <a name="users"></a>Users
* サインアップするユーザーごとに、Azure AD ディレクトリ内にユーザー オブジェクトが作成されます。
* 各ユーザー オブジェクトは外部としてマークされます。
* 各ユーザーは、サインアップしたサービスにアクセスできます。

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>所有ドメインのセルフサービス Azure AD ディレクトリの要求方法
セルフサービス Azure AD ディレクトリを要求するには、ドメイン検証を実行します。 ドメイン検証では、DNS レコードの作成によってドメインを所有していることを証明できます。

Azure AD ディレクトリの DNS 引き継ぎを行うには 2 つの方法があります。

* 内部の引き継ぎ (管理者が管理されていない Azure のディレクトリを検出し、管理対象ディレクトリに変更する)
* 外部の引き継ぎ (管理者が管理対象 Azure ディレクトリへの新しいドメインの追加を試みる)

ユーザーがセルフサービス サインアップした後に、管理されていないディレクトリを引き継ぐか、あるいは新しいドメインを既存の管理対象ディレクトリに追加するには、ドメインを所有していることを検証する必要があります。 たとえば、contoso.com という名前のドメインがあり、contoso.co.uk または contoso.uk という新しいドメインを追加する場合などにこれを実行します。

## <a name="what-is-domain-takeover"></a>ドメインの引き継ぎについて
このセクションでは、ドメインを所有していることを検証する方法を説明します。

### <a name="what-is-domain-validation-and-why-is-it-used"></a>ドメインの検証とその使用目的について
Azure AD のディレクトリで作業するには、DNS ドメインの所有権を検証する必要があります。  ドメインを検証するとディレクトリを要求でき、セルフサービスのディレクトリを管理対象ディレクトリに昇格するか、既存の管理対象ディレクトリにマージできます。

## <a name="examples-of-domain-validation"></a>ドメイン検証の例
ディレクトリの DNS 引き継ぎを行うには 2 つの方法があります。

* 内部の引き継ぎ (たとえば、管理者がセルフサービスおよび管理されていないディレクトリを検出して、管理対象ディレクトリに変更する)
* 外部の引き継ぎ (たとえば、管理者が管理対象ディレクトリへの新しいドメインの追加を試みる)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>内部の引き継ぎ - セルフサービスの管理されていないディレクトリを管理対象ディレクトリに昇格
内部の引き継ぎを実行すると、管理されていないディレクトリが、管理対象ディレクトリに変換されます。 DNS ドメイン名の検証を完了する必要があります。このとき、DNS ゾーンに MX レコードまたは TXT レコードを作成します。 この操作によって以下を実行できます。

* ドメインを所有していることを検証
* ディレクトリを管理対象にする
* ディレクトリのグローバル管理者になる

たとえば、ベロウズ大学の IT 管理者が、この大学のユーザーたちがセルフサービス プランにサインアップしたことを知ったとします。 その IT 管理者は、BellowsCollege.com という DNS 名の登録所有者として、Azure で DNS 名の所有権を検証してから、管理されていないディレクトリを引き継ぐことができます。 そのディレクトリは管理対象ディレクトリになり、IT 管理者は BellowsCollege.com ディレクトリのグローバル管理者ロールに割り当てられます。

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>外部の引き継ぎ - セルフサービスのディレクトリを既存の管理対象ディレクトリにマージ
外部の引き継ぎでは、既に管理対象ディレクトリを持っているため、2 つの異なるディレクトリを所有する代わりに、すべてのユーザーとグループにその管理対象ディレクトリに参加させます。

管理対象ディレクトリの管理者としてドメインを追加したとき、そのドメインに、管理されていないディレクトリが偶然関連付けられていたと考えてください。

たとえば IT 管理者が、contoso.com の管理対象ディレクトリを既に所有しているとします。contoso.com は IT 管理者の所属組織に登録されています。 その組織の複数ユーザーが、電子メールのドメイン名 user@contoso.co.uk, を使用してセルフサービス サインアップを実行したことをその IT 管理者は知ります。これは、この組織が所有する別のドメイン名です。 これらのユーザーは現在、contoso.co.uk の管理されていないディレクトリのアカウントを持っています。

2 つのディレクトリの管理は避けたいため、 IT 管理者は contoso.co.uk の管理されていないディレクトリを、contoso.com の既存の IT 管理対象ディレクトリにマージします。

外部の引き継ぎでも、内部の引き継ぎと同じ DNS 検証プロセスに従います。  異なる点は、ユーザーおよびサービスが、IT 管理対象ディレクトリに再マッピングされることです。

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>外部の引き継ぎの実行から受ける影響について
外部の引き継ぎを実行すると、ユーザーからリソースへのマッピングが作成されるため、ユーザーは引き続き中断されることなくサービスにアクセスできます。 個人向け RMS を含む多くのアプリケーションでは、ユーザーからリソースへのマッピングがうまく処理されるため、ユーザーはこれらのサービスに以前と同じようにアクセスできます。 ユーザーからリソースへのマッピングが効果的に処理されないアプリケーションの場合は、ユーザー エクスペリエンスの低下を回避するために、外部の引き継ぎが明示的にブロックされている場合があります。

#### <a name="directory-takeover-support-by-service"></a>サービスごとのディレクトリ引き継ぎのサポート
現在、次のサービスで引き継ぎがサポートされています。

* RMS

次のサービスでは、まもなく引き継ぎがサポートされます。

* PowerBI

次のサービスでは、外部の引き継ぎの後に、ユーザー データを移行するための追加の管理操作は必要ありません。

* SharePoint および OneDrive

## <a name="how-to-perform-a-dns-domain-name-takeover"></a>DNS ドメイン名の引き継ぎの実行方法
ドメイン検証 (および必要に応じて引き継ぎ) の実行方法はいくつかあります。

1. Microsoft Azure 管理ポータル

   引き継ぎは、ドメインの追加によってトリガーされます。  ドメインのディレクトリが既に存在する場合は、オプションで外部の引き継ぎを実行できます。

   資格情報を使用して、Azure Portal にサインインします。  既存のディレクトリに移動し、[ **ドメインの追加**] を選択します。
2. Office 365

   Office 365 の [ [ドメインの管理](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) ] ページにあるオプションを使用すると、ドメインと DNS レコードで作業できます。 「 [Office 365 でドメインを確認する](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/)」 参照してください。
3. Windows PowerShell

   Windows PowerShell を使用して検証を実行するには、次の手順に従ってください。

   | 手順 | 使用するコマンドレット |
   | --- | --- |
   | 資格情報オブジェクトの作成 |Get-Credential |
   | Azure への接続 |Connect-MsolService |
   | ドメイン一覧の取得 |Get-MsolDomain |
   | チャレンジの作成 |Get-MsolDomainVerificationDns |
   | DNS レコードの作成 |DNS サーバー上でこれを実行 |
   | チャレンジの確認 |Confirm-MsolEmailVerifiedDomain |

次に例を示します。

1. セルフ サービス プランに対応するために使用された資格情報を使用して Azure AD に接続します。

        import-module MSOnline
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
2. ドメイン一覧を、次のコマンドレットで取得します。

    Get-MsolDomain
3. そして、次のように Get-MsolDomainVerificationDns コマンドレットを実行してチャレンジを作成します。

    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord

    次に例を示します。

    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
4. このコマンドから返される値 (チャレンジ) をコピーします。

    次に例を示します。

    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
5. パブリック DNS 名前空間で、前の手順でコピーした値を含む DNS txt レコードを作成します。

    この レコードの名前は親ドメインの名前です。このリソース レコードを Windows Server の DNS ロールを使用して作成する場合は、[レコード名] を空のままにして、テキスト ボックスに値を貼り付けます。
6. 次のように Confirm-MsolDomain コマンドレットを実行してチャレンジを確認します。

    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*

    次に例を示します。

    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com

チャレンジがクリアされると、エラーなしでプロンプトに戻ります。

## <a name="how-do-i-control-self-service-settings"></a>セルフサービス設定の管理方法
管理者は、セルフサービスの 2 種類の管理を実行できます。 管理できる対象は次のとおりです。

* ユーザーが電子メール経由でディレクトリに参加できるかどうか。
* ユーザー自身がアプリケーションやサービスのライセンスを取得できるかどうか。

### <a name="how-can-i-control-these-capabilities"></a>これらの機能の管理方法
管理者は、Azure AD コマンドレット Set-MsolCompanySettings パラメータを使用してこれらの機能を構成できます。

* **AllowEmailVerifiedUsers** によって、ユーザーが管理されていないディレクトリを作成または参加できるかを管理できます。 このパラメーターを $false に設定すると、電子メール検証済みのユーザーはディレクトリに参加できません。
* **AllowAdHocSubscriptions** によって、ユーザーがセルフサービス サインアップを実行できるかどうかを管理できます。 このパラメータを $false に設定すると、ユーザーはセルフサービス サインアップを実行できません。

### <a name="how-do-the-controls-work-together"></a>これらの管理機能の連携について
これら 2 つのパラメーターを組み合わせて使用すると、セルフサービス サインアップをさらに細かく管理できるようになります。 たとえば、次のコマンドによりユーザーはセルフサービス サインアップを実行できますが、 Azure AD のアカウントを既に持っている場合に限定されます (つまり、電子メール検証済みのアカウントを作成する必要があるユーザーは、セルフサービス サインアップを実行できません)。

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

次のフローチャートでは、これらのパラメーターのさまざまな組み合わせと、結果として得られるディレクトリとセルフサービス サインアップの状態を示しています。

![][1]

これらのパラメーターの使用方法についての詳細は、「 [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)」を参照してください。

## <a name="see-also"></a>関連項目
* [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)
* [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
* [Azure コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/jj554330.aspx)
* [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png



<!--HONumber=Dec16_HO2-->


