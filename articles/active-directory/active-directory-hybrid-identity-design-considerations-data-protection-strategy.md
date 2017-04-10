---
title: "Azure Active Directory ハイブリッド ID の設計上の考慮事項 - データ保護戦略の定義 | Microsoft Docs"
description: "定義したビジネス要件を満たすために、ハイブリッド ID ソリューションのデータ保護戦略を定義します。"
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 82bc44b20158a22dfae0d6c8fbf5f1c1f4577c91
ms.lasthandoff: 03/18/2017


---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>ハイブリッド ID ソリューションのデータ保護戦略の定義
このタスクでは、次のトピックで定義したビジネス要件を満たすために、ハイブリッド ID ソリューションのデータ保護戦略を定義します。

* [データ保護要件の決定](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [コンテンツ管理要件の決定](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [アクセス制御要件の決定](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [インシデント対応要件の決定](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>データ保護オプションの定義
[ディレクトリ同期要件の決定](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)に関するページで説明したとおり、Microsoft Azure AD は、オンプレミスにある Active Directory ドメイン サービス (AD DS) と同期できます。 この統合により、組織は Azure AD を利用して、ユーザーが企業のリソースにアクセスしようとしたときに資格情報を確認できます。 これは、オンプレミスの保存データとクラウドの保存データの両方のシナリオで実行できます。  Azure AD 内のデータへのアクセスには、Security Token Service (STS) によるユーザー認証が要求されます。

認証されると、認証トークンからユーザー プリンシパル名 (UPN) が読み取られ、ユーザーのドメインに対応するレプリケート対象のパーティションとコンテナーが判断されます。 承認システムでは、ユーザーの存在、有効状態、ロールに関する情報を使用して、ターゲット テナントへのアクセス要求を当該セッションの当該ユーザーに対して承認するかどうかを決定します。 承認された特定のアクション (具体的には、ユーザーの作成やパスワードのリセットなど) を実行すると、テナント管理者がコンプライアンスへの取り組みや調査の管理に使用できる監査証跡が作成されます。

データの量や帯域幅の可用性などの考慮事項によって、インターネット接続を使用してオンプレミスのデータ センターから Azure Storage にデータを移動することが必ずしも適していない場合があります。 [Azure Storage Import/Export Service](../storage/storage-import-export-service.md) は、BLOB ストレージ内の大容量データを配置/取得するためのハードウェア ベースのオプションを提供します。 このオプションでは、 [BitLocker で暗号化された](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) ハード ディスク ドライブを直接 Azure のデータセンターに発送していただき、クラウド オペレーターがストレージ アカウントにコンテンツをアップロードしたり、Azure のデータをドライブにダウンロードしたりした後に返送します。 このプロセスで受け付けできるのは、(ジョブのセットアップ中にサービス自体によって生成された BitLocker キーを使用して) 暗号化されたディスクのみです。 BitLocker キーは別途 Azure に提供されるため、帯域外でキーが共有されます。

転送中のデータは、さまざまなシナリオで発生する可能性があるため、Microsoft Azure では[仮想ネットワーク](https://azure.microsoft.com/documentation/services/virtual-network/)を使用して、ホストレベルとゲストレベルのファイアウォール、IP パケットのフィルタリング、ポートのブロック、HTTPS エンドポイントなどの手段を採用しながら、テナントのトラフィックを相互に分離していることを知っておくと役立ちます。 ただし、インフラストラクチャ間やインフラストラクチャとお客様 (オンプレミス) の間など、Azure の内部通信の大部分も暗号化されます。 もう&1; つの重要なシナリオが Azure データセンター内での通信です。マイクロソフトでは、VM が別の VM の IP アドレスで偽装や盗聴を一切できないようにするためにネットワークを管理しています。 Azure Storage または SQL Databases にアクセスする場合、または Cloud Services に接続する場合は、TLS/SSL が使用されます。 この場合、TLS/SSL 証明書の取得とテナント インフラストラクチャへのデプロイは、お客様の管理者が行います。 同じデプロイ内の Virtual Machines 間を移動するデータ トラフィック、または Microsoft Azure Virtual Network 経由で単一デプロイのテナント間を移動するデータ トラフィックは、HTTPS や SSL/TLS などの暗号化された通信プロトコルを介して保護できます。

「 [データ保護要件の決定](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)」での質問への回答に基づいて、データをどのように保護するか、ハイブリッド ID ソリューションがデータ保護にどう役立つかを判断できます。 次の表に、各データ保護シナリオで利用可能な、Azure でサポートされているオプションを示します。

| データ保護オプション | クラウド内の保存データ | オンプレミスの保存データ | 転送中のデータ |
| --- | --- | --- | --- |
| BitLocker ドライブ暗号化 |○ |○ | |
| SQL Server によるデータベースの暗号化 |○ |○ | |
| VM 間での暗号化 | | |○ |
| SSL/TLS | | |○ |
| VPN | | |○ |

> [!NOTE]
> 各 Azure サービスが準拠している認定の詳細については、[Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/)の[製品ごとのコンプライアンス](https://azure.microsoft.com/support/trust-center/services/)をお読みください。
> データ保護のオプションでは複数層のアプローチを使用しているため、オプションを比較することはこのタスクには適しません。 必ず、データの状態ごとに使用可能なすべてのオプションを使用してください。
>
>

## <a name="define-content-management-options"></a>コンテンツ管理オプションの定義
Azure AD を使用してハイブリッド ID インフラストラクチャを管理する利点の&1; つは、プロセスがエンド ユーザーから見て完全に透過的な点です。 ユーザーが共有リソースにアクセスしようとする場合、リソースの認証が必要となり、ユーザーは、トークンを取得してリソースにアクセスするために、Azure AD に認証要求を送信する必要があります。 このプロセス全体はバックグラウンドで発生し、ユーザーが介入することはありません。 ユーザーの [グループ](active-directory-manage-groups.md#getting-started-with-access-management) に権限を付与し、特定の共通アクションを実行できるようにすることもできます。

データのプライバシーを懸念する組織では、一般的に、使用するソリューションにデータ分類が不可欠です。 現在のオンプレミス インフラストラクチャで既にデータ分類を使用している場合は、ユーザー ID の主要リポジトリとして Azure AD を利用できます。 オンプレミスでのデータ分類に使用されている一般的なツールは [Data Classification Toolkit](https://msdn.microsoft.com/library/Hh204743.aspx) for Windows Server 2012 R2 です。 このツールは、プライベート クラウド内のファイル サーバーにあるデータを識別、分類、保護するのに役立ちます。 Windows Server 2012 の [自動ファイル分類](https://technet.microsoft.com/library/hh831672.aspx) を利用してこれを実現することもできます。

組織がデータ分類を配置していないが、オンプレミスで新しいサーバーを追加せずに機密ファイルを保護する必要がある場合、Microsoft [Azure Rights Management Service](https://technet.microsoft.com/library/JJ585026.aspx)を使用できます。  Azure RMS では、暗号化、ID、承認ポリシーを使用してファイルと電子メールを保護し、携帯電話、タブレット、PC などの複数のデバイスで動作します。 Azure RMS はクラウド サービスであるため、保護されたコンテンツを共有する前に、他の組織との信頼関係を明示的に構成する必要はありません。 既に Office 365 や Azure AD ディレクトリが組織にある場合、組織間のコラボレーションが自動的にサポートされます。 また、Azure Active Directory 同期サービス (AAD Sync) または Azure AD Connect を使用することにより、Azure RMS でオンプレミスの Active Directory アカウントの共通 ID をサポートするために必要なディレクトリ属性のみを同期することもできます。

コンテンツ管理で重要な部分は、どのユーザーがどのリソースにアクセスするかを理解することです。したがって、ID 管理ソリューションの場合、機能豊富なログ記録機能が重要です。 Azure AD では、30 日間にわたり次を記録するログを提供します。

* ロール メンバーシップの変更 (例: グローバル管理者ロールに追加されたユーザー)
* 資格情報の更新 (例: パスワードの変更)
* ドメインの管理 (例: カスタム ドメインの検証、ドメインの削除)
* アプリケーションの追加または削除
* ユーザーの管理 (例: ユーザーの追加、削除、更新)
* ライセンスの追加または削除

> [!NOTE]
> Azure のログ記録機能の詳細については、 [Microsoft Azure のセキュリティおよび監査ログの管理](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) に関するドキュメントをお読みください。
> 「 [コンテンツ管理要件の決定](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)」での質問への回答に基づいて、ハイブリッド ID ソリューションでのコンテンツの管理方法を判断できます。 表 6 で公開されているオプションは、すべて Azure AD に統合できますが、ビジネス ニーズに適したオプションを定義することが重要です。
>
>

| コンテンツ管理オプション | 長所 | 短所 |
| --- | --- | --- |
| オンプレミスでの一元化 (Active Directory Rights Management Server) |データ分類を行うサーバー インフラストラクチャを完全に制御 <br> Windows Server の組み込み機能。追加のライセンスまたはサブスクリプションは不要 <br> ハイブリッド シナリオで Azure AD への統合が可能 <br> Exchange Online、SharePoint Online、Office 365 などの Microsoft Online Services の Information Rights Management (IRM) 機能をサポート <br> Exchange Server、SharePoint Server などのオンプレミスの Microsoft サーバー製品、および Windows Server とファイル分類インフラストラクチャ (FCI) を実行するファイル サーバーをサポート。 |IT 部門がサーバーを所有しているため、より高度なメンテナンスが必要 (更新プログラム、構成、潜在的なアップグレードなどを最新の状態に保つ非必要あり) <br> オンプレミスのサーバー インフラストラクチャが必要<br> Azure の機能をネイティブで利用していない |
| クラウド (Azure RMS) で一元化 |オンプレミス ソリューションに比べて管理が容易 <br> ハイブリッド シナリオで AD DS への統合が可能 <br>  Azure AD への完全統合 <br> サービスをデプロイするためのオンプレミス サーバーが不要 <br> Exchange Server、SharePoint Server などのオンプレミスの Microsoft サーバー製品、および Windows Server とファイル分類インフラストラクチャ (FCI) を実行するファイル サーバーをサポート <br> BYOK 機能を使用してテナントのキーを IT 部門で完全に制御可能 |RMS をサポートするクラウド サブスクリプションが組織に必要 <br> RMS のユーザー認証をサポートするための Azure AD ディレクトリが組織に必要 |
| ハイブリッド (Azure RMS とオンプレミスの Active Directory Rights Management Server の統合) |このシナリオには、オンプレミスでの一元化とクラウドでの一元化の両方の利点があります。 |RMS をサポートするクラウド サブスクリプションが組織に必要 <br> RMS のユーザー認証をサポートするための Azure AD ディレクトリが組織に必要 <br> Azure のクラウド サービスとオンプレミスのインフラストラクチャの接続が必要 |

## <a name="define-access-control-options"></a>アクセス制御オプションの定義
Azure AD で使用できる認証、承認、アクセス制御の機能を活用することにより、次の図に示すように、自社で中央の ID リポジトリを使用しながら、ユーザーやパートナーにシングル サインオン (SSO) の使用を許可することができます。

![](./media/hybrid-id-design-considerations/centralized-management.png)

一元管理および他のディレクトリとの完全統合

Azure Active Directory では、何千もの SaaS アプリケーションやオンプレミスの Web アプリケーションにシングル サインオンを提供しています。 マイクロソフトによってテストされた SSO サードパーティの詳細については、 [Azure Active Directory のフェデレーション互換性リスト: シングル サインオンの実装に使用できるサード パーティ ID プロバイダー](https://msdn.microsoft.com/library/azure/jj679342.aspx) に関する記事をお読みください。 この機能により、組織は ID とアクセス管理の制御を維持しながら、さまざまな B2B のシナリオを実装できます。 ただし、B2B の設計プロセスで重要なのは、パートナーが使用する認証方法を理解し、その方法が Azure でサポートされているかどうかを検証することです。 現在 Azure AD でサポートされているメソッドは次のとおりです。

* Security Assertion Markup Language (SAML)
* OAuth
* Kerberos
* トークン
* 証明書

> [!NOTE]
> Azure で使用できる各プロトコルとその機能の詳細については、「 [Azure Active Directory の認証プロトコル](https://msdn.microsoft.com/library/azure/dn151124.aspx) 」をお読みください。
>
>

Azure AD のサポートを使用すると、モバイル ビジネス アプリケーションでも簡単な Mobile Services の認証エクスペリエンスを使用できるため、従業員は会社の Active Directory 資格情報を使用してモバイル アプリケーションにサインインできます。 この機能により、既にサポートしている ID プロバイダー (Microsoft アカウント、Facebook ID、Google ID、Twitter ID など) と共に、Azure AD が Mobile Services の ID プロバイダーとしてサポートされます。 オンプレミスのアプリケーションで、会社の AD DS にあるユーザーの資格情報が使用されている場合、クラウドのユーザーやパートナーからのアクセスを透過的にする必要があります。 (クラウドベースの) Web アプリケーション、Web API、Microsoft Cloud Services、サードパーティの SaaS アプリケーション、およびネイティブの (モバイル) クライアント アプリケーションに対するユーザーの条件付きアクセス制御を管理できます。また、セキュリティ、監査、レポートを 1 か所で実行できる利点があります。 ただし、非運用環境で、またはユーザー数を限定してこれを検証することをお勧めします。

> [!TIP]
> AD DS にあるようなグループ ポリシーが Azure AD にはないことを確認しておくことが必要です。 デバイスのポリシーを適用するには、 [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx)などのモバイル デバイス管理ソリューションが必要です。
>
>

Azure AD を使用してユーザーが認証されたら、そのユーザーが持つアクセスのレベルを評価する必要があります。 ユーザーがリソースに対して持つアクセスのレベルは異なりますが、Azure AD では一部のリソースへのアクセスを制御することにより、セキュリティ層を追加することができるため、ファイル サーバーにあるファイルのアクセス制御など、リソース自体にそれぞれ独自のアクセス制御リストを持たせることができる点にも注意する必要があります。 次の図は、ハイブリッド シナリオで使用できるアクセス制御のレベルをまとめたものです。

![](./media/hybrid-id-design-considerations/accesscontrol.png)

図 X に示した個々のやり取りは、Azure AD で対応できる&1; つのアクセス制御シナリオを表しています。 各シナリオの説明を以下に示します。

1. オンプレミスでホストされるアプリケーションへの条件付きアクセス: Windows Server 2012 R2 で AD FS を使用するように構成されたアプリケーションに対して、アクセス ポリシーの登録済みのデバイスを使用できます。 オンプレミスの条件付きアクセスを設定する方法の詳細については、「 [Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定](active-directory-conditional-access.md)」を参照してください。

2. Microsoft Azure 管理ポータルに対するアクセス制御: Azure には、RBAC (ロールベースのアクセス制御) を使用して管理ポータルへのアクセスを制御する機能もあります。 この方法により、ユーザーが Azure 管理ポータルにアクセスした時点で実行できる操作の量を会社が制限できます。 RBAC を使用してポータルへのアクセスを制御することで、IT 管理者は、次のアクセスの管理方法を使用してアクセスを委任できます。

* グループベースのロールの割り当て: ローカルの Active Directory から同期できる Azure AD グループにアクセスを割り当てることができます。 これにより、グループ管理用のツールとプロセスに組織が費やした既存の投資を活用できます。 また、Azure AD Premium の委任されたグループ管理機能も使用できます。
* Azure の組み込みロールの活用: 所有者、共同作業者、閲覧者の&3; つのロールを使用して、ジョブの実行に必要なタスクのみを実行する権限をユーザーとグループに付与できます。
* リソースへのきめ細かなアクセス: 特定のサブスクリプションのユーザーとグループ、リソース グループ、Web サイトやデータベースなどの個々の Azure リソースにロールを割り当てることができます。 このように、ユーザーが必要なすべてのリソースにアクセスし、管理する必要のないリソースには一切アクセスできないようにすることができます。

> [!NOTE]
> この機能の詳細については、 [Azure でのロールベースのアクセス制御](https://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/) に関するページを参照してください。 アプリケーションをビルドしていて、それらのアクセス制御をカスタマイズする必要がある開発者の場合、Azure AD アプリケーション ロールを使用して承認を実行することもできます。 この機能を使用するアプリケーションをビルドする方法については、こちらの [WebApp-RoleClaims-DotNet の例](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) を参照してください。
>
>

3.Microsoft Intune を使用した Office 365 アプリケーションへの条件付きアクセス: IT 管理者は、条件付きアクセスのデバイス ポリシーをプロビジョニングすることで、会社リソースをセキュリティで保護し、同時にインフォメーション ワーカーに準拠デバイスからサービスへのアクセスを許可できます。 詳細については、「 [Office 365 サービス用条件付きアクセスのデバイス ポリシー](active-directory-conditional-access-device-policies.md)」を参照してください。

4.Saas アプリの条件付きアクセス: [この機能](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx)を使用すると、アプリケーションごとの Multi-Factor Authentication のアクセス規則と、信頼されたネットワークを使用していないユーザーのアクセスをブロックする機能を構成できます。 Multi-Factor Authentication の規則は、アプリケーションに割り当てられているすべてのユーザーに適用することも、指定したセキュリティ グループのユーザーにのみ適用することもできます。 ユーザーが組織のネットワーク内の IP アドレスからアプリケーションにアクセスしている場合は、そのユーザーを Multi-Factor Authentication の要件から除外できます。

アクセス制御のオプションでは複数層のアプローチを使用しているため、オプションを比較することはこのタスクには適しません。 必ず、リソースへのアクセスを制御する必要があるシナリオごとに使用可能なすべてのオプションを使用してください。

## <a name="define-incident-response-options"></a>インシデント対応オプションの定義
Azure AD は、ユーザーのアクティビティを監視することによって、IT 部門が環境内の潜在的なセキュリティ リスクを識別できるよう支援します。IT 部門は、Azure AD のアクセスおよび使用状況レポート機能を活用することで、組織のディレクトリの完全性とセキュリティを視覚的に確認できます。 IT 管理者は、この情報を使用して、潜在的なセキュリティ リスクがある箇所をより的確に特定でき、リスクを軽減するための計画を適切に作成できます。  [Azure AD Premium サブスクリプション](active-directory-get-started-premium.md)には一連のセキュリティ レポートがあります。IT 部門は、これらのレポートからセキュリティに関する情報を取得できます。 [Azure AD のレポート](active-directory-view-access-usage-reports.md)は、次のように分類されます。

* **異常レポート**: 異常と考えられるサインイン イベントが含まれます。 この目的は、このようなアクティビティを認識し、イベントが不審であるかどうかを判断できるようにすることです。
* **統合アプリケーション レポート**: 組織内のクラウド アプリケーションの使用状況を明らかにします。 Azure Active Directory は、何千ものクラウド アプリケーションとの統合を提供します。
* **エラー レポート**: 外部アプリケーションにアカウントをプロビジョニングするときに発生することがあるエラーを示します。
* **ユーザー固有レポート**: 特定のユーザーのデバイス/サインイン アクティビティ データを示します。
* **アクティビティ ログ**: 過去 24 時間、過去 7 日間、または過去 30 日間のすべての監査イベントの記録、グループのアクティビティの変更、およびパスワードのリセットと登録のアクティビティが含まれます。

> [!TIP]
> ケースに対処するインシデント対応チームにも役立つもう&1; つのレポートが、 [資格情報が漏洩したユーザー](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) レポートです。  このレポートでは、これらの漏洩した資格情報の一覧とテナントの間で一致した結果を表示します。
>
>

インシデント対応調査中に使用できるその他の Azure AD の主要な組み込みレポートは、次のとおりです。

* **パスワード リセット アクティビティ**: 管理者は、パスワードのリセットが組織でどの程度積極的に使用されているかについての洞察を得ることができます。
* **パスワード リセット登録アクティビティ**: どのユーザーがパスワードのリセット方法を登録したか、またどの方法を選択したかについての洞察を得ることができます。
* **グループ アクティビティ**: アクセス パネルで開始されたグループに対する変更履歴 (ユーザーの追加や削除など) が提供されます。

Azure AD Premium で提供され、インシデント対応調査時に使用できるコア レポート機能のほかに、IT 部門は監査レポートを活用して次の情報を取得できます。

* ロール メンバーシップの変更 (例: グローバル管理者ロールに追加されたユーザー)
* 資格情報の更新 (例: パスワードの変更)
* ドメインの管理 (例: カスタム ドメインの検証、ドメインの削除)
* アプリケーションの追加または削除
* ユーザーの管理 (例: ユーザーの追加、削除、更新)
* ライセンスの追加または削除

インシデント対応のオプションでは複数層のアプローチを使用しているため、オプションを比較することはこのタスクには適しません。 必ず、会社のインシデント対応プロセスの一部として Azure AD のレポート機能を使用する必要があるシナリオごとに使用可能なすべてのオプションを使用してください。

## <a name="next-steps"></a>次のステップ
[ハイブリッド ID 管理タスクの決定](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>関連項目
[設計上の考慮事項の概要](active-directory-hybrid-identity-design-considerations-overview.md)

