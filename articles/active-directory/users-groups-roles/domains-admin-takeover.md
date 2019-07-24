---
title: 管理者による非管理対象ディレクトリの引き継ぎ - Azure Active Directory | Microsoft Docs
description: Azure Active Directory にある非管理対象ディレクトリ (シャドウ テナント) の DNS ドメイン名を引き継ぐ方法です。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f9a33b6bce8cef5bf790efeb43259dfb8013487
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202488"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Azure Active Directory の非管理対象ディレクトリを管理者として引き継ぐ

この記事では、Azure Active Directory (Azure AD) の非管理対象ディレクトリにある DNS ドメイン名を引き継ぐ 2 つの方法について説明します。 セルフサービス ユーザーは、Azure AD を使用しているクラウド サービスにサインアップするときに、電子メールのドメインに基づいて管理されていない Azure AD ディレクトリに追加されます。 サービスに対するセルフサービス ("バイラル") サインアップについては、「[Azure Active Directory のセルフサービス サインアップについて](directory-self-service-signup.md)」をご覧ください

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>非管理対象ディレクトリを引き継ぐ方法を決定する
管理者の引き継ぎのプロセスでは、「[カスタム ドメイン名を Azure Active Directory に追加する](../fundamentals/add-custom-domain.md)」で説明されているように、所有権を証明できます。 次のセクションでは管理者エクスペリエンスを詳細に説明しますが、概要を以下に示します。

