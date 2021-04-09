---
title: Azure Active Directory Connect に関する FAQ | Microsoft Docs
description: この記事では、Azure AD Connect についてよく寄せられる質問に回答します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29c0ae8ec210356f6027a46ed01f2a7126ea4a49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644733"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect に関する FAQ

## <a name="general-installation"></a>一般的なインストール

**Q:セキュリティ攻撃の対象領域を小さくするために Azure AD Connect サーバーを強化するにはどうすればよいですか?**

Microsoft では、Azure AD Connect サーバーを強化して、お客様の IT 環境に含まれるこの重要なコンポーネントに対する、セキュリティ攻撃の対象領域を縮小することをお勧めしています。  以下の推奨事項に従うと、お客様の組織に対するセキュリティ リスクが低下します。

* ドメイン参加しているサーバー上に Azure AD Connect をデプロイし、管理アクセス権を、ドメイン管理者やその他の厳格に管理されたセキュリティ グループに制限する

詳細については、次を参照してください。 

* [管理者グループのセキュリティ保護](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [ビルトイン Administrator アカウントのセキュリティ保護](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [攻撃対象領域の縮小によるセキュリティの向上と維持](/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Active Directory の攻撃対象領域の縮小](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**Q:Azure Active Directory (Azure AD) 全体管理者が 2 要素認証 (2FA) を有効にしている場合、インストールは機能しますか。**  
2016 年 2 月のビルド時点で、このシナリオがサポートされています。

**Q:Azure AD Connect を無人インストールする方法はありますか。**  
インストール ウィザードを使用する場合にのみ、Azure AD Connect のインストールはサポートされます。 サイレント モードでの無人インストールはサポートされていません。

**Q:ドメインに接続できないフォレストがあります。Azure AD Connect をインストールにはどうすればよいですか。**  
2016 年 2 月のビルド時点で、このシナリオがサポートされています。

**Q:Azure Active Directory Domain Services (Azure AD DS) ヘルス エージェントはサーバー コア上で動作しますか。**  
はい。 エージェントをインストールした後、次の PowerShell コマンドレットを使って登録プロセスを実行できます。 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Q:Azure AD Connect では、2 つのドメインからの Azure AD への同期はサポートしていますか。**  
はい、このシナリオはサポートされています。 [複数のドメイン](how-to-connect-install-multiple-domains.md)に関するページを参照してください。
 
**Q:Azure AD Connect では同じ Active Directory ドメインに対して複数のコネクタを使用できますか。**  
いいえ、同じ AD ドメインの複数のコネクタはサポートされていません。 

**Q:Azure AD Connect データベースをローカル データベースからリモート SQL Server インスタンスに移行できますか。**    
はい、次の手順では、この移行方法に関する一般的なガイダンスを提供しています。 現在、より詳細なドキュメントの作成に取り組んでいます。
1. LocalDB ADSync データベースをバックアップします。
この最も簡単な方法は、Azure AD Connect と同じコンピューターにインストールされている SQL Server Management Studio を使用することです。 *(LocalDb).\ADSync* に接続し、ADSync データベースをバックアップします。

2. リモート SQL Server インスタンスに ADSync データベースを復元します

3. 既存の[リモート SQL Database](how-to-connect-install-existing-database.md) に対して Azure AD Connect をインストールします。
   この記事では、ローカルの SQL Database を使用して移行する方法について説明します。 リモート SQL Database を使用して移行する場合は、手順 5 で Windows Sync サービスを実行する既存のサービス アカウントも入力する必要があります。 この同期エンジンのサービス アカウントを次に示します。
   
      **既存のサービス アカウントを使用する**:既定では、同期サービスで使用する仮想サービス アカウントが Azure AD Connect によって使用されます。 リモート SQL Server インスタンスを使用する場合、または認証が必要なプロキシを使用する場合は、ドメイン内のマネージド サービス アカウントまたはサービス アカウントを使用し、パスワードを知っている必要があります。 このような場合は、使用するアカウントを入力します。 サービス アカウントのログイン資格情報を作成できるように、インストールを実行しているユーザーが SQL のシステム管理者であることを確認します。 詳細については、[Azure AD Connect アカウントとアクセス許可](reference-connect-accounts-permissions.md#adsync-service-account)に関するページを参照してください。 
   
      最新のビルドでは、SQL 管理者が帯域外でデータベースのプロビジョニングを実行し、データベース所有者権限を持つ Azure AD Connect 管理者がインストールできます。 詳細については、「[SQL によって委任された管理者のアクセス許可を使用した Azure AD Connect のインストール](how-to-connect-install-sql-delegation.md)」を参照してください。

シンプルにするために、Azure AD Connect をインストールするユーザーは、SQL のシステム管理者にすることをお勧めします。 ただし、最近のビルドでは、「[SQL によって委任された管理者のアクセス許可を使用した Azure AD Connect のインストール](how-to-connect-install-sql-delegation.md)」で説明されているように、委任された SQL 管理者を使用できるようになりました。

**Q: フィールドからのベスト プラクティスにはどのようなものがありますか?**  

以下は、エンジニアリング、サポート、および Microsoft のコンサルタントが長年にわたり開発してきたベスト プラクティスのいくつかについて情報を提供する文書です。  すばやく参照できる箇条書きで示されています。  この一覧を包括的なものとすることが試みられていますが、まだ一覧に反映されていない追加のベスト プラクティスが存在する可能性があります。

- 完全な SQL を使用する場合も、ローカルとリモートの比較には変わりがありません。
    - ホップ数がより少ない
    - トラブルシューティングがより簡単
    - 複雑さがより軽減されている
    - リソースを SQL に指定し、Azure AD Connect と OS に対するオーバーヘッドを許可する必要があります。
- 可能な限りプロキシをバイパスします。プロキシをバイパスすることができない場合は、5 分を超えるタイムアウト値を確保する必要があります。
- プロキシが必要な場合は、machine.config ファイルにプロキシを追加する必要があります。
- ローカルの SQL ジョブとメンテナンスに注意し、それらが Azure AD Connect に与える影響にも注意してください - 特に再インデックス作成
- DNS を外部で確実に解決できるようにします。
- 物理サーバーまたは仮想サーバーのどちらを使用しているかに関わらず、[サーバー仕様](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect)が確実に推奨に従っているようにします。
- 仮想サーバーを使用している場合、必要なリソースは確実に専用にします。
- ディスクおよびディスク構成が確実に SQL Server のベスト プラクティスに対応するようにします。
- Azure AD Connect Health をインストールおよび構成して監視を行います。
- Azure AD Connect に組み込まれている削除のしきい値を使用します。
- 追加される可能性があるすべての変更および新しい属性に対して準備するために、リリースの更新情報を慎重に確認してください。
- すべてをバックアップする
    - キーをバックアップする
    - 同期規則をバックアップする
    - サーバーの構成をバックアップする
    - SQL データベースをバックアップする
- SQL VSS Writer (サード パーティ スナップショットを使用した仮想サーバーでは一般的) なしで SQL をバックアップしているサード パーティ製バックアップ エージェントが決して存在しないようにします。
- 使用されているカスタム同期規則によって複雑さが増す場合は、その規則の量を制限します。
- Azure AD Connect サーバーを階層 0 サーバーとして扱います。
- 影響と適切なビジネス ドライバーをよく理解せずにクラウド同期規則を変更することに用心します。
- Azure AD Connect および Azure AD Connect Health をサポートするために適切な URL およびファイアウォールのポートが開かれていることを確認します。
- クラウドでフィルター処理される属性を活用して、ファントム オブジェクトのトラブルシューティングを行い、そのオブジェクトを防止します。
- ステージング サーバーでは、サーバー間の一貫性を保つのために Azure AD Connect 構成の解析ツールを確実に使用します。
- ステージング サーバーは個別のデータ センター (物理的な場所) に置く必要があります。
- ステージング サーバーは高可用性ソリューションになることを想定していませんが、複数のステージング サーバーを備えることができます。
- "ラグ" ステージング サーバーを導入すれば、エラーが発生した場合の潜在的なダウンタイムを軽減することが可能です。
- まず、ステージング サーバー上のすべてのアップグレードをテストおよび検証します。
- ステージング サーバーに切り替える前に、必ずエクスポートを検証してください。  完全インポートおよび完全同期用のステージング サーバーを活用して、ビジネスへの影響を軽減します
- Azure AD Connect サーバー間でのバージョンの一貫性を可能な限り保持します。 

**Q:ワークグループ コンピューター上に Azure AD Connector アカウントを作成することを Azure AD Connect に許可することができますか?**
いいえ。  Azure AD Connector アカウントの自動作成を Azure AD Connect に許可するには、該当するコンピューターがドメインに参加している必要があります。  

## <a name="network"></a>ネットワーク
**Q:ファイアウォールやネットワーク デバイスなど、ネットワーク上で接続を開いたままにすることができる時間を制限するものがあります。Azure AD Connect を使用する場合、クライアント側のタイムアウトしきい値はどのくらいにすればいいでしょうか。**  
すべてのネットワーク ソフトウェアや物理デバイスなど、接続を開ける最大時間を制限するものは、Azure AD Connect クライアントがインストールされているサーバーと Azure Active Directory 間の接続に対して少なくとも 5 分 (300 秒) のしきい値を使用する必要があります。 この推奨事項は、以前リリースされたすべての Microsoft ID 同期ツールにも適用されます。

**Q:シングル ラベル ドメイン (SLD) はサポートされていますか。**  
シングル ラベル ドメインのネットワーク構成が適切に機能している限り、このネットワーク構成に対しては、シングル ラベル ドメインを利用した Azure AD Connect 同期の利用がサポートされることを強くお勧めします ([記事を参照](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains))。

**Q:切り離された AD ドメインを持つフォレストはサポートされますか。**  
いいえ。Azure AD Connect は、切り離された名前空間を持つオンプレミスのフォレストはサポートしていません。

**Q:"ドット形式" の NetBios 名はサポートされていますか。**  
いいえ。Azure AD Connect では、NetBios 名にドット (.) が含まれているオンプレミスのフォレストまたはドメインをサポートしていません。

**Q:純粋な IPv6 環境はサポートされますか。**  
いいえ。Azure AD Connect は、純粋な IPv6 環境はサポートしていません。

**Q: マルチフォレスト環境を使用しており、2 つのフォレスト間のネットワークでは NAT (ネットワーク アドレス変換) を使用しています。2 つのフォレスト間で Azure AD Connect の使用はサポートされますか。**</br>
いいえ、NAT を介した Azure AD Connect の使用はサポートされていません。 

## <a name="federation"></a>フェデレーション
**Q:Microsoft 365 の証明書を更新するように求める電子メールを受け取った場合はどうすればいいですか。**  
証明書の更新方法については、[証明書の更新](how-to-connect-fed-o365-certs.md)に関する記事を参照してください。

**Q:Microsoft 365 証明書利用者の "証明書利用者の自動更新" を設定しました。トークン署名証明書が自動的にロールオーバーされるときに、何か必要な操作はありますか。**  
[証明書の更新](how-to-connect-fed-o365-certs.md)に関する記事に記載されているガイダンスに従ってください。

## <a name="environment"></a>環境
**Q:Azure AD Connect のインストール後のサーバー名の変更はサポートされていますか。**  
いいえ。 サーバー名を変更すると、同期エンジンは SQL データベース インスタンスに接続できなくなり、サービスを開始できなくなります。

**Q:FIPS 対応のコンピューター上で次世代暗号化 (NGC) 同期規則はサポートされていますか?**  
いいえ。  それらはサポートされていません。

**Q.Azure portal で同期されているデバイス (たとえば HAADJ) を無効にしても、再び有効になるのはなぜですか?**<br>
同期されているデバイスは、オンプレミスで作成または管理されている場合があります。 同期されているデバイスがオンプレミスで有効になっているときは、前に管理者によって無効になった場合でも、Azure portal で再び有効になることがあります。 同期されているデバイスを無効にするには、オンプレミスの Active Directory を使用して、コンピューター アカウントを無効にします。

**Q.Microsoft 365 または Azure AD ポータルで同期されているユーザーのサインインをブロックした場合、サインイン時に再びブロックが解除されるのはなぜですか?**<br>
同期されているユーザーは、オンプレミスで作成または管理されている場合があります。 アカウントがオンプレミスで有効になっている場合、管理者によって実行されたサインイン ブロックは解除される可能性があります。

## <a name="identity-data"></a>ID データ
**Q:Azure AD の userPrincipalName (UPN) 属性がオンプレミス UPN と一致しないのはなぜですか。**  
詳細については、以下の記事を参照してください。

* [Microsoft 365、Azure、Intune におけるユーザー名が、オンプレミスの UPN または代替ログイン ID と一致しない](https://mskb.pkisolutions.com/kb/2523192)
* [異なるフェデレーション ドメインを使用するようにユーザー アカウントの UPN を変更した後、Azure Active Directory 同期ツールによって変更が同期されない](https://mskb.pkisolutions.com/kb/2669550)

また、「[Azure AD Connect 同期サービスの機能](how-to-connect-syncservice-features.md)」に記載された手順に従って、同期エンジンによる UPN の更新が許可されるように Azure AD を構成することもできます。

**Q:オンプレミスの Azure AD グループまたは連絡先オブジェクトと既存の Azure AD グループまたは連絡先オブジェクトとのあいまい一致はサポートされていますか。**  
はい。このあいまい一致は proxyAddress に基づいています。 メールが有効でないグループに対して、あいまい一致はサポートされていません。

**Q:既存の Azure AD グループまたは連絡先オブジェクトに対して手動で設定された ImmutableId 属性とオンプレミスの Azure AD グループまたは連絡先オブジェクトとの完全一致はサポートされていますか。**  
いいえ。既存の Azure AD グループまたは連絡先オブジェクトに対して手動で設定された ImmutableId 属性の完全一致は現在サポートされていません。

## <a name="custom-configuration"></a>カスタム構成
**Q:Azure AD Connect 用の PowerShell コマンドレットのドキュメントはどこにありますか。**  
このサイトに記載されているコマンドレットを除き、Azure AD Connect で使用されている PowerShell コマンドレットは、ユーザーによる使用をサポートしていません。

**Q:Synchronization Service Manager の [サーバーのエクスポート/インポート] オプションを使用して、サーバー間で構成を移動できますか。**  
いいえ。 このオプションはすべての構成設定を取得しないため、使用すべきではありません。 代わりに、2 台目のサーバーでウィザードを使用して基本構成を作成し、同期ルール エディターを使用して PowerShell スクリプトを生成し、サーバー間でカスタム ルールを移動してください。 詳細については、「[スウィング移行](how-to-upgrade-previous-version.md#swing-migration)」を参照してください。

**Q:Azure サインイン ページではパスワードがキャッシュされますか。また、パスワード入力要素の *autocomplete 属性を "false"* に設定することで、このキャッシュを防ぐことはできますか。**  
現在、オートコンプリート タグを含め、 **[パスワード]** フィールドの HTML 属性を変更することはできません。 現在、 **[パスワード]** フィールドに属性を追加できるようカスタム Javascript を許可する機能の開発に取り組んでいます。

**Q:Azure サインイン ページでは、以前正常にサインインしたユーザーのユーザー名が表示されますか。また、この動作は無効にできますか。**  
現在、オートコンプリート タグを含め、 **[パスワード]** 入力フィールドの HTML 属性を変更することはできません。 現在、 **[パスワード]** フィールドに属性を追加できるようカスタム Javascript を許可する機能の開発に取り組んでいます。

**Q:同時セッションを防ぐ方法はありますか。**  
いいえ。

## <a name="auto-upgrade"></a>自動アップグレード

**Q:自動アップグレードを使用した場合の利点と結果について教えてください。**  
Microsoft では、すべてのお客様に対し、Azure AD Connect インストールの自動アップグレードを有効にするようお勧めしています。 自動アップグレードの利点は、常に最新の修正プログラムを適用できることです (Azure AD Connect で確認された脆弱性に対応するセキュリティ更新プログラムなど)。 アップグレード プロセスは簡単で、新しいバージョンがリリースされ次第、自動的に実行されます。 Azure AD Connect の何千ものお客様が、新しいリリースのたびに自動アップグレードを使用しています。

自動アップグレード プロセスでは、まずインストールが自動アップグレードの対象かどうかを確認します。 対象の場合は、アップグレードが実行され、テストされます。 このプロセスには、ルールおよび特定の環境要因に対するカスタム変更を検索する処理も含まれています。 テストの結果、アップグレードが失敗していた場合は、旧バージョンが自動的に復元されます。

環境の規模によっては、処理に数時間かかることがあります。 アップグレードの実行中は、Windows Server Active Directory と Azure AD 間の同期は実行されません。

**Q:自動アップグレードが動作しなくなっているため、新しいバージョンをインストールする必要があるという内容の電子メールが送られてきました。なぜそうしなければならないのですか。**  
昨年リリースされた Azure AD Connect のバージョンでは、特定の状況において、自動アップグレード機能がサーバー上で無効になることがあります。 この問題は、Azure AD Connect バージョン 1.1.750.0 で修正しました。 問題の影響を受けている場合は、PowerShell スクリプトを実行して修復するか、手動で最新バージョンの Azure AD Connect にアップグレードすることで軽減できます。 

PowerShell スクリプトを実行するには、[スクリプトをダウンロード](/samples/browse/?redirectedfrom=TechNet-Gallery)して、管理 PowerShell ウィンドウの Azure AD Connect サーバー上で実行します。 スクリプトの実行方法については、[この短い動画](https://aka.ms/repairaadcau)をご覧ください。

手動でアップグレードするには、AADConnect.msi ファイルの最新バージョンをダウンロードし、実行する必要があります。
 
-  現在のバージョンが 1.1.750.0 よりも前の場合は、[最新バージョン](https://www.microsoft.com/download/details.aspx?id=47594)をダウンロードしてアップグレードしてください。
- Azure AD Connect のバージョンが 1.1.750.0 以降の場合、以降の操作は必要ありません。 自動アップグレードの修正プログラムを含むバージョンを既に使用しています。 

**Q:最新バージョンにアップグレードして、自動アップグレードを再度有効にするよう求める内容の電子メールが送られてきました。バージョン 1.1.654.0 を使用しています。アップグレードする必要はありますか。**  
はい。バージョン 1.1.750.0 以降にアップグレードして、自動アップグレードを再度有効にする必要があります。 [最新バージョンをダウンロードしてアップグレード](https://www.microsoft.com/download/details.aspx?id=47594)してください。

**Q:最新バージョンにアップグレードして、自動アップグレードを再度有効にするよう求める内容の電子メールが送られてきました。PowerShell を使用して自動アップグレードを有効にしたのですが、最新バージョンをインストールする必要があるのでしょうか。**  
はい。1.1.750.0 以降のバージョンにアップグレードする必要があります。 PowerShell を使用して自動アップグレード サービスを有効にした場合、1.1.750.0 より前のバージョンで見つかった自動アップグレードの問題は回避されません

**Q:新しいバージョンにアップグレードする必要があるのですが、Azure AD Connect を誰がインストールしたのかわからないため、ユーザー名とパスワードがわかりません。これらの情報は必要ですか。**
最初に Azure AD Connect をアップグレードするときに使用されたユーザー名とパスワードを知っている必要はありません。 全体管理者ロールを持つ任意の Azure AD アカウントを使用してください。

**Q:現在使用している Azure AD Connect のバージョンはどうすれば確認できますか。**  
サーバーにインストールされている Azure AD Connect のバージョンを確認するには、コントロール パネルに移動し、次のように **[プログラム]**  >  **[プログラムと機能]** の順に選択して、インストールされている Microsoft Azure AD Connect のバージョンを調べてください。

![コントロール パネルの Azure AD Connect のバージョン](./media/reference-connect-faq/faq1.png)

**Q:最新のバージョンの Azure AD Connect にアップグレードするにはどうすればよいですか。**  
最新バージョンにアップグレードする方法については、「[Azure AD Connect:旧バージョンから最新バージョンにアップグレードする](how-to-upgrade-previous-version.md)」を参照してください。 

**Q:昨年、Azure AD Connect を最新バージョンにアップグレードしたのですが、再度アップグレードする必要はありますか。**  
Azure AD Connect チームは、このサービスを頻繁に更新しています。 バグの修正プログラムやセキュリティ更新プログラムだけでなく、新機能を利用するには、サーバーを最新バージョンの状態に保つことが重要です。 自動アップグレードを有効にしておけば、ソフトウェアのバージョンは自動的に更新されます。 Azure AD Connect のバージョン リリース履歴を確認するには、「[Azure AD Connect:バージョンのリリース履歴](reference-connect-version-history.md)」を参照してください。

**Q:アップグレードにかかる時間と、ユーザーへの影響について教えてください。**  
アップグレードに必要な時間は、テナントのサイズによって変わります。 大規模な組織の場合は、夜間や週末にアップグレードを実行することをお勧めします。 アップグレード中は、同期アクティビティは実行されません。

**Q:Azure AD Connect のアップグレードは行ったと思うのですが、Office ポータルにはまだ DirSync と表示されます。なぜですか?**  
Office チームでは、Office ポータルに現在の製品名が反映されるようにするべく取り組んでいます。 現状では、ご使用の同期ツールは反映されません。

**Q:自動アップグレードの状況に "中断" と表示されています。なぜ中断されているのでしょうか。自分で有効化する必要があるのでしょうか。**  
以前のバージョンでは、特定の状況において、自動アップグレードの状態が "中断" に設定されたままになるというバグが確認されています。 技術的には手動で有効にすることはできますが、いくつかの複雑な手順が必要です。 Azure AD Connect の最新バージョンをインストールすることをお勧めします。

**Q:私の会社では変更管理の要件が厳しく設定されているため、変更がプッシュアウトされるタイミングを制御したいのですが、自動アップグレードが起動されるタイミングを制御することはできますか。**  
いいえ。現在、そのような機能はありません。 この機能は、今後のリリースのために評価されています。

**Q:自動アップグレードが失敗した場合、電子メールによる通知は送られてきますか。アップグレードが成功したかどうかを確認する方法について教えてください。**  
アップグレードの結果は通知されません。 この機能は、今後のリリースのために評価されています。

**Q:自動アップグレードのプッシュ アウト予定に関するタイム ラインは公表されていますか。**  
自動アップグレードは、新しいバージョンのリリース プロセスの第一歩です。 新しいリリースがあるたびに、アップグレードが自動的にプッシュされます。 Azure AD Connect の新バージョンについては、[Azure AD のロードマップ](../fundamentals/whats-new.md)に関するページで事前に発表されます。

**Q:自動アップグレードでは Azure AD Connect Health もアップグレードされますか。**  
はい、自動アップグレードでは、Azure AD Connect Health もアップグレードされます

**Q:ステージング モードの Azure AD Connect サーバーも自動アップグレードされますか。**  
はい、ステージング モードの Azure AD Connect サーバーは自動アップグレードされます。

**Q:自動アップグレードが失敗し、Azure AD Connect サーバーが起動しない場合には、どうすればよいのでしょうか。**  
まれに、アップグレードの実行後、Azure AD Connect サービスが開始されないことがあります。 そのような場合は、サーバーを再起動してください。通常はこれで問題が解決します。 それでも Azure AD Connect サービスが開始されない場合は、サポート チケットを発行してください。 詳細については、[サービス要求を作成して Microsoft 365 サポートに問い合わせる](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support)方法に関する記事を参照してください。 

**Q:Azure AD Connect を新しいバージョンにアップグレードする際のリスクがよくわかりません。アップグレードについて電話で説明を受けることはできますか。**  
新しいバージョンの Azure AD Connect にアップグレードする場合は、[サービス要求を作成して Microsoft 365 サポートに問い合わせる](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support)方法に関する記事を参照してサポート チケットを開いてください。

## <a name="operational-best-practice"></a>操作のベスト プラクティス    
Windows Server Active Directory と Azure Active Directory 間で同期を行う場合に実行する必要があるベスト プラクティスを次に示します。

**同期されるすべてのアカウントに Multi-Factor Authentication を適用する** Azure AD Multi-Factor Authentication は、ユーザーにとり代わらずに平易でありながら、データやアプリケーションへのアクセスを保護します。 第 2 の認証方式を要求することでセキュリティが向上し、使用が簡単なさまざまな認証方法によって強力な認証を実現しています。 ユーザーは、管理者が行う構成上の決定に基づいて、MFA で認証が行われる場合と行われない場合があります。 MFA の詳細は、こちら https://www.microsoft.com/security/business/identity/mfa?rtc=1 を参照してください。

**Azure AD Connect サーバーのセキュリティ ガイドラインに従う** Azure AD Connect サーバーには、重要な ID データが格納されているため、[Active Directory 管理階層モデル](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)の説明に従い、階層 0 のコンポーネントとして取り扱う必要があります。 こちらの[お使いの AADConnect サーバーを保護するガイドライン](./how-to-connect-install-prerequisites.md#azure-ad-connect-server)の説明も参照してください。

**資格情報の漏洩に対して PHS を有効にする** パスワード ハッシュの同期では、ご自分のハイブリッド アカウントの [漏洩資格情報検出](../identity-protection/concept-identity-protection-risks.md)も有効になります。 Microsoft はダーク Web の研究者や法執行機関と協力し、公で利用できるユーザー名とパスワードのペアを見つけています。 これらの任意のペアがご自分のユーザーのものと一致する場合、関連付けられているアカウントは高リスクに移されます。 


## <a name="troubleshooting"></a>トラブルシューティング
**Q:Azure AD Connect に関するヘルプを参照する方法を教えてください。**

[Microsoft サポート技術情報 (KB) の検索](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Azure AD Connect のサポートに関する一般的な破損時補償の技術的な解決策について、KB を検索してください。

[Azure Active Directory に関する Microsoft Q&A 質問ページ](/answers/topics/azure-active-directory.html)

* [Azure AD コミュニティ](/answers/topics/azure-active-directory.html)にアクセスして、技術的な質問と回答を探し、質問をすることができます。

[Azure AD のサポートを受ける](../fundamentals/active-directory-troubleshooting-support-howto.md)

**Q:同期ステップのエラーの後にイベント 6311 と 6401 が発生するのはなぜですか。**

イベント 6311 - "**The server encountered an unexpected error while performing a callback (コールバックの実行中にサーバーで予期しないエラーが発生しました)** " および 6401 - "**管理エージェント コントローラーで予期しないエラーが発生しました**" は、同期ステップのエラー後に常にログに記録されます。 これらのエラーを解決するには、同期ステップのエラーをクリーンアップする必要があります。  詳細については、「[同期中のエラーのトラブルシューティング](tshoot-connect-sync-errors.md)」および「[Azure AD Connect Sync を使用したオブジェクト同期のトラブルシューティング](tshoot-connect-objectsync.md)」を参照してください