* 管理されていない Azure ディレクトリの ["内部" 管理者の引き継ぎ](#internal-admin-takeover)を実行、その非管理対象ディレクトリのグローバル管理者として追加されます。 管理しているその他のディレクトリには、ユーザー、ドメイン、またはサービス プランは移行されません。

* 管理されていない Azure ディレクトリの ["外部" 管理者の引き継ぎ](#external-admin-takeover)を実行すると、その非管理対象ディレクトリの DNS ドメイン名が管理対象の Azure ディレクトリに追加されます。 ドメイン名を追加するとき、ユーザーが引き続き中断されずにサービスにアクセスできるように、リソースに対するユーザーのマッピングが管理対象の Azure ディレクトリに作成されます。 

## <a name="internal-admin-takeover"></a>内部管理者の引き継ぎ

Office 365 など SharePoint と OneDrive が搭載されている一部の製品では、外部引き継ぎをサポートしていません。 このシナリオに該当する場合、または管理されていない ("シャドウ") テナントの作成をセルフサービス サインアップを使用したユーザーから引き継ぎたいと考えている管理者の場合は、内部管理者の引き継ぎを使ってこれを実行できます。

1. Power BI などのサインアップを使って、管理されていないテナントにユーザー コンテキストを作成します。 わかりやすい例にするために、この手順では Power BI を使用した方法を仮定します。

2. [Power BI サイト](https://powerbi.com)を開き、**[無料体験を開始する]** を選びます。 たとえば、`admin@fourthcoffee.xyz` のように、組織のドメイン名を使用しているユーザー アカウントを入力します。 認証コードを入力したら、電子メールで確認コードをチェックします。

3. Power BI からの確認の電子メールで、**[Yes, that's me]\(はい、私です\)** を選びます。

4. Power BI ユーザー アカウントで、[Microsoft 365 管理センター](https://admin.microsoft.com)にサインインします。 管理されていないテナントで既に確認済みのドメイン名の **[管理者になる]**  に誘導するメッセージを受信します。 **[Yes, I want to be the admin]\(はい、管理者になります\)** を選択します。
  
   ![[管理者になる] の最初のスクリーン ショット](./media/domains-admin-takeover/become-admin-first.png)
  
5. TXT レコードを追加して、ドメイン名のレジストラーでドメイン名 **fourthcoffee.xyz** を所有していることを証明します。 この例では、GoDaddy.com になります。
  
   ![ドメイン名の txt レコードを追加する](./media/domains-admin-takeover/become-admin-txt-record.png)

DNS TXT レコードがドメイン名のレジストラーで確認済みの場合、Azure AD テナントを管理できます。

上記の手順を完了すると、Office 365 の Fourth Coffee テナントのグローバル管理者になります。 お使いの他の Azure サービスとドメイン名を統合するには、ドメイン名を Office 365 から削除して、Azure の別の管理対象テナントに追加できます。

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Azure AD の管理対象テナントにドメイン名を追加する

1. [Microsoft 365 管理センター](https://admin.microsoft.com)を開きます。
2. **[ユーザー]** タブを選択し、カスタム ドメイン名を使用していない *user\@fourthcoffeexyz.onmicrosoft.com* のような名前で、新しいユーザー アカウントを作成します。 
3. 新しいユーザー アカウントに Azure AD テナントのグローバル管理者特権が付与されていることを確認します。
4. Microsoft 365 管理センターで **[ドメイン]** タブを開き、ドメイン名を選択して **[削除]** を選択します。 
  
   ![Office 365 からドメイン名を削除する](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. 削除されたドメイン名を参照しているユーザーまたはグループが Office 365 にある場合、.onmicrosoft.com ドメインに名前を変更する必要があります。 ドメイン名を強制的に削除した場合、すべてのユーザーの名前が自動的に変更されます。この例では、*user\@fourthcoffeexyz.onmicrosoft.com* になります。
  
6. Azure AD テナントのグローバル管理者であるアカウントを使って、[Azure AD 管理センター](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)にサインインします。
  
7. **[カスタム ドメイン名]** を選択してドメイン名を追加します。 DNS TXT レコードを入力して、ドメイン名の所有権を確認する必要があります。 
  
   ![Azure AD に追加済みと確認されたドメイン](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Office 365 テナントに割り当てられているライセンスを所有する、Power BI または Azure Rights Management サービスのユーザーは、ドメイン名が削除された場合、ダッシュ ボードを保存しておく必要があります。 *user\@fourthcoffee.xyz* ではなく、*user\@fourthcoffeexyz.onmicrosoft.com* のようなユーザー名でサインインする必要があります。

## <a name="external-admin-takeover"></a>外部管理者の引き継ぎ

Azure サービスまたは Office 365 を使って既にテナントを管理している場合、別の Azure AD テナントで既に確認されているカスタム ドメイン名を追加することはできません。 ただし、Azure AD で管理されているテナントから、外部管理者の引き継ぎとして管理されていないテナントを引き継ぐことは可能です。 一般的な手順については、[カスタム ドメインの Azure AD への追加](../fundamentals/add-custom-domain.md)に関する記事に従ってください。

ドメイン名の所有権を確認するときは、Azure AD では、管理されていないテナントからドメイン名を削除し、既存のテナントに移動します。 非管理対象ディレクトリの外部管理者の引き継ぎには、内部管理者の引き継ぎと同じ DNS TXT の確認プロセスを行う必要があります。 相違点は、ドメイン名と共に次の内容も移行されることです。

- ユーザー
- サブスクリプション
- ライセンスの割り当て

### <a name="support-for-external-admin-takeover"></a>外部管理者の引き継ぎのサポート
外部管理者の引き継ぎは、次のオンライン サービスによってサポートされています。

- Power BI
- Azure Rights Management
- Exchange Online

サポートされているサービス プランは次のとおりです。

- Power BI Free
- Power BI Pro
- PowerApps Free
- PowerFlow Free
- 個人向け RMS
- Microsoft Stream
- Dynamics 365 無料試用版

外部管理者の引き継ぎは、たとえば、Office の無償のサブスクリプションや Office Basic SKU 経由など、SharePoint、OneDrive、または Skype For Business を含むサービス プランを保持しているサービスではサポートされません。 アンマネージド テナントからドメイン名を削除し、目的のテナントでその有効性を確認する [**ForceTakeover** オプション](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option)を任意で使用できます。 この ForceTakeover オプションでユーザーが移動することはありません。サブスクリプションへのアクセスが保持されます。 そうではなく、このオプションではドメイン名のみが移動します。 

#### <a name="more-information-about-rms-for-individuals"></a>個人向け RMS の詳細

[個人向け RMS](/azure/information-protection/rms-for-individuals) の場合、所有しているテナントと同じリージョンにアンマネージド テナントがあるとき、自動的に作成された [Azure Information Protection テナント キー](/azure/information-protection/plan-implement-tenant-key)と[既定の保護テンプレート](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates)もドメイン名と共に移動します。 

アンマネージド テナントが異なるリージョンにあるときは、キーとテンプレートは移動しません。 たとえば、アンマネージド テナントがヨーロッパにあり、所有しているテナントが北米にあるとします。 

個人向け RMS は保護コンテンツを開くために Azure AD 対応として設計されていますが、ユーザーがコンテンツ保護もすることを防ぎません。 ユーザーが個人向け RMS サブスクリプションでコンテンツを保護したとき、キーとテンプレートが移動していなければ、ドメイン引き継ぎ後、そのコンテンツにアクセスできなくなります。

#### <a name="more-information-about-power-bi"></a>Power BI の詳細情報

外部引き継ぎを実行すると、引き継ぎの前に作成された Power BI コンテンツは、[Power BI アーカイブ済みワークスペース](/power-bi/service-admin-power-bi-archived-workspace)に配置されます。 新しいテナントで使用するすべてのコンテンツを手動で移行する必要があります。

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>ForceTakeover オプションの Azure AD PowerShell コマンドレット
「[PowerShell の例](#powershell-example)」で使用されているコマンドレットを以下に示します。


コマンドレット | 使用法 
------- | -------
`connect-msolservice` | 指示に従い、管理対象テナントにサインインします。
`get-msoldomain` | 現在のテナントに関連付けられているドメイン名を表示します。
`new-msoldomain –name <domainname>` | "未確認" (DNS の確認がまだ実行されていない) としてテナントにドメイン名を追加します。
`get-msoldomain` | ドメイン名は、管理対象テナントに関連付けられているドメイン名の一覧に含まれるようになりましたが、"**未確認**" として表示されます。
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | ドメインの新しい DNS TXT レコード (MS=xxxxx) に含める情報を提示します。 TXT レコードのプロパゲートに一定の時間がかかるため、確認が直ちに行われるわけではありません。数分間待機してから、**-ForceTakeover** オプションを検討します。 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>お使いのドメイン名がまだ確認済みではない場合、**-ForceTakeover** オプションを使って続行できます。 TXT レコードが作成され、引き継ぎプロセスが開始されたことを確認します。<li>**- ForceTakeover** オプションは、引き継ぎをブロックしている Office 365 サービスが管理されていないテナントに含まれている場合など、外部管理者の引き継ぎを強制的に行う場合のみ、コマンドレットに追加する必要があります。
`get-msoldomain` | ドメインの一覧に、ドメイン名が "**確認済み**" と表示されるようになりました。

### <a name="powershell-example"></a>PowerShell の例

1. セルフ サービス プランに対応するために使用された資格情報を使用して Azure AD に接続します。
   ```powershell
    Install-Module -Name MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
   ```
2. ドメイン一覧を、次のコマンドレットで取得します。
  
   ```powershell
    Get-MsolDomain
   ```
3. 次のように Get-MsolDomainVerificationDns コマンドレットを実行してチャレンジを作成します。
   ```powershell
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. このコマンドから返される値 (チャレンジ) をコピーします。 例: 
   ```powershell
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. パブリック DNS 名前空間で、前の手順でコピーした値を含む DNS txt レコードを作成します。 このレコードの名前は親ドメインの名前です。このリソース レコードを Windows Server の DNS ロールを使用して作成する場合は、[レコード名] を空のままにして、テキスト ボックスに値を貼り付けます。
6. 次のように Confirm-MsolDomain コマンドレットを実行してチャレンジを確認します。
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
   ```
  
   例: 
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
   ```

チャレンジがクリアされると、エラーなしでプロンプトに戻ります。

## <a name="next-steps"></a>次の手順

* [Azure AD にカスタム ドメイン名を追加する](../fundamentals/add-custom-domain.md)
* [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure コマンドレット リファレンス](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
