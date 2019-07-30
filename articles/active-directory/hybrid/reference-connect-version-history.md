---
title: Azure AD Connect:バージョンのリリース履歴 | Microsoft Docs
description: この記事では、Azure AD Connect と Azure AD Sync のすべてのリリースの一覧を示します
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b13b23e59595acf8c637a2ef58c8098256920bea
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654058"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect:バージョンのリリース履歴
Azure Active Directory (Azure AD) チームは、Azure AD Connect を定期的に更新し、新機能を追加しています。 すべての追加機能がすべてのユーザーに適用されるわけではありません。


この記事は、リリースされたバージョンを追跡し、最新バージョンで変更された点を確認するためのものです。

次の表に関連トピックの一覧を示します。

トピック |  詳細
--------- | --------- |
Azure AD Connect からのアップグレード手順 | Azure AD Connect の [以前のバージョンから最新バージョンにアップグレード](how-to-upgrade-previous-version.md) するさまざまな方法を説明しています。
必要なアクセス許可 | 更新プログラムの適用に必要なアクセス許可については、[アカウントとアクセス許可](reference-connect-accounts-permissions.md#upgrade)に関するページを参照してください。

ダウンロード | [Azure AD Connect をダウンロード](https://go.microsoft.com/fwlink/?LinkId=615771)します。

>[!NOTE]
>Azure AD Connect の新バージョンのリリースは、サービスの操作の機能性を確保するためのいくつかの品質管理手順が必要なプロセスであり、このプロセスを進めている間、新しいリリースのバージョン番号とリリースの状態が、最新の状況を反映するように更新されます。
このプロセスを進めている間、リリースのバージョン番号では、"1.3.X.0" のようにマイナー リリース番号の位置に "X" が表示されます。これは、このドキュメントのリリース ノートは、"1.3." で始まるすべてのバージョンで有効であることを示しています。 リリース プロセスが終了すると、すぐにリリース バージョン番号が最近リリースされたバージョンに更新され、リリースの状態が "ダウンロードと自動アップグレード用にリリース済み" に更新されます。
Azure AD Connect のすべてのリリースが自動アップグレードに対応しているわけではありません。 リリースの状態により、リリースが自動アップグレードに対応しているか、ダウンロードにのみ対応しているかが分かります。 Azure AD Connect サーバーに対して自動アップグレードが有効になっている場合、そのサーバーは、自動アップグレードのためにリリースされた Azure AD Connect の最新バージョンに自動的にアップグレードされます。 Azure AD Connect のすべての構成が自動アップグレードの対象となっているわけではないので注意してください。 詳細については、[自動アップグレード](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)に関するこのリンクを参照してください。

## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Azure AD Connect の以前のバージョンから 1.3.21.0 へのアップグレードに関する既知の問題 (Azure AD Connect が正常にアップグレードされても O365 ポータルによって更新されたバージョンが反映されない) があります。
>
> これを解決するには、**AdSync** モジュールをインポートしてから、Azure AD Connect サーバー上で `Set-ADSyncDirSyncConfiguration` PowerShell コマンドレットを実行します。  次の手順を使用できます。
>
>1. 管理者モードで PowerShell を開く
>2. `Import-Module "ADSync"` を実行します。
>3. `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""` を実行します。
 


### <a name="release-status"></a>リリースの状態 

2019/05/14:ダウンロード対象としてリリース済み


### <a name="fixed-issues"></a>修正された問題 

- Microsoft Azure Active Directory Connect ビルド 1.3.20.0 に存在する特権の昇格の脆弱性を修正しました。  この脆弱性により、特定の条件下で、攻撃者は特権アカウントのコンテキストで 2 つの PowerShell コマンドレットを実行し、特権の必要なアクションを実行することができる可能性があります。  このセキュリティ更新プログラムは、これらのコマンドレットを無効にすることで問題を修正します。 詳細については、[セキュリティ更新プログラム](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1000)に関する記事を参照してください。

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>リリースの状態 

2019/04/24:ダウンロード対象としてリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化 

- ドメインの更新に対するサポートが追加されました。 
- Exchange メールのパブリック フォルダー機能が一般提供されました。 
- サービス エラーに対するウィザードのエラー処理が機能強化されました。 
- コネクタのプロパティ ページで、古い UI の警告リンクが追加されました。 
- 統合グループの書き戻し機能が一般提供されるようになりました。 
- DC で LDAP コントロールがない場合の SSPR エラー メッセージが改善されました。 
- インストール時の DCOM レジストリ エラーに対する診断が追加されました。  
- PHS RPC エラーのトレースが機能強化されました。 
- 子ドメインからの EA 資格情報が許可されるようになりました。 
- インストール中のデータベース名の入力が許可されるようになりました (既定名 ADSync)。
- Ping での WS-Trust の修正をピックアップするためと新しい Azure インスタンスに対するサポートを追加するために ADAL 3.19.8 へのアップグレードが行われました。 
- samAccountName、DomainNetbios、および DomainFQDN をクラウドに送信するためのグループ同期規則が変更されました (要求で必要)。 
- 同期規則の既定の処理が変更されました。詳細については[こちら](how-to-connect-fix-default-rules.md)を参照してください。
- Windows サービスとして実行される新しいエージェントが追加されました。 “Admin Agent” という名前のこのエージェントでは、Azure AD Connect サーバーの詳細なリモート診断が可能であり、お客様がサポート ケースを開いたときに Microsoft のエンジニアがトラブルシューティングを行う際に役に立ちます。 エージェントはインストールされず、既定で有効になっています。  エージェントをインストールして有効にする方法の詳細については、「[Azure AD Connect 管理エージェントとは](whatis-aadc-admin-agent.md)」を参照してください。 
- エンド ユーザー ライセンス契約 (EULA) が更新されました。 
- ログインの種類として AD FS を使用するデプロイに対する自動アップグレードのサポートが追加されました。  これにより、アップグレード プロセスの一環として AD FS の Azure AD 証明書利用者信頼を更新するという要件が削除されます。 
- 分析/更新の信頼とリセットの信頼という 2 つのオプションがある Azure AD 信頼管理タスクが追加されました。 
- AD FS Azure AD 証明書利用者信頼の動作が、常に -SupportMultipleDomain スイッチを使用するように変更されました。 (信頼と Azure AD ドメインの更新が含まれます)。 
- インストール済みの証明書を使用するオプションの削除によって、新しい AD FS ファームのインストール動作で .pfx 証明書が必要になるように変更されました。
- 新しい AD FS ファームのワークフローのインストールが、1 台の AD FS と 1 台の WAP サーバーのデプロイのみを許可するように更新されました。  すべてのサーバーの追加は、初期インストールの後で実行されます。 

### <a name="fixed-issues"></a>修正された問題 


- ADSync サービスの SQL 再接続ロジックが修正されました。 
- 空の SQL AOA DB を使用したクリーン インストールを許可するための修正が行われました。 
- GWB アクセス許可を絞り込むための PS アクセス許可が修正されました。 
- LocalDB での VSS エラーが修正されました。  
- オブジェクトの種類が範囲外の場合の誤解されやすいエラー メッセージが修正されました。 
- サーバーでの Azure AD PowerShell のインストールで、Azure AD Connect とのアセンブリの競合が発生する可能性があるという問題が修正されました。 
- 古い UI でコネクタの資格情報が更新されるときのステージング サーバーでの PHS のバグが修正されました。 
- いくつかのメモリ リークが修正されました。 
- 自動更新に関するさまざまな修正が行われました。 
- エクスポートと未確認のインポート処理に対するさまざまな修正が行われました。 
- ドメインと OU のフィルター処理でのバックスラッシュの処理のバグが修正されました。 
- ADSync サービスが停止するまで 2 分以上かかり、アップグレード時間に問題が発生するという問題を修正しました。 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>リリースの状態

2018/12/18:ダウンロード対象としてリリース済み

### <a name="fixed-issues"></a>修正された問題

このビルドでは、Azure AD Connect に付属の非標準コネクタ (Generic LDAP コネクタや Generic SQL コネクタなど) を更新します。 適用可能なコネクタの詳細については、「[コネクタ バージョンのリリース履歴](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history)」でバージョン 1.1.911.0 を参照してください。


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>リリースの状態
2018 年 12 月 11 日:ダウンロード対象としてリリース済み

### <a name="fixed-issues"></a>修正された問題
この修正プログラムのビルドでは、デバイス ライトバックを有効にするときに、RegisteredDevices コンテナーに対して、指定されたフォレスト内でターゲット ドメインを選択できます。  新しいデバイス オプション機能が含まれる、以前のバージョン (1.1.819.0 から 1.2.68.0) で、RegisteredDevices コンテナーの場所はフォレストのルートに制限されており、子ドメインが許可されていませんでした。  この制限は、新しいデプロイのみで表面化し、配置済みのアップグレードは影響を受けませんでした。  

更新されたデバイスのオプション機能を含む任意のビルドを新しいサーバーにデプロイして、デバイス ライトバックを有効にする場合、フォレスト ルート内に配置しないためには、コンテナーの場所を手動で指定する必要があります。  これを行うには、[ライトバック フォレスト] ページで、デバイス ライトバックを無効にして、コンテナーの場所を指定できるように再度有効にする必要があります。



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>リリースの状態 

2018 年 11 月 30 日:ダウンロード対象としてリリース済み

### <a name="fixed-issues"></a>修正された問題

この修正プログラムのビルドでは、同期サーバー上の MSOnline PowerShell ギャラリー モジュールが独立して存在するため、認証エラーが発生する競合が修正されます。



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>リリースの状態 

2018 年 11 月 19 日:ダウンロード対象としてリリース済み

### <a name="fixed-issues"></a>修正された問題

この修正プログラムのビルドでは、Windows Server 2008/R2 で ADDS ドメイン コントローラーを使用するとパスワード ライトバックが失敗する前回のビルドでの回帰が修正されます。

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>リリースの状態 

2018 年 10 月 25 日: ダウンロード対象としてリリース済み

 
### <a name="new-features-and-improvements"></a>新機能と機能強化 


- ホストされているボイス メールが期待どおりに動作していることを確認するために、属性の書き戻し機能が変更されました。  一部のシナリオでは、Azure AD は、null 値での書き戻し中に msExchUcVoicemailSettings 属性を上書きしていました。  これで Azure AD は、クラウドの値が設定されていない場合は、この属性のオンプレミスの値をクリアしないようになりました。
- Azure AD への接続の問題を調査および特定するため、Azure AD Connect ウィザードに診断が追加されました。 これらと同じ診断は、Test- AdSyncAzureServiceConnectivity コマンドレットを使用して Powershell を介して直接実行することもできます。 
- AD への接続の問題を調査および特定するため、Azure AD Connect ウィザードに診断が追加されました。 これらと同じ診断は、ADConnectivityTools Powershell モジュールの Start-ConnectivityValidation 関数を使用して、Powershell を介して直接実行することもできます。  詳細については、[ADConnectivityTool PowerShell モジュール](how-to-connect-adconnectivitytools.md)に関するページを参照してください
- ハイブリッド Azure Active Directory Join とデバイスの書き戻し用の AD スキーマのバージョンの事前チェックが追加されました 
- ディレクトリ拡張ページ属性の検索で大文字と小文字が区別されないように変更されました。
-   TLS 1.2 の完全なサポートが追加されました。 このリリースでは、Azure AD Connect がインストールされているマシン上で、他のすべてのプロトコルを無効にし、TLS 1.2 のみを有効にすることがサポートされています。  詳細については、[Azure AD Connect への TLS 1.2 の適用](reference-connect-tls-enforcement.md)に関するページを参照してください

 

### <a name="fixed-issues"></a>修正された問題   

- SQL Always On が使用されている場合に Azure AD Connect のアップグレードが失敗したバグを修正しました。 
- スラッシュが含まれている OU 名が正しく解析されるよう、バグを修正しました。 
- ステージング モードでのクリーン インストールの場合にパススルー認証が無効になる問題を修正しました。 
- トラブルシューティング ツールの実行中に PowerShell モジュールの読み込みを妨げるバグを修正しました 
- 顧客がホスト名の最初の文字に数値を使用できないバグを修正しました。 
- Azure AD Connect が無効なパーティションとコンテナーの選択を許可するバグを修正しました 
- デスクトップ SSO が有効になっている場合の「無効なパスワードです」というエラー メッセージを修正しました。 
- AD FS 信頼管理に関するさまざまなバグの修正  
- デバイスの書き戻しの構成時 - msDs-DeviceContainer オブジェクト クラス (WS2012 R2 で導入) を検索するためのスキーマの確認を修正しました

 
## <a name="118820"></a>1.1.882.0  

9/7/2018: ダウンロード向けにリリース済み。自動アップグレード向けにはリリース予定なし 

### <a name="fixed-issues"></a>修正された問題  

SQL Always On 可用性が ADSync DB に対して構成されている場合に Azure AD Connect のアップグレードが失敗します。 この修正プログラムはこの問題に対処し、アップグレードが成功するようにします。 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>リリースの状態

2018 年 8 月 21 日:ダウンロードと自動アップグレード向けにリリース済み。 

### <a name="new-features-and-improvements"></a>新機能と機能強化

- Azure AD Connect の Ping Federate 統合が一般提供となりました。 [Azure AD と Ping Federate のフェデレーションについて詳しくは、こちらをご覧ください](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)。
- Azure AD Connect は、Azure AD 信頼のバックアップを更新のたびに AD FS に作成し、さらに、必要に応じて簡単に復元できるよう別個のファイルにそれを格納するようになりました。 [Azure AD Connect における Azure AD の信頼管理と新機能の詳細をご覧ください](https://aka.ms/fedtrustinaadconnect)。
- 通常の電子メール アドレスを変更したり、グローバル アドレス一覧に対してアカウントを非表示にしたりする際に発生した問題のトラブルシューティングを支援するトラブルシューティング ツールが導入されました。
- Azure AD Connect が更新されて、最新の SQL Server 2012 Native Client が追加されました。
- [ユーザー サインインの変更] タスクで、ユーザーのサインインをパスワード ハッシュ同期またはパススルー認証に切り替えたとき、[シームレスなシングル サインオン] チェック ボックスが既定で有効になります。
- Windows Server Essentials 2019 のサポートが追加されました
- Azure AD Connect Health エージェントが最新バージョンの 3.1.7.0 に更新されました
- アップグレード中、既定の同期規則に対する変更をインストーラーが検出した場合、変更された規則を上書きする前に、管理者に警告が表示されます。 ユーザーは是正措置を講じたうえで、後から再開することができます。 従来は、標準の規則に変更が加えられていた場合、それらの規則は、ユーザーへの警告なしで手動アップグレードによって上書きされ、同期スケジューラは、ユーザーへの通知なしで無効にされていました。 今後は、標準の同期規則に変更が加えられていた場合、それらを上書きする前にユーザーに警告が表示されます。 ユーザーはアップグレード処理を停止し、是正措置を講じたうえで後から再開することができます。
- FIPS への準拠に関する問題の処理能力が向上しました。FIPS に準拠している環境で MD5 ハッシュ生成のエラー メッセージが表示されるようになったほか、この問題の回避策を記載したドキュメントへのリンクが提供されます。
- ウィザードのフェデレーション関連タスクの改善を図るために UI が更新されています。これらのタスクは、フェデレーション用の独立したサブ グループに表示されます。 
- フェデレーション関連の追加タスクはすべて、使いやすいよう単一のサブメニューにグループ化されています。
- 新しく改良された ADSyncConfig Posh Module (AdSyncConfig.psm1) と新しい AD Permissions 機能が、以前の ADSyncPrep.psm1 (間もなく非推奨となる予定) から移動されました

### <a name="fixed-issues"></a>修正された問題 

- .NET 4.7.2 へのアップグレード後に AAD Connect サーバーで高い CPU 使用率が表示されるバグを修正しました
- 自動的に解決された SQL デッドロックの問題に関して、エラー メッセージが断続的に生成されるバグを修正しました
- Sync Rules Editor と Sync Service Manager のアクセシビリティに関するいくつかの問題を修正しました  
- Azure AD Connect がレジストリ設定情報を取得できないバグを修正しました
- ウィザードでユーザーが前後に移動する際の問題を引き起こしていたバグを修正しました
- ウィザード内でのマルチスレッド処理の誤りに起因したエラーの発生を防ぐためにバグを修正しました
- Group Sync Filtering ページでセキュリティ グループを解決する際に LDAP エラーが発生した場合、詳細な情報を忠実に含んだ例外が Azure AD Connect から返されるようになりました。  紹介例外の根本的な原因はまだ不明であり、別のバグで対処される予定です。
-  STK キーと NGC キー (WHfB のユーザー/デバイス オブジェクトの ms-DS-KeyCredentialLink 属性) のアクセス許可が正しく設定されないバグを修正しました。     
- "Set-ADSyncRestrictedPermissions" が正しく呼び出されないバグを修正しました
-  AADConnect のインストール ウィザードで、グループの書き戻しに対するアクセス許可を付与できるようになりました
- サインイン方法をパスワード ハッシュ同期から AD FS に変更してもパスワード ハッシュ同期が無効になりませんでした。
- AD FS の構成で IPv6 アドレスの検証を追加しました
- 既存の構成が存在することを知らせるための通知メッセージを更新しました。
- デバイス ライトバックで、信頼されていないフォレストのコンテナーを検出することはできません。 この点について、エラー メッセージと適切なドキュメントへのリンクを提供するように更新しました
- OU の選択を解除すると、その OU に対応する同期/書き戻しで一般的な同期エラーが発生します。 この点について、もっと理解しやすいエラー メッセージが表示されるように変更されています。

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>リリースの状態

2018 年 5 月 14 日:自動アップグレードとダウンロード向けにリリース済み。

### <a name="new-features-and-improvements"></a>新機能と機能強化

新機能と機能強化

- このリリースには、Azure AD Connect での PingFederate の統合のパブリック プレビューが含まれます。 このリリースでは、PingFederate をフェデレーション プロバイダーとして使用するように Azure Active Directory 環境を簡単かつ確実に構成できます。 この新しい機能を使用する方法の詳細については、こちらの[オンライン ドキュメント](plan-connect-user-signin.md#federation-with-pingfederate)を参照してください。 
- Azure AD Connect ウィザードのトラブルシューティング ユーティリティが更新され、分析されるエラー シナリオが増えました (リンクされたメールボックスや AD の動的グループなど)。 トラブルシューティング ユーティリティの詳細については、[こちら](tshoot-connect-objectsync.md)を参照してください。
- デバイスの書き戻しの構成は、Azure AD Connect ウィザード内でのみ管理されるようになりました。
- SQL 接続の問題とその他のさまざまなトラブルシューティング ユーティリティをトラブルシューティングするために使用できる ADSyncTools.psm1 という名前の新しい PowerShell モジュールが追加されました。 ADSyncTools モジュールの詳細については、[こちら](tshoot-connect-tshoot-sql-connectivity.md)を参照してください。 
- 新しいタスクである "デバイス オプションの構成" が追加されました。 このタスクを使用して、次の 2 つの操作を構成できます。 
  - **Hybrid Azure AD 参加**:環境にオンプレミスの AD フットプリントがあるときに、Azure Active Directory が提供する機能も活用したい場合は、ハイブリッド Azure AD 参加済みデバイスを実装できます。 これらのデバイスは、オンプレミスの Active Directory と Azure Active Directory の両方に参加しているデバイスです。
  - **デバイス ライトバック**:デバイス ライトバックを使うと、AD FS (2012 R2 以降) で保護されているデバイスへの、デバイスに基づく条件付きアクセスを有効にできます。

    >[!NOTE] 
    > - 同期カスタマイズ オプションからデバイスの書き戻しを有効にするオプションはグレー表示されます。 
    > -  このリリースでは、ADPrep の PowerShell モジュールは非推奨になりました。



### <a name="fixed-issues"></a>修正された問題 

- このリリースでは、SQL Server 2012 SP4 への SQL Server Express のインストールを更新します。とりわけ、複数のセキュリティの脆弱性の修正プログラムを提供します。  SQL Server 2012 SP4 の詳細については、[こちら](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information)を参照してください。
- 同期ルールの処理: 親同期規則が適用可能でなくなった場合、結合条件のない送信結合同期規則は適用されない
- 複数のアクセシビリティの修正が Synchronization Service Manager の UI と Sync Rules Editor に適用されている
- Azure AD Connect ウィザード:Azure AD Connect がワークグループに含まれている場合の AD Connect アカウント作成エラー
- Azure AD Connect ウィザード:AD ドメインと Azure AD の確認済みドメインが食い違う場合に常にAzure AD サインイン ページに検証チェックボックスが表示される
- 自動アップグレードの試行後、特定の状況で自動アップグレードの状態を正しく設定するように自動アップグレード PowerShell を修正
- Azure AD Connect ウィザード:前回見逃していた情報を取り込むようにテレメトリを更新
- Azure AD Connect ウィザード:AD FS からパススルー認証に切り替えるために**ユーザー サインインの変更**タスクを使用するときに、次の変更が加えられています。
    - ドメインをフェデレーションから管理対象に変換する前に、Azure AD Connect サーバーにパススルー認証エージェントがインストールされ、パススルー認証機能が有効になります。
    - ユーザーがフェデレーションから管理対象に変換されることはなくなります。 ドメインのみが変換されます。
- Azure AD Connect ウィザード:ユーザーの UPN に特殊文字がある場合、AD FS マルチドメイン Regex が正確ではない。特殊文字をサポートするように Regex を更新
- Azure AD Connect ウィザード:変更がない場合の偽の "ソース アンカー属性を構成します" メッセージを削除 
- Azure AD Connect ウィザード:AD FS でデュアル フェデレーション シナリオをサポート
- Azure AD Connect ウィザード:管理対象ドメインのフェデレーションへの変換時に追加されたドメインでは AD FS 要求は更新されない
- Azure AD Connect ウィザード:インストール済みのパッケージの検出中に古い Dirsync/Azure AD Sync/Azure AD Connect 関連製品が検出される。 今後、古い製品がアンインストールされるように修正
- Azure AD Connect ウィザード:パススルー認証エージェントのインストールが失敗した場合のエラー メッセージのマッピングを修正
- Azure AD Connect ウィザード:[ドメインと OU のフィルタリング] ページから "構成" コンテナーを削除
- 同期エンジンのインストール: 失敗することがある不要なレガシ ロジックを同期エンジンのインストール msi から削除
- Azure AD Connect ウィザード:パスワード ハッシュ同期の [オプション機能] ページのポップアップ ヘルプ テキストを修正
- 同期エンジン ランタイム:CS オブジェクトに削除がインポートされているときに同期規則がそのオブジェクトの再プロビジョニングを試行するシナリオを修正
- 同期エンジン ランタイム:インポート エラーのイベント ログにオンライン接続トラブルシューティング ガイドへのリンクを追加
- 同期エンジン ランタイム:コネクタの列挙時に同期スケジューラが使用するメモリの量を削減
- Azure AD Connect ウィザード:AD 読み取り特権がないカスタム同期サービス アカウントの解決問題を修正
- Azure AD Connect ウィザード:ドメインと OU の選択内容のログ記録を改善
- Azure AD Connect ウィザード:MFA シナリオ用に作成されたフェデレーションの信頼に AD FS の既定の要求を追加
- Azure AD Connect ウィザード:AD FS デプロイ WAP:新しい証明書を使用するサーバーの追加が失敗する
- Azure AD Connect ウィザード:onPremCredentials がドメインに対して初期化されていない場合に DSSO 例外が発生 
- アクティブなユーザー オブジェクトから AD の distinguishedName属性を優先的にフロー
- 最初の OOB の同期ルールの優先順位が 100 ではなく 99 に設定される表面的なバグを修正



## <a name="117510"></a>1.1.751.0
状態: 2018 年 4 月 12 日:ダウンロード用のみにリリース済み

>[!NOTE]
>このリリースは Azure AD Connect の修正プログラムです

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>修正された問題
中国のテナント用の Azure の自動インスタンス検出が失敗することがある問題が修正されました。  

### <a name="ad-fs-management"></a>AD FS の管理
#### <a name="fixed-issues"></a>修正された問題

構成再試行ロジックに問題があり、"同一のキーを含む項目が既に追加されています" を示す ArgumentException が発生していました。  これにより、再試行操作がすべて失敗します。

## <a name="117500"></a>1.1.750.0
状態: 2018 年 3 月 22 日:自動アップグレードとダウンロード向けにリリース済み。
>[!NOTE]
>この新しいバージョンへのアップグレードが完了すると、Azure AD コネクタの完全同期とフル インポート、および AD コネクタの完全同期が自動的にトリガーされます。 Azure AD Connect 環境のサイズによっては、これには時間がかかる場合があるため、これに対応できるように必要な手順を確実に実施していることを確認してください。また、好都合なタイミングが見つかるまでアップグレードを見合わせるようにしてください。

>[!NOTE]
>“1.1.524.0 より後にビルドをデプロイしたテナントの一部で、AutoUpgrade 機能が間違って無効化されました。 Azure AD Connect インスタンスで引き続き AutoUpgrade 機能が適用されるように、PowerShell コマンドレット "Set-ADSyncAutoUpgrade -AutoupGradeState Enabled" を実行してください。


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>修正された問題

* 自動アップグレードの状態が一時停止に設定されている場合に、Set-ADSyncAutoUpgrade コマンドレットによって Autoupgrade がブロックされます。 この機能は変更されました。将来のビルドでは AutoUpgrade はブロックされません。
* **ユーザー サインイン** ページの "パスワード同期" オプションが "パスワード ハッシュの同期" オプションに変更されました。  Azure AD Connect ではパスワードではなくパスワード ハッシュが同期されるため、この変更は実際の動作と一致しています。  詳細については、「[Azure AD Connect 同期を使用したパスワード ハッシュ同期の実装](how-to-connect-password-hash-synchronization.md)」を参照してください。

## <a name="117490"></a>1.1.749.0
状態:一部のお客様にリリース

>[!NOTE]
>この新しいバージョンへのアップグレードが完了すると、Azure AD コネクタの完全同期とフル インポート、および AD コネクタの完全同期が自動的にトリガーされます。 Azure AD Connect 環境のサイズによっては、これには時間がかかる場合があるため、これに対応できるように必要な手順を確実に実施していることを確認してください。また、好都合なタイミングが見つかるまでアップグレードを見合わせるようにしてください。

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>修正された問題
* 次のページに切り替えるときの、パーティション フィルター処理ページのバック グラウンド タスクにおけるタイミング ウィンドウの修正。

* ConfigDB カスタム アクションの実行中にアクセス違反を引き起こしていたバグを修正しました

* SQL 接続のタイムアウトから復旧できるようにバグを修正しました。

* SAN ワイルドカードを含む証明書で前提条件の確認に失敗するというバグを修正しました。

* Azure AD コネクタのエクスポート中に miiserver.exe がクラッシュするというバグを修正しました。

* Azure AD Connect ウィザードを実行しているときに DC に間違ったパスワードの試行が記録されると構成が変更されるというバグを修正しました。


#### <a name="new-features-and-improvements"></a>新機能と機能強化

* 一般データ保護規則 (GDPR) のプライバシー設定の追加。  詳しくは、[こちら](reference-connect-user-privacy.md)の記事をご覧ください。

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* アプリケーション テレメトリ - このデータ クラスのオン/オフは、管理者が任意に切り替えることができます

* Azure AD の正常性データ - 正常性の設定を制御するには、管理者が正常性ポータルにアクセスする必要があります。
   サービス ポリシーが変更された場合、エージェントがこれを読み取って強制します。

* デバイスの書き戻し構成アクションと、ページの初期化の進行状況バーを追加しました

* HTML レポートでの一般的な診断と、ZIP テキスト/HTML レポートの完全なデータ コレクションが向上しました

* 自動アップグレードの信頼性が向上しました。また、サーバーの正常性を確認できるように、テレメトリを追加しました

* AD コネクタ アカウントの特権アカウントで使用できるアクセス許可の制限

  * 新規インストールの場合、MSOL アカウントの作成後に特権アカウントが MSOL アカウントに対して持つアクセス許可がウィザードによって制限されます。

変更により、次に対応します。
1. 高速インストール
2. 自動作成アカウントでのカスタム インストール
3. Azure AD Connect のクリーン インストールで SA 特権が求められないようにインストーラーを変更しました

* 特定のオブジェクトの同期に関する問題のトラブルシューティングを行う新しいユーティリティを追加しました。 これは、Azure AD Connect ウィザードのトラブルシューティングの追加タスクにある "オブジェクトの同期のトラブルシューティング" オプションで使用できます。 現時点では、このユーティリティは以下を確認します。

  * 同期されたユーザー オブジェクトと Azure AD テナントのユーザー アカウントの間の UserPrincipalName の不一致。
  * ドメインのフィルター処理によって、オブジェクトが同期からフィルター処理されているかどうか
  * 組織単位 (OU) のフィルター処理によって、オブジェクトが同期からフィルター処理されているかどうか

* 特定のユーザー アカウントを対象に、オンプレミス Active Directory に格納されている現在のパスワード ハッシュを同期する新しいユーティリティを追加しました。

このユーティリティでは、パスワードの変更は必要ありません。 これは、Azure AD Connect ウィザードのトラブルシューティングの追加タスクにある "パスワード ハッシュの同期のトラブルシューティング" オプションで使用できます。






## <a name="116540"></a>1.1.654.0
状態:2017 年 12 月 12 日

>[!NOTE]
>このリリースは、Azure AD Connect のセキュリティに関連する修正プログラムです

### <a name="azure-ad-connect"></a>Azure AD Connect
Azure AD Connect バージョン 1.1.654.0 (以降) が強化され、Azure AD Connect が AD DS アカウントを作成するときに「[AD DS アカウントへのアクセスのロックダウン](#lock)」のセクションで説明されている推奨されるアクセス許可の変更が自動的に適用されます。 

- Azure AD Connect をセットアップするときにインストールを実行する管理者は、既存の AD DS アカウントを指定するか、Azure AD Connect でアカウントを自動的に作成できます。 アクセス許可の変更は、セットアップ中に Azure AD Connect によって作成される AD DS アカウントに自動的に適用されます。 インストールを実行する管理者が指定した既存の AD DS アカウントには適用されません。
- 以前のバージョンの Azure AD Connect から 1.1.654.0 (またはそれ以降) にアップグレードしたお客様の場合、アクセス許可の変更は、アップグレードの前に作成された既存の AD DS アカウントにさかのぼって適用されません。 アップグレード後に作成された新しい AD DS アカウントにのみ適用されます。 これは、Azure AD に同期する新しい AD フォレストを追加するときに行われます。

>[!NOTE]
>このリリースでは、サービス アカウントがインストール プロセスによって作成される Azure AD Connect の新規インストールに対してのみ脆弱性が除去されます。 既存のインストールの場合、または自分でアカウントを指定する場合は、この脆弱性が存在しないことを確認する必要があります。

#### <a name="lock"></a> AD DS アカウントへのアクセスのロックダウン
オンプレミスの AD で次のアクセス許可の変更を実装して、AD DS アカウントへのアクセスをロックダウンします。  

*   指定したオブジェクトの継承を無効にします
*   SELF に固有の ACE を除き、特定のオブジェクトのすべての ACE を削除します。 SELF については、既定のアクセス許可を維持します。
*   以下の特定のアクセス許可を割り当てます。

Type     | EnableAdfsAuthentication                          | Access               | 適用対象
---------|-------------------------------|----------------------|--------------|
ALLOW    | SYSTEM                        | フル コントロール         | このオブジェクト  |
ALLOW    | Enterprise Admins             | フル コントロール         | このオブジェクト  |
ALLOW    | Domain Admins                 | フル コントロール         | このオブジェクト  |
ALLOW    | 管理者                | フル コントロール         | このオブジェクト  |
ALLOW    | Enterprise Domain Controllers | コンテンツの一覧        | このオブジェクト  |
ALLOW    | Enterprise Domain Controllers | すべてのプロパティの読み取り  | このオブジェクト  |
ALLOW    | Enterprise Domain Controllers | 読み取りのアクセス許可     | このオブジェクト  |
ALLOW    | Authenticated Users           | コンテンツの一覧        | このオブジェクト  |
ALLOW    | Authenticated Users           | すべてのプロパティの読み取り  | このオブジェクト  |
ALLOW    | Authenticated Users           | 読み取りのアクセス許可     | このオブジェクト  |

AD DS アカウントの設定を強化するために、[この PowerShell スクリプト](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978)を実行できます。 PowerShell スクリプトでは、AD DS アカウントに上記のアクセス許可が割り当てられます。

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>既存のサービス アカウントを強化する PowerShell スクリプト

PowerShell スクリプトを使用して、(組織で指定した、または Azure AD Connect の以前のインストールによって作成された) 既存の AD DS アカウントにこれらの設定を適用するには、上記のリンクからスクリプトをダウンロードしてください。

##### <a name="usage"></a>用途:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Where 

**$ObjectDN** = アクセス許可のセキュリティを強化する必要がある Active Directory アカウント。

**$Credential** = $ObjectDN アカウントに対するアクセス許可を制限するために必要な権限を持つ管理者資格情報。 通常、これらの権限はエンタープライズ管理者またはドメイン管理者が持っています。 アカウント参照の失敗を回避するには、管理者アカウントの完全修飾ドメイン名を使用します。 例: contoso.com\admin。

>[!NOTE] 
>$credential.UserName は FQDN\username 形式である必要があります。 例: contoso.com\admin 

##### <a name="example"></a>例:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>この脆弱性が未承認のアクセスに使用されたか

この脆弱性が Azure AD Connect 構成の侵害に使用されたかどうかを確認するには、サービス アカウントのパスワードが最後にリセットされた日付を確認する必要があります。  予期しないタイムスタンプがある場合は、イベント ログでそのパスワードのリセット イベントをさらに調査する必要があります。

詳しくは、[マイクロソフト セキュリティ アドバイザリ 4056318](https://technet.microsoft.com/library/security/4056318) をご覧ください

## <a name="116490"></a>1.1.649.0
状態:2017 年 10 月 27 日

>[!NOTE]
>このビルドは、Azure AD Connect の自動アップグレード機能では提供されません。

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>修正された問題
* Azure AD Connect と Azure AD Connect Health エージェント (同期用) 間の、バージョンの互換性に関する問題が修正されました。 この問題は、Azure AD Connect のバージョン 1.1.647.0 へのインプレース アップグレードを実行していて、現在の Health Agent のバージョンが 3.0.127.0 である場合に影響があります。 アップグレード後に、Health Agent は Azure AD Connect 同期サービスについての正常性データを Azure AD ヘルス サービスに送信できなくなります。 この修正により、Azure AD Connect のインプレース アップグレード中に Health Agent バージョン 3.0.129.0 がインストールされます。 Health Agent バージョン 3.0.129.0 には、Azure AD Connect バージョン 1.1.649.0 との互換性の問題はありません。


## <a name="116470"></a>1.1.647.0
状態:2017 年 10 月 19 日

> [!IMPORTANT]
> Azure AD Connect バージョン 1.1.647.0 と Azure AD Connect Health エージェント (同期用) バージョン 3.0.127.0 間で、互換性に関する既知の問題があります。 この問題によって、Health エージェントは、Azure AD Connect 同期サービスに関する正常性データ (オブジェクト同期エラーと実行履歴データを含む) を Azure AD Health サービスに送信できません。 Azure AD Connect のデプロイをバージョン 1.1.647.0 に手動でアップグレードする前に、Azure AD Connect サーバーにインストールされている Azure AD Connect Health エージェントの現在のバージョンを確認してください。 これを実行するには、 *[コントロール パネル]→[プログラムの追加と削除]* の順に選択し、 *[Microsoft Azure AD Connect Health Agent for Sync]\(同期用 Microsoft Azure AD Connect Health エージェント\)* のアプリケーションを探します。このバージョンが 3.0.127.0 の場合は、Azure AD Connect の次のバージョンが利用可能になってからアップグレードすることをお勧めします。 Health エージェントのバージョンが 3.0.127.0 でない場合は、手動でインプレース アップグレードを続行できます。 この問題は、スウィング アップデートや、Azure AD Connect の新しいインストールを実行しているお客様には影響しません。
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>修正された問題
* Azure AD Connect ウィザードの *[ユーザー サインインの変更]* タスクの問題を修正しました。

  * この問題は、Azure AD Connect の既存のデプロイでパスワード同期が**有効**になっており、ユーザーのサインイン方法を*パススルー認証*に設定しようとするときに発生します。 変更が適用される前に、ウィザードに "*パスワード同期を無効にする*" という内容のメッセージが間違って表示されます。 ただし、変更が適用された後もパスワード同期は引き続き有効です。 この修正により、今後はウィザードでこのメッセージが表示されることはありません。

  * 仕様上、お客様が *[ユーザー サインインの変更]* タスクを使用してユーザーのサインイン方法を更新するときに、ウィザードによってパスワード同期が無効化されることはありません。 これは、ユーザーの主要なサインイン方法としてパススルー認証またはフェデレーションを有効にする場合でも、パスワード同期を維持する必要があるお客様への中断を避けるためです。
  
  * ユーザーのサインイン方法を更新した後にパスワード同期を無効にする場合は、ウィザードで *[Customize Synchronization Configuration]\(同期構成のカスタマイズ\)* タスクを実行する必要があります。 *[Optional features]\(オプション機能\)* ページに移動して、 *[パスワード同期]* オプションをオフにします。
  
  * シームレス シングル サインオンを有効または無効にしようとする場合にも、同じ問題が発生することに注意してください。 特に、Azure AD Connect の既存のデプロイでパスワード同期が有効になっており、ユーザーのサインイン方法が既に*パススルー認証*に構成されている場合です。 ユーザーのサインイン方法が "パススルー認証" に構成されたままで、 *[ユーザー サインインの変更]* タスクを使用して、 *[Enable Seamless Single Sign-On]\(シームレス シングル サインオンを有効にする\)* オプションをオンまたはオフにしようとすると、 変更が適用される前に、ウィザードに "*パスワード同期を無効にする*" という内容のメッセージが間違って表示されます。 ただし、変更が適用された後もパスワード同期は引き続き有効です。 この修正により、今後はウィザードでこのメッセージが表示されることはありません。

* Azure AD Connect ウィザードの *[ユーザー サインインの変更]* タスクの問題を修正しました。

  * この問題は、Azure AD Connect の既存のデプロイでパスワード同期が**無効**になっており、ユーザーのサインイン方法を*パススルー認証*に設定しようとしているときに発生します。 変更が適用されるときに、ウィザードによってパススルー認証とパスワード同期の両方が有効になります。 この修正により、ウィザードでパスワード同期が有効化されなくなりました。

  * 以前は、パスワード同期は、パススルー認証を有効にするための前提条件でした。 ユーザーのサインイン方法を*パススルー認証*に設定すると、ウィザードによってパススルー認証とパスワード同期の両方が有効化されていました。 最近、パスワード同期が前提条件から削除されました。 Azure AD Connect バージョン 1.1.557.0 の一環として、ユーザーのサインイン方法が*パススルー認証*に設定される場合に、パスワード同期を有効にしない変更が Azure AD Connect に対して行われました。 ただし、この変更は Azure AD Connect のインストールにのみ適用されていました。 この修正により、同じ変更が *[ユーザー サインインの変更]* タスクにも適用されます。
  
  * シームレス シングル サインオンを有効または無効にしようとする場合にも、同じ問題が発生することに注意してください。 特に、Azure AD Connect の既存のデプロイでパスワード同期が無効になっており、ユーザーのサインイン方法が既に*パススルー認証*に構成されている場合です。 ユーザーのサインイン方法が "パススルー認証" に構成されたままで、 *[ユーザー サインインの変更]* タスクを使用して、 *[Enable Seamless Single Sign-On]\(シームレス シングル サインオンを有効にする\)* オプションをオンまたはオフにしようとすると、 変更が適用されるときに、ウィザードによってパスワード同期が有効になります。 この修正により、ウィザードでパスワード同期が有効化されなくなりました。 

* Azure AD Connect のアップグレードが失敗して "*Unable to upgrade the Synchronization Service*"(同期サービスをアップグレードできません) というエラーが表示される問題を修正しました。 また、今後は同期サービスの起動時にイベント エラー "*The service was unable to start because the version of the database is newer than the version of the binaries installed*"(データベースのバージョンが、インストールされているバイナリのバージョンよりも新しいため、サービスを開始できませんでした) が表示されることはありません。 この問題は、アップグレードを実行している管理者が、Azure AD Connect で使用されている SQL サーバーに対して sysadmin 権限を持っていない場合に発生します。 この修正により、Azure AD Connect ではアップグレード時に、管理者が ADSync データベースに対する db_owner 権限を持っていることのみが求められます。

* [シームレス シングル サインオン](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)を有効にしているお客様に影響を与える、Azure AD Connect のアップグレードの問題を修正しました。 Azure AD Connect をアップグレードした後に、シームレス シングル サインオンが引き続き有効で完全に機能するにもかかわらず、Azure AD Connect ウィザードに "無効" と間違って表示されます。 この修正により、この機能がウィザードで正しく "有効" と表示されるようになりました。

* Azure AD Connect ウィザードで、ソース アンカーに関連する変更が行われていない場合にも、 *[構成の準備完了]* ページに "*ソース アンカーの構成*" メッセージが常に表示される問題を修正しました。

* Azure AD Connect のインプレース アップグレードを手動で実行する際、お客様には、対応する Azure AD テナントのグローバル管理者の資格情報を入力することが求められます。 以前は、入力されたグローバル管理者の資格情報が別の Azure AD テナントに属している場合でも、アップグレードを続行できました。 アップグレードが正常に完了したように見えても、特定の構成はアップグレードで正しく保持されません。 この変更により、入力された資格情報が該当する Azure AD テナントに一致しない場合、ウィザードでアップグレードを続行できません。

* 手動でのアップグレードの開始時に Azure AD Connect Health サービスを不必要に再起動させる冗長なロジックを削除しました。


#### <a name="new-features-and-improvements"></a>新機能と機能強化
* Microsoft Germany Cloud で Azure AD Connect を設定するために必要な手順を簡略化するロジックを追加しました。 以前は、この記事で説明しているように、Microsoft Germany Cloud で正しく機能するためには、Azure AD Connect サーバー上の特定のレジストリ キーを更新する必要がありました。 今後は、セットアップ時に入力されたグローバル管理者の資格情報に基づいて、Microsoft Germany Cloud 内に存在するテナントかどうかが Azure AD Connect で自動的に検出されます。

### <a name="azure-ad-connect-sync"></a>Azure AD Connect 同期
> [!NOTE]
> 注:同期サービスには、独自のカスタム スケジューラを作成できる WMI インターフェイスがあります。 このインターフェイスは現在非推奨であり、2018 年 6 月 30 日以降にリリースされる Azure AD Connect の将来のバージョンから削除される予定です。 同期スケジュールをカスタマイズしようとする顧客は、組み込みスケジューラ (https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler) を使用する必要があります。

#### <a name="fixed-issues"></a>修正された問題
* Azure AD Connect ウィザードで、オンプレミス Active Directory からの変更を同期するために必要な AD Connector アカウントを作成するときに、PublicFolder オブジェクトの読み取りに必要なアクセス許可がアカウントに正しく割り当てられません。 この問題は、高速インストールとカスタム インストールの両方に影響します。 今回の変更により、この問題が修正されました。

* Windows Server 2016 から実行している管理者に、Azure AD Connect ウィザードのトラブルシューティング ページが正しく表示されない問題を修正しました。

#### <a name="new-features-and-improvements"></a>新機能と機能強化
* Azure AD Connect ウィザードのトラブルシューティング ページを使用してパスワード同期をトラブルシューティングすると、トラブルシューティング ページによって、ドメイン固有の状態が返されるようになりました。

* 以前は、パスワード ハッシュ同期を有効にしようとする際に、オンプレミス AD からのパスワード ハッシュを同期するために必要なアクセス許可が AD Connector アカウントにあるかどうかが、Azure AD Connect で検証されませんでした。 今後は、Azure AD Connect ウィザードが検証を行い、AD Connector アカウントに適切なアクセス許可がない場合は警告メッセージが表示されます。

### <a name="ad-fs-management"></a>AD FS の管理
#### <a name="fixed-issue"></a>修正された問題
* [ソース アンカーとしての ms-DS-ConsistencyGuid](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) 機能の使用に関連する問題を修正しました。 この問題は、ユーザーのサインイン方法として *AD FS とのフェデレーション*を構成しているお客様に影響します。 ウィザードで *[ソース アンカーの構成]* タスクを実行すると、Azure AD Connect では、immutableId のソース属性として *ms-DS-ConsistencyGuid を使用するように切り替わります。 この変更の一環として、Azure AD Connect は、AD FS で ImmutableId の要求規則を更新しようとします。 ただし、Azure AD Connect には AD FS の構成に必要な管理者の資格情報がないため、このステップは失敗していました。 この修正により、 *[ソース アンカーの構成]* タスクを実行すると、AD FS の管理者の資格情報を入力するように求めるメッセージが Azure AD Connect に表示されるようになります。



## <a name="116140"></a>1.1.614.0
状態:2017 年 9 月 5 日

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>既知の問題
* Azure AD Connect のアップグレードが失敗して "*Unable to upgrade the Synchronization Service*"(同期サービスをアップグレードできません) というエラーが表示される既知の問題があります。 また、今後は同期サービスの起動時にイベント エラー "*The service was unable to start because the version of the database is newer than the version of the binaries installed*"(データベースのバージョンが、インストールされているバイナリのバージョンよりも新しいため、サービスを開始できませんでした) が表示されることはありません。 この問題は、アップグレードを実行している管理者が、Azure AD Connect で使用されている SQL サーバーに対して sysadmin 権限を持っていない場合に発生します。 dbo アクセス許可では不十分です。

* [シームレス シングル サインオン](how-to-connect-sso.md)を有効にしているお客様に影響する、Azure AD Connect のアップグレードに関する既知の問題があります。 Azure AD Connect をアップグレードすると、機能は引き続き有効であるにもかかわらず、ウィザードには無効と表示されます。 この問題は、今後のリリースで修正される予定です。 この表示の問題が気になるお客様は、ウィザードでシームレス シングル サインオンを有効にすることで、問題を手動で修正できます。

#### <a name="fixed-issues"></a>修正された問題
* [ソース アンカーとしての ms-DS-ConsistencyGuid](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) 機能を有効する際に Azure AD Connect でオンプレミスの AD FS の要求規則を更新できない問題を修正しました。 この問題は、Azure AD Connect の既存のデプロイでサインインの方法として AD FS が構成されている場合に、上記機能を有効にしようとすると発生します。 この問題は、ウィザードで AD FS の要求規則の更新に先立ち ADFS の資格情報の入力を求めるプロンプトを表示していなかったことによるものです。
* オンプレミスの AD フォレストで NTLM が無効になっている場合に Azure AD Connect のインストールが失敗する問題を修正しました。 この問題は、Kerberos 認証に必要なセキュリティ コンテキストを作成するときに、Azure AD Connect ウィザードが完全に修飾された資格情報を提供しないことによるものです。 それが原因で Kerberos 認証が失敗し、Azure AD Connect ウィザードは NTLM の使用に戻ります。

### <a name="azure-ad-connect-sync"></a>Azure AD Connect 同期
#### <a name="fixed-issues"></a>修正された問題
* Tag 属性が指定されていないと新しい同期ルールを作成できない問題を修正しました。
* Kerberos を使用できる場合でも、Azure AD Connect がオンプレミスの AD に接続して NTLM を使用したパスワード同期を行う問題を修正しました。 この問題は、オンプレミス AD トポロジに、バックアップから復元された 1 つまたは複数のドメイン コントローラーが存在する場合に発生します。
* アップグレード後に完全な同期手順が不必要に発生する問題を修正しました。 一般に、アップグレード後の完全な同期手順は、既定の同期ルールが変更されている場合に実行する必要があります。 この問題は、同期ルール式に改行文字が出現したときに変更が間違って検出される変更検出ロジックのエラーが原因でした。 改行文字は、読みやすさを向上させるために同期ルール式に挿入されます。
* Azure AD Connect サーバーが自動アップグレード後に正常に動作しない可能性がある問題を修正しました。 この問題は、Azure AD Connect サーバーのバージョン 1.1.443.0 (またはそれ以前) に影響を与えます。 この問題の詳細については、「[Azure AD Connect is not working correctly after an automatic upgrade](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade)」(Azure AD Connect が自動アップグレード後に正常に動作しない) を参照してください。
* エラーが発生したときに、自動アップグレードが 5 分間隔で再試行される可能性がある問題を修正しました。 修正により、エラー発生時の自動アップグレードの再試行は、指数バックオフで行われます。
* パスワード同期イベント 611 がWindows アプリケーション イベント ログに**エラー**ではなく**情報**と間違って表示される問題を修正しました。 イベント 611 は、パスワード同期で問題が発生するたびに生成されます。 
* Azure AD Connect ウィザードで、グループの書き戻し機能が、グループを書き戻すために必要な OU を選択しなくても有効化できる問題を修正しました。

#### <a name="new-features-and-improvements"></a>新機能と機能強化
* Azure AD Connect ウィザードの追加タスクに、トラブルシューティング タスクを追加しました。 このタスクを活用して、パスワード同期に関連する問題のトラブルシューティングと一般的な診断の収集を実行できます。 今後、トラブルシューティング タスクは、他のディレクトリの同期に関連する問題を含むように拡張されます。
* Azure AD Connect で、 **[既存のデータベースを使用する]** という名前の新しいインストール モードをサポートするようになりました。 このインストール モードを使用すると、既存の ADSync データベースを指定する Azure AD Connect をインストールできます。 この機能の詳細については、[既存のデータベースの使用](how-to-connect-install-existing-database.md)に関する記事を参照してください。
* セキュリティを強化するため、Azure AD Connect は、ディレクトリを同期する際に、既定で TLS1.2 を使用して Azure AD に接続するようになりました。 以前の既定値は TLS1.0 でした。
* Azure AD Connect のパスワード同期エージェントは、起動時に Azure AD の既知のエンドポイントに接続してパスワード同期を試行します。 接続が成功すると、リージョン固有のエンドポイントにリダイレクトされます。 以前は、パスワード同期エージェントは、リージョン固有のエンドポイントが再起動するまで、それをキャッシュしていました。 現在、エージェントは、リージョン固有のエンドポイントで接続問題が発生した場合は、キャッシュをクリアし、既知のエンドポイントで再試行します。 この変更により、キャッシュされたリージョン固有のエンドポイントが使用できなった場合に、パスワード同期を別のリージョン固有のエンドポイントに確実にフェールオーバーできます。
* オンプレミスの AD フォレストの変更を同期するには、AD DS アカウントが必要です。 (i) AD DS アカウントを自分で作成してその資格情報を Azure AD Connect に提供するか、(ii) エンタープライズ管理者の資格情報を指定して Azure AD Connect に AD DS アカウントの作成を任せることができます。 以前は、(i) が Azure AD Connect ウィザードの既定のオプションでした。 現在は、(ii) が既定のオプションです。

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>新機能と機能強化
* Microsoft Azure Government Cloud と Microsoft Cloud Germany のサポートを追加しました。

### <a name="ad-fs-management"></a>AD FS の管理
#### <a name="fixed-issues"></a>修正された問題
* AD prep powershell モジュールの Initialize-ADSyncNGCKeysWriteBack コマンドレットが、ACL をデバイス登録コンテナーに間違って適用し、そのために既存のアクセス許可のみが継承されていました。  これが、同期サービス アカウントが適切なアクセス許可を持つように更新されました。

#### <a name="new-features-and-improvements"></a>新機能と機能強化
* AAD Connect の ADFS ログイン確認タスクが、ADFS からのトークンの取得だけではなく、Microsoft Online に対するログインも確認するように更新されました。
* AAD Connect を使用した新しい ADFS ファームの設定時に表示される ADFS の資格情報を求めるページが移動され、ユーザーに ADFS サーバーと WAP サーバーの指定を求める前に表示されるようになりました。  これにより、AAD Connect は、指定されたアカウントに適切なアクセス許可があることを確認できます。
* AAD Connect のアップグレード中に ADFS AAD Trust の更新に失敗しても、アップグレードが失敗することはありません。  これが発生した場合は、適切な警告メッセージが表示され、ユーザーは、AAD Connect の追加タスクを使用して信頼をリセットする操作に進む必要があります。

### <a name="seamless-single-sign-on"></a>シームレス シングル サインオン
#### <a name="fixed-issues"></a>修正された問題
* [シームレス シングル サインオン](how-to-connect-sso.md)の有効化が試行されたときに Azure AD Connect ウィザードがエラーを返す問題を修正しました。 エラー メッセージは、"*Configuration of Microsoft Azure AD Connect Authentication Agent failed. (Microsoft Azure AD Connect の認証エージェントを構成できませんでした)* " です。 この問題は、[パススルー認証](how-to-connect-sso.md)用の認証エージェントのプレビュー バージョンを[こちらの記事](how-to-connect-pta-upgrade-preview-authentication-agents.md)に記載されている手順に基づいて手動でアップグレードしたお客様に影響します。


## <a name="115610"></a>1.1.561.0
状態:2017 年 7 月 23 日

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>修正された問題

* 既定の同期規則 “AD への送信 – ユーザー ImmutableId” の削除を招く問題を修正しました。

  * この問題は、Azure AD Connect をアップグレードする際か、Azure AD Connect ウィザード内のタスク オプション *[Update Synchronization Configuration] \(同期構成の更新)* を使用して Azure AD Connect 同期構成を更新する際に発生します。
  
  * この同期規則は、[ソース アンカーとしての ms-DS-ConsistencyGuid 機能](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)を有効にしているお客様に適用されます。 この機能は、バージョン 1.1.524.0 以降で導入されました。 この同期規則が削除されると、Azure AD Connect でオンプレミスの AD ms-DS-ConsistencyGuid 属性に ObjectGuid 属性値を設定することができなくなります。 これによって新しいユーザーが Azure AD にプロビジョニングされなくなることはありません。
  
  * この修正により、上記の機能が有効になっていれば、アップグレード中や構成の変更中にこの同期規則が削除されなくなります。 この問題による影響を受けている既存のお客様の場合、この修正により、このバージョンの Azure AD Connect へのアップグレード後に、この同期規則がもう一度追加されるようにもなります。

* 既定の同期規則の優先順位値が 100 未満になる原因の問題を修正しました。

  * 一般に、カスタム同期規則のために優先順位値 0 から 99 までが予約されています。 アップグレード中に、既定の同期規則の優先順位値は、同期規則の変更に対応して更新されます。 この問題のために、既定の同期規則に 100 未満の優先順位値が割り当てられる可能性があります。
  
  * この修正により、アップグレード中にこの問題が発生しなくなります。 しかし、この問題の影響を受けている既存のお客様の優先順位値は復元されません。 将来、復元に役立つ別個の修正が提供される予定です。

* Azure AD Connect ウィザード内の [[ドメインと OU のフィルター処理] 画面](how-to-connect-install-custom.md#domain-and-ou-filtering)で、OU ベースのフィルター処理が有効になっている場合でも、 *[すべてのドメインと OU の同期]* オプションが選択済みとして表示される問題を修正しました。

*   Synchronization Service Manager の[[ディレクトリ パーティションの構成] 画面](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)で *[更新]* ボタンをクリックするとエラーが返される原因となる問題を修正しました。 エラー メッセージは *“An error was encountered while refreshing domains:Unable to cast object of type ‘System.Collections.ArrayList’ to type ‘Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject.”* (ドメインの更新中にエラーが発生しました。型 ‘System.Collections.ArrayList’ のオブジェクトを型 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject' にキャストできません。) です。 このエラーは、新しい AD ドメインが既存の AD フォレストに追加されている場合に、[更新] ボタンを使用して Azure AD Connect を更新しようとすると発生します。

#### <a name="new-features-and-improvements"></a>新機能と機能強化

* [自動アップグレード機能](how-to-connect-install-automatic-upgrade.md)が、次のような構成のお客様をサポートするように拡張されています。
  * デバイスの書き戻し機能を有効にしました。
  * グループの書き戻し機能を有効にしました。
  * インストールが簡単設定でも DirSync のアップグレードでもありません。
  * メタバース内のオブジェクトが 100,000 を超えています。
  * 現在、複数のフォレストに接続しています。 高速セットアップで接続するフォレストは 1 つのみです。
  * AD Connector アカウントは、既定の MSOL_ アカウントではなくなりました。
  * サーバーがステージング モードに設定されています。
  * ユーザーの書き戻し機能を有効にしました。
  
  >[!NOTE]
  >自動アップグレード機能の範囲の拡大は、Azure AD Connect ビルド 1.1.105.0 以降のお客様に影響します。 Azure AD Connect サーバーが自動的にアップグレードされないようにするには、Azure AD Connect サーバーで `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` コマンドレットを実行する必要があります。 自動アップグレードの有効化/無効化の詳細については、「[Azure AD Connect:自動アップグレード](how-to-connect-install-automatic-upgrade.md)」を参照してください。

## <a name="115580"></a>1.1.558.0
状態:リリース予定なし。 このビルドの変更は、バージョン 1.1.561.0 に組み込まれています。

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>修正された問題

* OU ベースのフィルター処理構成の更新時に、既定の同期規則 “AD への送信 – ユーザー ImmutableId” が削除を招く問題を修正しました。 この同期規則は、[ソース アンカーとしての ms-DS-ConsistencyGuid 機能](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)にとって必要です。

* Azure AD Connect ウィザード内の [[ドメインと OU のフィルター処理] 画面](how-to-connect-install-custom.md#domain-and-ou-filtering)で、OU ベースのフィルター処理が有効になっている場合でも、 *[すべてのドメインと OU の同期]* オプションが選択済みとして表示される問題を修正しました。

*   Synchronization Service Manager の[[ディレクトリ パーティションの構成] 画面](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)で *[更新]* ボタンをクリックするとエラーが返される原因となる問題を修正しました。 エラー メッセージは *“An error was encountered while refreshing domains:Unable to cast object of type ‘System.Collections.ArrayList’ to type ‘Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject.”* (ドメインの更新中にエラーが発生しました。型 ‘System.Collections.ArrayList’ のオブジェクトを型 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject' にキャストできません。) です。 このエラーは、新しい AD ドメインが既存の AD フォレストに追加されている場合に、[更新] ボタンを使用して Azure AD Connect を更新しようとすると発生します。

#### <a name="new-features-and-improvements"></a>新機能と機能強化

* [自動アップグレード機能](how-to-connect-install-automatic-upgrade.md)が、次のような構成のお客様をサポートするように拡張されています。
  * デバイスの書き戻し機能を有効にしました。
  * グループの書き戻し機能を有効にしました。
  * インストールが簡単設定でも DirSync のアップグレードでもありません。
  * メタバース内のオブジェクトが 100,000 を超えています。
  * 現在、複数のフォレストに接続しています。 高速セットアップで接続するフォレストは 1 つのみです。
  * AD Connector アカウントは、既定の MSOL_ アカウントではなくなりました。
  * サーバーがステージング モードに設定されています。
  * ユーザーの書き戻し機能を有効にしました。
  
  >[!NOTE]
  >自動アップグレード機能の範囲の拡大は、Azure AD Connect ビルド 1.1.105.0 以降のお客様に影響します。 Azure AD Connect サーバーが自動的にアップグレードされないようにするには、Azure AD Connect サーバーで `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` コマンドレットを実行する必要があります。 自動アップグレードの有効化/無効化の詳細については、「[Azure AD Connect:自動アップグレード](how-to-connect-install-automatic-upgrade.md)」を参照してください。

## <a name="115570"></a>1.1.557.0
状態:2017 年 7 月

>[!NOTE]
>このビルドは、Azure AD Connect の自動アップグレード機能では提供されません。

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>修正された問題
* Initialize-ADSyncDomainJoinedComputerSync コマンドレットによって、既存のサービス接続ポイント オブジェクトで構成されている確認済みドメインが、有効なドメインであるにもかかわらず、変更される問題を修正しました。 この問題は、サービス接続ポイントの構成に使用できる確認済みドメインが、Azure AD テナントに複数ある場合に発生します。

#### <a name="new-features-and-improvements"></a>新機能と機能強化
* Microsoft Azure Government クラウドと Microsoft Cloud Germany のプレビューで、パスワード ライトバックを利用できるようになりました。 さまざまなサービス インスタンスの Azure AD Connect サポートの詳細については、「[Azure AD Connect:インスタンスに関する特別な考慮事項](reference-connect-instances.md)」を参照してください。

* Initialize-ADSyncDomainJoinedComputerSync コマンドレットで、AzureADDomain という新しい省略可能なパラメーターを利用できるようになりました。 このパラメーターを使用すると、サービス接続ポイントの構成に使用する確認済みドメインを指定できます。

### <a name="pass-through-authentication"></a>パススルー認証

#### <a name="new-features-and-improvements"></a>新機能と機能強化
* パススルー認証に必要なエージェントの名前が、"*Microsoft Azure AD アプリケーション プロキシ コネクタ*" から "*Microsoft Azure AD Connect 認証エージェント*" に変更されました。

* パススルー認証を有効にしても、既定では、パスワード ハッシュ同期は有効化されなくなりました。


## <a name="115530"></a>1.1.553.0
状態:2017 年 6 月

> [!IMPORTANT]
> このビルドでは、スキーマと同期規則に変更が加えられています。 アップグレードの後、フル インポートの手順と完全同期の手順が Azure AD Connect 同期サービスによってトリガーされます。 変更の詳細は以下で説明しています。 アップグレード後、フル インポートと完全な同期の手順を一時的に保留にするには、「[How to defer full synchronization after upgrade (アップグレード後に完全な同期を保留にする方法)](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)」を参照してください。
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect 同期

#### <a name="known-issue"></a>既知の問題
* Azure AD Connect 同期で [OU ベースのフィルター処理](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)を使用している顧客に影響する問題があります。Azure AD Connect ウィザードで [[ドメインと OU のフィルタリング] ページ](how-to-connect-install-custom.md#domain-and-ou-filtering)に移動すると、通常は、次のように動作します。
  * OU ベースのフィルター処理が有効になっている場合は、 **[選択したドメインと OU の同期]** オプションが選択されます。
  * それ以外の場合は、 **[すべてのドメインと OU の同期]** オプションが選択されます。

問題は、ウィザードを実行したときに、常に **[すべてのドメインと OU の同期]** が選択されることです。  この問題は、OU ベースのフィルター処理が構成されている場合でも発生します。 AAD Connect 構成の変更を保存する前に、 **[選択したドメインと OU の同期] オプションを必ず選択**し、同期する必要があるすべての OU が有効になっていることをもう一度確認します。 これを行わないと、OU ベースのフィルター処理は無効になります。

#### <a name="fixed-issues"></a>修正された問題

* パスワード ライトバックによって Azure AD 管理者がオンプレミスの AD 特権ユーザー アカウントのパスワードをリセットできる、という問題を修正しました。 この問題は、特権アカウントに対するパスワードのリセット アクセス許可が、Azure AD Connect に付与されている場合に発生します。 このバージョンの Azure AD Connect で問題を解決するには、オンプレミスの AD 特権ユーザー アカウントの所有者でない Azure AD 管理者が、任意の AD 特権ユーザー アカウントのパスワードをリセットできないようにします。 詳しくは、[セキュリティ アドバイザリ 4033453](https://technet.microsoft.com/library/security/4033453) を参照してください。

* Azure AD Connect がオンプレミスの AD ms-DS-ConsistencyGuid 属性へのライトバックを行わないという、[ソース アンカーとしての ms-DS-ConsistencyGuid](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) 機能に関連する問題を修正しました。 この問題は、オンプレミスの AD フォレストに複数の Azure AD Connect が追加され、" *[複数のディレクトリにユーザー ID が存在します] オプション*" が選択されているときに発生します。 このような構成が使用されている場合、結果の同期ルールでは、メタバースの sourceAnchorBinary 属性が設定されません。 sourceAnchorBinary 属性は、ms-DS-ConsistencyGuid 属性のソース属性として使用されます。 このため、ms-DSConsistencyGuid 属性へのライトバックが行われません。 この問題を修正するために、メタバースの sourceAnchorBinary 属性が常に設定されるように、次の同期規則が更新されました。
  * AD からの受信 - InetOrgPerson AccountEnabled.xml
  * AD からの受信 - InetOrgPerson Common.xml
  * AD からの受信 - ユーザー AccountEnabled.xml
  * AD からの受信 - ユーザー Common.xml
  * AD からの受信 - ユーザー結合 SOAInAAD.xml

* [ソース アンカーとしての ms-DS-ConsistencyGuid](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) 機能が有効になっていなくても、"AD への送信 – ユーザー ImmutableId" 同期規則は引き続き Azure AD Connect に追加されたままです。 これが原因で問題が発生することはなく、ms-DS-ConsistencyGuid 属性のライトバックも行われません。 混乱を避けるために、機能が有効の場合にのみ同期規則が追加されるロジックが追加されました。

* パスワード ハッシュ同期がエラー イベント 611 で失敗する問題を修正しました。 この問題は、1 つ以上のドメイン コントローラーがオンプレミスの AD から削除された後に発生します。 パスワード同期サイクルの終了時、オンプレミスの AD によって発行された同期 Cookie には、削除されたドメイン コントローラーの、USN (Update Sequence Number) 値が 0 の呼び出し ID が含まれています。 パスワード同期マネージャーは、USN 値 0 を含む同期 Cookie を保持できないため、エラー イベント 611 で失敗します。 次の同期サイクル中、パスワード同期マネージャーは、USN 値 0 を含まない、最後に保持された同期 Cookie を再利用します。 これにより、同じパスワードの変更が再同期されます。 この修正により、パスワード同期マネージャーは同期 Cookie を正しく保持します。

* Set-ADSyncAutoUpgrade コマンドレットで自動アップグレードを無効にしても、自動アップグレード プロセスにより、アップグレードは引き続き定期的にチェックされ、アップグレードの無効化には、ダウンロードしたインストーラーを使っていました。 この修正により、自動アップグレード プロセスによって、アップグレードが定期的にチェックされなくなっています。 この修正は、この Azure AD Connect バージョンのアップグレード インストーラーが 1 回実行されたときに、自動的に適用されます。

#### <a name="new-features-and-improvements"></a>新機能と機能強化

* 以前は、[ソース アンカーとしての ms-DS-ConsistencyGuid](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) 機能は、新しいデプロイでのみ使用できました。 現在、この機能は、既存のでデプロイでも使用することができます。 具体的には次のとおりです。
  * 機能にアクセスするには、Azure AD Connect ウィザードを開始し、"*ソース アンカーの更新*" オプションを選択します。
  * このオプションは、objectGuid を sourceAnchor 属性として使用している既存のデプロイにのみ表示されます。
  * オプションを構成するとき、オンプレミス Active Directory の ms-DS-ConsistencyGuid 属性の状態がウィザードによって検証されます。 この属性がディレクトリ内のどのユーザー オブジェクトに対しても構成されていない場合は、ms-DS-ConsistencyGuid が sourceAnchor 属性として使用されます。 ディレクトリ内の 1 つ以上のユーザー オブジェクトに対してこの属性が構成済みであった場合は、この属性が他のアプリケーションによって使用されており、sourceAnchor 属性としては適さないため、ソース アンカーの変更を続行できないと判断されます。 この属性が、既存のアプリケーションで使用されていないことが確実である場合は、サポートに連絡してエラーの抑制方法を入手する必要があります。

* デバイス オブジェクトの **userCertificate** 属性に固有の機能として、Azure AD Connect は、Azure AD との同期の前に、[Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy)ときに必要な証明書の値を探して、それ以外の部分を除外できるようになりました。 この動作を有効にするために、既定の同期規則 "AAD への送信 - デバイス結合 SOAInAD" が更新されました。

* Azure AD Connect が、オンプレミス AD **publicDelegates** 属性への Exchange Online **cloudPublicDelegates** 属性のライトバックをサポートするようになりました。 これにより、オンプレミスの Exchange メールボックスを持つユーザーに送信するための SendOnBehalfTo 権限を、Exchange Online メールボックスに付与できるシナリオが有効になります。 この機能をサポートするために、既定の同期規則 "AD への送信 – ユーザー Exchange ハイブリッド PublicDelegates ライトバック" が新しく追加されました。 この同期規則は、Exchange ハイブリッド機能が有効になっている場合にのみ、Azure AD Connect に追加されます。

* Azure AD Connect が、Azure AD からの **altRecipient** 属性の同期をサポートするようになりました。 この変更をサポートするために、次の既定の同期規則が更新され、必須の属性フローが追加されています。
  * AD からの受信 - ユーザー Exchange
  * AAD への送信 – ユーザー ExchangeOnline
  
* メタバースの **cloudSOAExchMailbox** 属性は、特定のユーザーに Exchange Online メールボックスがあるかどうかを示します。 その定義は、追加の Exchange Online RecipientDisplayTypes、つまり備品用メールボックスや会議室のメールボックスを含めるように更新されました。 この変更を有効にするために、既定の同期規則 "AAD からの受信 – ユーザー Exchange ハイブリッド" にある次の cloudSOAExchMailbox 属性の定義が更新されています。

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

更新後の定義は次のとおりです。

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* 同期規則の式を作成するために、X509Certificate2 対応の次の関数を追加しました。これにより、userCertificate 属性の証明書の値が処理されます。

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|選択|
    |CertKeyAlgorithmParams|CertHashString|Where|
    |||With|

* 次のスキーマ変更が行われ、グループ オブジェクトについては sAMAccountName、domainNetBios、および domainFQDN を、ユーザー オブジェクトについては distinguishedName をフローするカスタム同期規則を、顧客が作成できます。

  * 次の属性が、MV スキーマに追加されました。
    * グループ:AccountName
    * グループ: domainNetBios
    * グループ: domainFQDN
    * ユーザー: distinguishedName

  * 次の属性が、Azure AD コネクタ スキーマに追加されました。
    * グループ:OnPremisesSamAccountName
    * グループ:NetBiosName
    * グループ:DnsDomainName
    * ユーザー:OnPremisesDistinguishedName

* ADSyncDomainJoinedComputerSync コマンドレット スクリプトで、AzureEnvironment という新しい省略可能なパラメーターを利用できるようになりました。 このパラメーターを使用して、対応する Azure Active Directory テナントがホストされているリージョンを指定します。 有効な値は、次のとおりです。
  * AzureCloud (既定)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* 同期規則の作成中、リンクの種類の既定値として、(プロビジョニングではなく) 結合が使用されるように同期規則エディターを更新しました。

### <a name="ad-fs-management"></a>AD FS の管理

#### <a name="issues-fixed"></a>修正された問題

* 次の URL は、認証の障害に対する回復性を向上させるために Azure AD によって導入された新しい WS-Federation エンドポイントで、オンプレミスの AD FS 証明書利用者信頼の構成に追加されます。
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* AD FS によって不正確な要求の値が IssuerID に対して生成される問題を修正しました。 この問題は、Azure AD テナントに複数の確認済みドメインがあり、IssuerID 要求の生成に使用される userPrincipalName 属性のドメイン サフィックスの深さが 3 レベル以上 (たとえば、johndoe@us.contoso.com) の場合に発生します。 問題を解決するには、要求規則によって使用される正規表現を更新します。

#### <a name="new-features-and-improvements"></a>新機能と機能強化
* 以前は、Azure AD Connect が提供する ADFS 証明書の管理機能は、Azure AD Connect で管理されている ADFS ファームでのみ使用できました。 現在、この機能は、Azure AD Connect で管理されていない ADFS ファームでも使用できます。

## <a name="115240"></a>1.1.524.0
リリース日:2017 年 5 月

> [!IMPORTANT]
> このビルドでは、スキーマと同期規則に変更が加えられています。 アップグレードの後、フル インポートの手順と完全同期の手順が Azure AD Connect 同期サービスによってトリガーされます。 変更の詳細は以下で説明しています。
>
>

**修正された問題:**

Azure AD Connect Sync

* ユーザーが Set-ADSyncAutoUpgrade コマンドレットを使用して自動アップグレード機能を無効にしたにもかかわらず、Azure AD Connect サーバーで自動アップグレードが実行される問題を修正しました。 この修正の適用後も、サーバー上の自動アップグレード プロセスで引き続きアップグレードが定期的にチェックされますが、ダウンロードされたインストーラーは、自動アップグレードの構成を忠実に守ります。
* DirSync のインプレース アップグレード中、Azure AD Connect は、Azure AD コネクタが Azure AD との同期に使用する Azure AD サービス アカウントを作成します。 アカウントの作成後、Azure AD Connect は、そのアカウントを使って Azure AD を認証します。 この認証が一時的な問題で失敗することがあり、それが原因で、DirSync のインプレース アップグレードも "*An error has occurred executing Configure AAD Sync task: AADSTS50034: To sign into this application, the account must be added to the xxx.onmicrosoft.com directory. (AAD 同期タスクの構成の実行中にエラーが発生しました:AADSTS50034:このアプリケーションにサインインするには、xxx.onmicrosoft.com ディレクトリにアカウントを追加する必要があります。)* " というエラーが発生して失敗することがあります。 DirSync アップグレードの回復性を高めるために、Azure AD Connect で認証ステップが再試行されるようになりました。
* ビルド 443 では、DirSync のインプレース アップグレードは成功するものの、ディレクトリの同期に必要な実行プロファイルが作成されない問題がありました。 このビルドの Azure AD Connect には、復旧ロジックが追加されています。 ユーザーがこのビルドにアップグレードするときに、不足している実行プロファイルが Azure AD Connect によって検出されて作成されます。
* パスワード同期処理が、イベント ID 6900 および "*同一のキーを含む項目が既に追加されています*" というエラーで起動に失敗する問題を修正しました。 この問題は、AD 構成パーティションを含めるように OU のフィルタリング構成を更新した場合に発生します。 この問題を修正するため、AD ドメイン パーティションからのパスワード変更のみを同期するようにパスワード同期処理を変更しました。 非ドメイン パーティション (構成パーティションなど) はスキップされます。
* AD コネクタでオンプレミス AD との通信に使用されるオンプレミス AD DS アカウントが、高速インストール中、Azure AD Connect によって作成されます。 以前のバージョンでは、このアカウントが、user-Account-Control 属性の PASSWD_NOTREQD フラグを設定した状態で作成され、アカウントにはランダムなパスワードが設定されます。 新しいバージョンでは、アカウントのパスワードが設定された後、PASSWD_NOTREQD フラグは Azure AD Connect によって明示的に削除されます。
* オンプレミス AD スキーマに mailNickname 属性が検出されたものの、AD ユーザー オブジェクト クラスにその属性がバインドされていないと、"*a deadlock occurred in sql server which trying to acquire an application lock (アプリケーション ロックの取得を試みるデッドロックが SQL Server で発生しました)* " というエラーが発生して DirSync のアップグレードが失敗する問題を修正しました。
* 管理者が Azure AD Connect ウィザードを使って Azure AD Connect Sync の構成を更新しているときに、デバイスの書き戻し機能が自動的に無効になる問題を修正しました。 この問題は、デバイスの書き戻し構成が既にオンプレミス AD に存在しているときに、ウィザードによってその前提条件チェックが実行され、失敗することが原因で発生します。 デバイスの書き戻しが既に有効になっている場合は、このチェックをスキップすることで問題を修正しました。
* OU のフィルタリングは、Azure AD Connect ウィザードを使用するか、または Synchronization Service Manager を使用して構成することができます。 以前のバージョンでは、Azure AD Connect ウィザードを使用して OU のフィルタリングを構成した場合、後で作成した新しい OU がディレクトリ同期の対象に含められます。 新しい OU を含めたくない場合は、Synchronization Service Manager を使って OU のフィルタリングを構成する必要があります。 新しいバージョンでは、同じ動作を Azure AD Connect ウィザードを使って実現できます。
* Azure AD Connect で必要なストアド プロシージャが、dbo スキーマにではなく、インストールしている管理者のスキーマに作成される問題を修正しました。
* Azure AD から返される TrackingId 属性が AAD Connect Server のイベント ログから抜け落ちる問題を修正しました。 この問題は、Azure AD Connect が Azure AD からリダイレクト メッセージを受信し、指定されたエンドポイントに Azure AD Connect が接続できない場合に発生します。 TrackingId は、トラブルシューティング時に、サービス側のログに関連付ける目的でサポート エンジニアが使用します。
* Azure AD Connect は、Azure AD から LargeObject エラーを受け取ると、EventID 6941 のイベントと "*提供されたオブジェクトが大きすぎます。このオブジェクト上の属性値の数を調整してください*" というメッセージを生成します。 その際、誤解を招くおそれのある EventID 6900 イベントと、"*Microsoft.Online.Coexistence.ProvisionRetryException:Windows Azure Active Directory サービスと通信できません*" というメッセージも Azure AD Connect から生成されます。 今後は混乱を防ぐために、Azure AD Connect で LargeObject エラーが発生しても、後者のイベントは生成されません。
* Generic LDAP コネクタの構成を更新しようとしているときに Synchronization Service Manager が無応答になる問題を修正しました。

**新機能/改善点:**

Azure AD Connect Sync
* 同期規則の変更 - 以下の同期規則に変更を加えました。
  * **userCertificate** 属性と **userSMIMECertificate** 属性に割り当てられている値が 15 個を超えている場合、これらの属性をエクスポートしないように既定の同期規則セットを更新しました。
  * AD 属性 **employeeID** と **msExchBypassModerationLink** は、既定の同期規則セットに追加しました。
  * AD 属性 **photo** は、既定の同期規則セットから削除しました。
  * メタバースのスキーマと AAD コネクタのスキーマに **preferredDataLocation** を追加しました。 Azure AD でいずれかの属性を更新する必要のあるユーザーは、カスタム同期規則を実装してそのようにすることができます。 
  * メタバースのスキーマと AAD コネクタのスキーマに **userType** を追加しました。 Azure AD でいずれかの属性を更新する必要のあるユーザーは、カスタム同期規則を実装してそのようにすることができます。

* 現在、Azure AD Connect では、ConsistencyGuid 属性の使用が、オンプレミスの AD オブジェクトのソース アンカー属性として自動的に有効になります。 また、ConsistencyGuid 属性が空の場合、この属性は、Azure AD Connect によって、objectGuid 属性の値で自動的に設定されます。 この機能は新しいデプロイにのみ適用されます。 この機能の詳細については、「[Azure AD Connect:設計概念」の「sourceAnchor としての ms-DS-ConsistencyGuid の使用](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)」を参照してください。
* トラブルシューティングのための新しいコマンドレット Invoke-ADSyncDiagnostics を追加しました。パスワード ハッシュ同期に関する問題の診断に役立てることができます。 コマンドレットの使用について詳しくは、「[Azure AD Connect Sync を使用したパスワード ハッシュ同期のトラブルシューティング](tshoot-connect-password-hash-synchronization.md)」を参照してください。
* Azure AD Connect で新たに、オンプレミスの AD と Azure AD との間で "メールが有効なパブリック フォルダ" オブジェクトの同期がサポートされます。 この機能は、Azure AD Connect ウィザードのオプション機能から有効にできます。 この機能の詳細については、「[Office 365 Directory Based Edge Blocking support for on-premises Mail Enabled Public Folders (オンプレミスのメールが有効なパブリック フォルダーに対する Office 365 ディレクトリ ベース エッジ ブロック サポート)](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders)」を参照してください。
* Azure AD Connect では、オンプレミスの AD から同期するために AD DS アカウントが必要となります。 以前は、簡易モードを使用して Azure AD Connect をインストールした場合、エンタープライズ管理者アカウントの資格情報を指定でき、必要な AD DS アカウントは Azure AD Connect によって作成されました。 しかし、カスタム インストールを行う場合や、既存のデプロイにフォレストを追加する場合は、AD DS アカウントを自分で指定する必要がありました。 今後は、カスタム インストールの際に、エンタープライズ管理者アカウントの資格情報を指定することで、必要な AD DS アカウントを Azure AD Connect で自動的に作成することもできます。
* Azure AD Connect で新たに SQL AOA がサポートされます。 Azure AD Connect をインストールする前に SQL AOA を有効にする必要があります。 インストール中、指定された SQL インスタンスで SQL AOA が有効であるかどうかが Azure AD Connect によって検出されます。 SQL AOA が有効である場合、Azure AD Connect はさらに、SQL AOA が、同期レプリケーションまたは非同期レプリケーションを使用するように構成されているかどうかを調べます。 可用性グループ リスナーを設定するときは、RegisterAllProvidersIP プロパティを 0 に設定することをお勧めします。 Azure AD Connect は現在、SQL Native Client を使用して SQL に接続していますが、SQL Native Client は、MultiSubNetFailover プロパティの使用をサポートしていないためです。
* Azure AD Connect サーバーのデータベースとして LocalDB を使用していて、サイズの上限である 10 GB に達した場合、それ以降、同期サービスは起動しません。 以前のバージョンでは、LocalDB で ShrinkDatabase 操作を実行し、同期サービスを起動できるだけの DB 空き領域を回収する必要があります。 その後は、Synchronization Service Manager を使用して実行履歴を削除し、DB 空き領域をさらに回収することができます。 新しいバージョンでは、Start-ADSyncPurgeRunHistory コマンドレットを使用して実行履歴データを LocalDB から消去し、DB 空き領域を回収することができます。 このコマンドレットは、同期サービスが実行されていないときに使用できるオフライン モードにも対応しています (-offline パラメーターを指定)。 注:オフライン モードは、同期サービスが実行されておらず、なおかつ使用されているデータベースが LocalDB である場合にのみ使用できます。
* 新しいバージョンの Azure AD Connect では、必要な記憶域スペースを小さくするために、同期エラーの詳細情報は、圧縮してから LocalDB/SQL データベースに格納されます。 以前のバージョンの Azure AD Connect からこのバージョンにアップグレードすると、既に存在している同期エラー情報に対して一回限りの圧縮が実行されます。
* 以前のバージョンでは、OU のフィルタリング構成を更新した後、フル インポートを手動で実行して、ディレクトリ同期の対象に既存のオブジェクトを適切に含めたり、対象から除外したりする必要があります。 新しいバージョンの Azure AD Connect では、次の同期サイクルの間にフル インポートが自動的にトリガーされます。 また、フル インポートは、更新の影響を受けた AD コネクタにのみ適用されます。 注: この機能強化が適用されるのは、Azure AD Connect ウィザードを使用して行われた OU のフィルタリングの更新だけです。 Synchronization Service Manager を使って行われた OU のフィルタリングの更新には適用されません。
* 以前のバージョンでは、グループベースのフィルターが、ユーザー オブジェクト、グループ オブジェクト、連絡先オブジェクトでしかサポートされていません。 新しいバージョンでは、グループベースのフィルターでコンピューター オブジェクトもサポートされます。
* 以前のバージョンでは、Azure AD Connect 同期スケジューラを無効にしなくても、コネクタ スペースのデータを削除することができます。 新しいバージョンでは、スケジューラが有効になっていることを Synchronization Service Manager が検出した場合、コネクタ スペースのデータは、削除できないようブロックされます。 さらに、コネクタ スペースのデータを削除するとデータが失われる可能性があるとして、ユーザーに警告が返されます。
* 以前のバージョンでは、Azure AD Connect ウィザードを正しく動作させるためには、PowerShell トランスクリプションを無効にする必要があります。 この問題は一部解決されています。 Azure AD Connect ウィザードを使用して同期構成を管理している場合は、PowerShell トランスクリプションを有効にすることができます。 Azure AD Connect ウィザードを使用して ADFS の構成を管理している場合は、PowerShell トランスクリプションを無効にする必要があります。



## <a name="114860"></a>1.1.486.0
リリース日:2017 年 4 月

**修正された問題:**
* Azure AD Connect がローカライズ版の Windows Server に正常にインストールされない問題を修正しました。

## <a name="114840"></a>1.1.484.0
リリース日:2017 年 4 月

**既知の問題:**

* 次の条件がすべて当てはまる場合、このバージョンの Azure AD Connect は正常にインストールされません。
   1. Azure AD Connect の DirSync インプレース アップグレードまたは新規インストールのどちらかを実行している。
   2. サーバー上の組み込みの管理者グループの名前が "Administrators" でないローカライズ版の Windows Server を使用している。
   3. 独自の完全な SQL を提供するのではなく、Azure AD Connect と共にインストールされた既定の SQL Server 2012 Express LocalDB を使用している。

**修正された問題:**

Azure AD Connect Sync
* 1 つ以上のコネクタに同期手順の実行プロファイルがない場合、同期スケジューラがその同期手順全体をスキップする問題を修正しました。 たとえば、差分インポート実行プロファイルを作成せずに、Synchronization Service Manager を使用してコネクタを手動で追加した場合です。 この解決策により、同期スケジューラが引き続き他のコネクタの差分インポートを実行することが保証されます。
* いずれかの実行手順で問題が発生した場合、同期サービスが直ちに実行プロファイルの処理を停止する問題を修正しました。 この解決策により、同期サービスがその実行手順をスキップし、残りの処理を続行することが保証されます。 たとえば、複数の実行手順 (オンプレミス AD ドメインごとに 1 つ) を含む AD コネクタ用の差分インポート実行プロファイルがあります。 そのいずれかにネットワーク接続に関する問題が発生した場合でも、同期サービスは他の AD ドメインの差分インポートを実行します。
* 自動アップグレード中に Azure AD Connector の更新がスキップされる問題を修正しました。
* セットアップ中にサーバーがドメイン コントローラであるかどうかを Azure AD Connect が誤って判定し、そのために DirSync アップグレードが失敗する問題を修正しました。
* DirSync インプレース アップグレードで Azure AD Connector の実行プロファイルが作成されない問題を修正しました。
* Generic LDAP コネクタを構成しようとしたときに Synchronization Service Manager ユーザー インターフェイスが無応答になる問題を修正しました。

AD FS の管理
* AD FS プライマリ ノードが別のサーバーに移動されている場合、Azure AD Connect ウィザードが失敗する問題を修正しました。

デスクトップ SSO
* 新規インストール中に [サインイン] オプションとして [パスワードの同期] を選択した場合、[サインイン] 画面で [デスクトップ SSO] 機能を有効にできない Azure AD Connect ウィザードの問題を修正しました。

**新機能/改善点:**

Azure AD Connect Sync
* Azure AD Connect 同期は現在、そのサービス アカウントとして仮想サービス アカウント、管理サービス アカウント、およびグループ管理サービス アカウントの使用をサポートしています。 これは、Azure AD Connect の新規インストールにのみ適用されます。 Azure AD Connect をインストールしている場合:
    * 既定では、Azure AD Connect ウィザードは仮想サービス アカウントを作成し、それをそのサービス アカウントとして使用します。
    * ドメイン コントローラ上にインストールしている場合、Azure AD Connect は、ドメイン ユーザー アカウントを作成する前の動作にフォールバックし、代わりにそれをそのサービス アカウントとして使用します。
    * 次のいずれかを指定することによって、既定の動作をオーバーライドできます。
      * グループ管理サービス アカウント
      * 管理サービス アカウント
      * ドメイン ユーザー アカウント
      * ローカル ユーザー アカウント
* 以前は、コネクタの更新または同期規則の変更を含む Azure AD Connect の新しいビルドにアップグレードすると、Azure AD Connect は完全同期サイクルをトリガーしました。 現在、Azure AD Connect は、更新を含むコネクタに対してのみフル インポート手順を、同期規則の変更を含むコネクタに対してのみ完全同期手順を選択的にトリガーします。
* 以前は、エクスポート削除しきい値は、同期スケジューラからトリガーされたエクスポートにのみ適用されました。 現在、この機能は、顧客が Synchronization Service Manager を使用して手動でトリガーしたエクスポートを含むように拡張されています。
* Azure AD テナント上に、そのテナントで [パスワードの同期] 機能が有効になっているかどうかを示すサービス構成が存在します。 以前は、アクティブなステージング サーバーがある場合、Azure AD Connect はサービス構成を簡単に誤って構成しました。 現在、Azure AD Connect は、サービス構成をアクティブな Azure AD Connect サーバーだけと整合性のある状態に保持しようとします。
* Azure AD Connect ウィザードは現在、オンプレミス AD で AD のごみ箱が有効になっていないかどうかを検出し、警告を返します。
* 以前は、バッチ内のオブジェクトの合計サイズが特定のしきい値を超えている場合、Azure AD へのエクスポートはタイムアウトし、失敗しました。 現在、この問題が発生した場合、同期サービスは個別の、より小さなバッチでのオブジェクトの再送信を再度試みます。
* 同期サービス キー管理アプリケーションが Windows の [スタート] メニューから削除されました。 暗号化キーの管理は、miiskmu.exe を使用してコマンド ライン インターフェイス経由で引き続きサポートされます。 暗号化キーの管理については、[Azure AD Connect 同期の暗号化キーの破棄](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-adsync-service-account-encryption-key)の記事を参照してください。
* 以前は、Azure AD Connect 同期サービス アカウントのパスワードを変更すると、暗号化キーを破棄し、Azure AD Connect 同期サービス アカウントのパスワードを再初期化するまで、同期サービスを正常に開始できなくなります。 現在、このプロセスは必要なくなりました。

デスクトップ SSO

* Azure AD Connect ウィザードでは、パススルー認証およびデスクトップ SSO を構成する場合、ネットワーク上でポート 9090 を開く必要がなくなりました。 ポート 443 のみが必要です。 

## <a name="114430"></a>1.1.443.0
リリース日:2017 年 3 月

**修正された問題:**

Azure AD Connect Sync
* Azure AD テナントに割り当てられた初期の onmicrosoft.com ドメインが Azure AD コネクタの表示名に含まれていない場合に Azure AD Connect ウィザードが失敗する原因となる問題が修正されました。
* 同期サービス アカウントのパスワードにアポストロフィ、コロン、スペースなどの特殊文字が含まれている場合に SQL Database への接続時に Azure AD Connect ウィザードが失敗する原因となる問題が修正されました。
* オンプレミスの AD オブジェクトを同期から一時的に除外してからもう一度同期に含めた後で、ステージング モードの Azure AD Connect サーバー上で "The dimage has an anchor that is different than the image"\(dimage にイメージとは異なるアンカーがあります\) というエラーが発生する原因となる問題が修正されました。
* オンプレミスの ADオブジェクトを同期から一時的に除外してからもう一度同期に含めた後で、ステージング モードの Azure AD Connect サーバーで「The object located by DN is a phantom (DN によって特定されたオブジェクトはファントムです)」エラーが発生する原因となる問題が修正されました。

AD FS の管理
* 代替ログイン ID が構成された後、Azure AD Connect ウィザードが AD FS の構成を更新せず、証明書利用者信頼に対する適切な要求を設定しない問題が修正されました。
* サービス アカウントが sAMAccountName 形式ではなく userPrincipalName 形式を使用して構成されている AD FS サーバーを Azure AD Connect ウィザードが正しく処理できない問題が修正されました。

パススルー認証
* パススルー認証が選ばれていてもそのコネクタの登録に失敗する場合に Azure AD Connect ウィザードが失敗する原因となる問題が修正されました。
* デスクトップ SSO 機能を有効にするときに選ばれたサインイン メソッドに対する検証チェックを Azure AD Connect ウィザードがバイパスする原因となる問題が修正されました。

パスワードのリセット
* ファイアウォールまたはプロキシにより接続が強制終了した場合に Azure AAD Connect サーバーによる再接続の試行が行われない問題が修正されました。

**新機能/改善点:**

Azure AD Connect Sync
* Get-ADSyncScheduler コマンドレットは、SyncCycleInProgress という名前の新しいブール値プロパティを返すようになりました。 戻り値が true の場合は、進行中のスケジュールされた同期サイクルがあることを意味します。
* Azure AD Connect のインストールおよびセットアップ ログを格納するための保存先フォルダーが %localappdata%\AADConnect から %programdata%\AADConnect に移動され、ログ ファイルへのアクセシビリティが改善されました。

AD FS の管理
* AD FS ファーム SSL 証明書の更新のサポートが追加されました。
* AD FS 2016 を管理するためのサポートが追加されました。
* AD FS のインストール中に既存の gMSA (グループ管理サービス アカウント) を指定できるようになりました。
* SHA-256 を Azure AD 証明書利用者信頼の署名ハッシュ アルゴリズムとして構成できるようになりました。

パスワードのリセット
* より厳格なファイアウォール規則の環境においても製品が機能するように機能強化が導入されました。
* Azure Service Bus への接続信頼性が向上しました。

## <a name="113800"></a>1.1.380.0
リリース日:2016 年 12 月

**修正された問題:**

* このビルドでの Active Directory Federation Services (AD FS) 用の issuerid 要求規則が欠落しているという問題を解決しました。

>[!NOTE]
>このビルドは、Azure AD Connect の自動アップグレード機能では提供されません。

## <a name="113710"></a>1.1.371.0
リリース日:2016 年 12 月

**既知の問題:**

* このビルドには AD FS 用の issuerid 要求規則が欠落しています。 Azure Active Directory (Azure AD) で複数のドメインのフェデレーションを行っている場合は issuerid 要求規則が必要です。 Azure AD Connect を使用してオンプレミスの AD FS デプロイを管理している場合は、このビルドにアップグレードすると、既存の issuerid 要求規則が AD FS 構成から削除されます。 この問題は、インストール/アップグレード後に issuerid 要求規則を追加することで回避できます。 issuerid 要求規則の追加の詳細については、「[Azure AD とのフェデレーションに使用する複数ドメインのサポート](how-to-connect-install-multiple-domains.md)」を参照してください。

**修正された問題:**

* ポート 9090 が送信接続に開かれていない場合、Azure AD Connect のインストールまたはアップグレードは失敗します。

>[!NOTE]
>このビルドは、Azure AD Connect の自動アップグレード機能では提供されません。

## <a name="113700"></a>1.1.370.0
リリース日:2016 年 12 月

**既知の問題:**

* このビルドには AD FS 用の issuerid 要求規則が欠落しています。 Azure AD で複数のドメインのフェデレーションを行っている場合は issuerid 要求規則が必要です。 Azure AD Connect を使用してオンプレミスの AD FS デプロイを管理している場合は、このビルドにアップグレードすると、既存の issuerid 要求規則が AD FS 構成から削除されます。 この問題は、インストール/アップグレード後に issuerid 要求規則を追加することで回避できます。 issuerid 要求規則の追加の詳細については、「[Azure AD とのフェデレーションに使用する複数ドメインのサポート](how-to-connect-install-multiple-domains.md)」を参照してください。
* インストールを完了するには、ポート 9090 が送信に対して開かれている必要があります。

**新機能:**

* パススルー認証 (プレビュー)。

>[!NOTE]
>このビルドは、Azure AD Connect の自動アップグレード機能では提供されません。

## <a name="113430"></a>1.1.343.0
リリース日:2016 年 11 月

**既知の問題:**

* このビルドには AD FS 用の issuerid 要求規則が欠落しています。 Azure AD で複数のドメインのフェデレーションを行っている場合は issuerid 要求規則が必要です。 Azure AD Connect を使用してオンプレミスの AD FS デプロイを管理している場合は、このビルドにアップグレードすると、既存の issuerid 要求規則が AD FS 構成から削除されます。 この問題は、インストール/アップグレード後に issuerid 要求規則を追加することで回避できます。 issuerid 要求規則の追加の詳細については、「[Azure AD とのフェデレーションに使用する複数ドメインのサポート](how-to-connect-install-multiple-domains.md)」を参照してください。

**修正された問題:**

* 組織のパスワード ポリシーで指定された複雑さのレベルを満たしているパスワードを持つローカル サービス アカウントを作成することができないため、Azure AD Connect のインストールが失敗することがあります。
* コネクタ スペースのオブジェクトが、1 つの結合規則ではスコープ外になり、別の結合規則ではスコープ内になった場合に、結合規則が再評価されない問題が修正されました。 この問題は、結合条件が相互に排他的になっている 2 つ以上の結合規則がある場合に発生することがあります。
* 結合規則のない (Azure AD からの) 受信同期規則の優先順位の値が、結合規則のある受信同期規則よりも低い場合、結合規則のない受信同期規則が処理されないという問題が修正されました。

**機能強化:**

* Windows Server 2016 Standard 以降に Azure AD Connect をインストールするためのサポートが追加されました。
* Azure AD Connect のリモートのデータベースとしての SQL Server 2016 の使用がサポートされるようになりました。

## <a name="112810"></a>1.1.281.0
リリース日:2016 年 8 月

**修正された問題:**

* 同期間隔の変更が、次の同期サイクルの完了後まで反映されません。
* Azure AD Connect ウィザードで、アンダースコア (\_) で始まるユーザー名を持つ Azure AD アカウントを使用できません。
* Azure AD Connect ウィザードで、アカウントのパスワードに含まれる特殊文字の数が多すぎると、指定した Azure AD アカウントの認証が失敗します。 "資格情報を検証できません。 予期しないエラーが発生しました" というエラー メッセージが 返されます。
* ステージング サーバーをアンインストールすると、Azure AD テナントでパスワード同期が無効になり、アクティブなサーバーでのパスワード同期が失敗します。
* ユーザーに対してパスワードのハッシュが格納されていない場合、例外的な状況でパスワード同期が失敗します。
* Azure AD Connect サーバーでステージング モードが有効になっている場合、パスワード ライトバックが一時的に無効になりません。
* サーバーがステージング モードの場合、Azure AD Connect ウィザードでパスワード同期とパスワード ライトバックの実際の構成が表示されません。 常に無効として表示されます。
* サーバーがステージング モードの場合、パスワード同期とパスワード ライトバックの構成への変更が Azure AD Connect ウィザードで保持されません。

**機能強化:**

* Start-ADSyncSyncCycle コマンドレットが更新され、新しい同期サイクルを正常に開始できるかどうかを示すようになりました。
* Stop-ADSyncSyncCycle コマンドレットが追加され、現在実行中の同期サイクルと操作を終了できるようになりました。
* Stop-ADSyncScheduler コマンドレットが更新され、現在実行中の同期サイクルと操作を終了できるようになりました。
* Azure AD Connect ウィザードで[ディレクトリ拡張機能](how-to-connect-sync-feature-directory-extensions.md)を構成するときに、"Teletex 文字列" 型の Azure AD 属性を選択できるようになりました。

## <a name="111890"></a>1.1.189.0
リリース日:2016 年 6 月

**修正された問題と機能強化:**

* Azure AD Connect を FIPS に準拠しているサーバーにインストールできるようになりました。
  * パスワードの同期については、[パスワード ハッシュの同期と FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips) に関する記事を参照してください。
* Active Directory コネクタで、NetBIOS を FQDN に名前解決できないという問題が修正されました。

## <a name="111800"></a>1.1.180.0
リリース日:2016 年 5 月

**新機能:**

* Azure AD Connect 実行前にドメインの確認が行われなかった場合に、ドメインの確認について警告し、必要なヘルプ情報を提供します。
* [Microsoft Cloud Germany](reference-connect-instances.md#microsoft-cloud-germany)のサポートが追加されました。
* 新しい URL 要件を含む最新の [Microsoft Azure Government クラウド](reference-connect-instances.md#microsoft-azure-government) インフラストラクチャのサポートが追加されました。

**修正された問題と機能強化:**

* 同期規則を探しやすくするフィルターが同期規則エディターに追加されました。
* コネクタ スペースを削除するときのパフォーマンスが改善されました。
* 同じオブジェクトに対して削除と追加の両方が同一の実行で行われた場合 (削除/追加) の問題が修正されました。
* 無効にした同期規則で、含まれるオブジェクトや属性が、アップグレードまたはディレクトリ スキーマの更新時に再び有効にされることがなくなりました。

## <a name="111300"></a>1.1.130.0
リリース日:2016 年 4 月

**新機能:**

* [ディレクトリ拡張機能](how-to-connect-sync-feature-directory-extensions.md)に、複数値の属性のサポートが追加されました。
* アップグレードの対象と見なされる [自動アップグレード](how-to-connect-install-automatic-upgrade.md) の構成バリエーションが増えました。
* [カスタム スケジューラ](how-to-connect-sync-feature-scheduler.md#custom-scheduler)にコマンドレットがいくつか追加されました。

## <a name="111190"></a>1.1.119.0
リリース日:2016 年 3 月

**修正された問題:**

* Windows Server 2008 (R2 より前のバージョン) ではパスワード同期がサポートされないため、このオペレーティング システムでは高速インストールが使用できなくなりました。
* カスタム フィルター構成での DirSync からのアップグレードが予期したとおりに動作しません。
* 新しいリリースへのアップグレード時に構成に変更がない場合は、フル インポート/同期をスケジュールすることはできません。

## <a name="111100"></a>1.1.110.0
リリース日:2016 年 2 月

**修正された問題:**

* インストールが既定の C:\Program Files フォルダーにない場合、以前のリリースからのアップグレードが機能しません。
* インストール時に、インストール ウィザードの最後で **[...同期処理を開始してください]** をオフにした場合、2 回目にインストール ウィザードを実行したときにスケジューラが有効になりません。
* 日付と時刻の形式が US-en ではない場合、スケジューラはサーバーで予想どおりに機能しません。 また、正しい時刻を返す `Get-ADSyncScheduler` もブロックされます。
* サインイン オプションおよびアップグレードとして AD FS を使用して以前のリリースの Azure AD Connect をインストールした場合、インストール ウィザードを再度実行することはできません。

## <a name="111050"></a>1.1.105.0
リリース日:2016 年 2 月

**新機能:**

* [Automatic upgrade](how-to-connect-install-automatic-upgrade.md) 機能。
* インストール ウィザードで Azure Multi-Factor Authentication および Privileged Identity Management を使用して全体管理者をサポート。
  * Multi-Factor Authentication を使用する場合は、 https://secure.aadcdn.microsoftonline-p.com へのトラフィックも許可するように、プロキシを設定する必要があります。
  * Multi-Factor Authentication を正しく動作させるには、信頼済みサイトの一覧に https://secure.aadcdn.microsoftonline-p.com を追加する必要があります。
* 初期インストール後のユーザーのサインイン方法の変更を許可。
* インストール ウィザードでの[ドメインと OU のフィルター処理](how-to-connect-install-custom.md#domain-and-ou-filtering)を許可。 これによって、一部のドメインは使用できないフォレストへの接続も許可されます。
* 同期エンジンに組み込まれた[スケジューラ](how-to-connect-sync-feature-scheduler.md)。

**プレビューから GA に昇格した機能:**

* [デバイスの書き戻し](how-to-connect-device-writeback.md)。
* [ディレクトリ拡張機能](how-to-connect-sync-feature-directory-extensions.md)。

**新しいプレビュー機能:**

* 新しい既定の同期サイクル間隔は 30 分です。 以前のすべてのリリースでは、3 時間でした。 [スケジューラ](how-to-connect-sync-feature-scheduler.md) の動作の変更がサポートされるようになりました。

**修正された問題:**

* DNS ドメインの検証ページが、ドメインを認識できない場合がありました。
* AD FS を構成するときに、ドメイン管理者の資格情報を求めるメッセージが表示されます。
* オンプレミス AD アカウントが、ルート ドメインとは異なる DNS ツリーを持つドメイン内にある場合、インストール ウィザードがそのアカウントを認識できません。

## <a name="1091310"></a>1.0.9131.0
リリース日:2015 年 12 月

**修正された問題:**

* Active Directory Domain Services (AD DS) でパスワードを変更するときにパスワードの同期が機能しない場合がありますが、パスワードの設定時には機能します。
* プロキシ サーバーがある場合、Azure AD に対する認証が、インストール中または構成ページでアップグレードが取り消された場合に失敗することがあります。
* SQL Server のシステム管理者 (SA) でない場合、完全な SQL Server インスタンスで以前のリリースの Azure AD Connect から更新すると失敗します。
* リモートの SQL Server で以前のリリースの Azure AD Connect から更新すると、"ADSync SQL データベースにアクセスできません" というエラーが表示されます。

## <a name="1091250"></a>1.0.9125.0
リリース日:2015 年 11 月

**新機能:**

* AD FS から Azure AD に対する信頼を再構成できるようになりました。
* Active Directory スキーマを更新し、同期規則を再生成できるようになりました。
* 同期規則を無効にできるようになりました。
* 同期規則の新しいリテラルとして "AuthoritativeNull" を定義できるようになりました。

**新しいプレビュー機能:**

* [Azure AD Connect Health for Sync](how-to-connect-health-sync.md)。
* [Azure AD Domain Services](../user-help/active-directory-passwords-update-your-own-password.md) のパスワード同期がサポートされました。

**新しくサポートされたシナリオ:**

* 複数のオンプレミス Exchange 組織がサポートされました。 詳細については、「[複数の Active Directory フォレストを伴うハイブリッド展開](https://technet.microsoft.com/library/jj873754.aspx)」を参照してください。

**修正された問題:**

* パスワード同期の問題:
  * スコープ外からスコープ内に移動されたオブジェクトのパスワードは同期されなくなります。 これには、OU と属性フィルターの両方が含まれます。
  * 同期に含める新しい OU を選択する際に、完全なパスワード同期は必要ありません。
  * 無効なユーザーが有効になっても、パスワードは同期されません。
  * パスワード再試行キューは無制限です。5,000 個のオブジェクトを上限として削除されるという以前の制限は削除されました。
* Windows Server 2016 フォレスト機能レベルを使用して Active Directory に接続することはできなくなりました。
* 最初のインストール後にグループ フィルターに使用したグループを変更できなくなりました。
* パスワード ライトバックを有効にしてパスワードを変更した各ユーザーについては、Azure AD Connect の新しいユーザー プロファイルを作成しなくなりました。
* 同期規則スコープに Long Integer 値を使用できなくなりました。
* 到達不能なドメイン コントローラーがある場合、[デバイスの書き戻し] チェック ボックスは無効なままです。

## <a name="1086670"></a>1.0.8667.0
リリース日:2015 年 8 月

**新機能:**

* Azure AD Connect インストール ウィザードが、すべての Windows Server 言語にローカライズされました。
* Azure AD パスワード管理を使用する場合のアカウント ロック解除のサポートが追加されました。

**修正された問題:**

* インストールを開始したユーザー以外のユーザーがインストールを続けると、Azure AD Connect インストール ウィザードがクラッシュします。
* 以前の Azure AD Connect のアンインストールで Azure AD Connect Sync を完全にアンインストールできなかった場合、再インストールすることができません。
* ユーザーがフォレストのルート ドメインに属していないか、英語以外のバージョンの Active Directory が使用されている場合、高速インストールを使用して Azure AD Connect をインストールすることはできません。
* Active Directory ユーザー アカウントの FQDN を解決できない場合、スキーマをコミットできなかったという誤ったエラー メッセージが表示されます。
* Active Directory Connector で使用されているアカウントがウィザードの外部で変更された場合、ウィザードのその後の実行が失敗します。
* ドメイン コントローラーで、Azure AD Connect のインストールが失敗することがあります。
* 拡張属性が追加されている場合、"ステージング モード" の有効化や無効化ができません。
* Active Directory Connector での正しくないパスワードのために、一部の構成ではパスワード ライトバックが失敗します。
* 属性フィルターで識別名 (DN) が使用されている場合、DirSync をアップグレードできません。
* パスワード リセットの使用時に CPU 使用量が過剰になります。

**削除されたプレビュー機能:**

* [ユーザーの書き戻し](how-to-connect-preview.md#user-writeback) プレビュー機能は、プレビューを利用されているお客様からのフィードバックに基づいて一時的に削除されました。 このプレビュー機能は、提供されたフィードバックに対処した後で、再度追加されます。

## <a name="1086410"></a>1.0.8641.0
リリース日:2015 年 6 月

**Azure AD Connect の最初のリリースです。**

名前が Azure AD Sync から Azure AD Connect に変更されました。

**新機能:**

* [簡単設定](how-to-connect-install-express.md)を使用したインストール
* [AD FS の構成](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* [DirSync からのアップグレード](how-to-dirsync-upgrade-get-started.md)
* [誤って削除されないように保護する](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* [ステージング モード](how-to-connect-sync-staging-server.md)

**新しいプレビュー機能:**

* [ユーザーの書き戻し](how-to-connect-preview.md#user-writeback)
* [グループの書き戻し](how-to-connect-preview.md#group-writeback)
* [デバイスの書き戻し](how-to-connect-device-writeback.md)
* [ディレクトリ拡張機能](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
リリース日:2015 年 5 月

**新しい要件:**

* Azure AD Sync のインストールに .NET Framework 4.5.1 が必要になりました。

**修正された問題:**

* Azure AD からのパスワード ライトバックが、Azure Service Bus 接続のエラーで失敗します。

## <a name="104910413"></a>1.0.491.0413
リリース日:2015 年 4 月

**修正された問題と機能強化:**

* ごみ箱が有効になっていて、フォレスト内に複数のドメインがある場合、Active Directory Connector が削除を正しく処理しません。
* Azure Active Directory Connector で、インポート操作のパフォーマンスが改善されました。
* グループがメンバーシップの制限を超えた場合 (既定では、制限は 50,000 オブジェクトに設定)、Azure Active Directory でグループが削除されます。 新しい動作では、グループは削除されません。エラーがスローされ、新しいメンバーシップの変更はエクスポートされません。
* 同じ DN のステージングされた削除がコネクタ スペース内に既に存在する場合、新しいオブジェクトをプロビジョニングすることはできません。
* オブジェクトでステージングされている変更がなくても、差分同期中に一部のオブジェクトが同期のためにマークされます。
* パスワード同期を強制すると、優先 DC リストも削除されます。
* CSExportAnalyzer には、一部のオブジェクトの状態に関する問題があります。

**新機能:**

* 結合で、MV の "任意" のオブジェクト型に接続できるようになりました。

## <a name="104850222"></a>1.0.485.0222
リリース日:2015 年 2 月

**機能強化:**

* インポートのパフォーマンスが強化されました。

**修正された問題:**

* パスワード同期が、属性フィルターで使用される cloudFiltered 属性を受け取ります。 フィルター処理されたオブジェクトが、パスワード同期のスコープに含まれなくなります。
* トポロジが多くのドメイン コントローラーを持つまれな状況では、パスワード同期が機能しません。
* Azure AD/Intune でデバイス管理が有効化された後、Azure AD Connector からのインポート時に、"サーバーが停止" します。
* 同じフォレスト内の複数のドメインの外部セキュリティ プリンシパル (FSP) を結合すると、あいまい結合のエラーが発生します。

## <a name="104751202"></a>1.0.475.1202
リリース日:2014 年 12 月

**新機能:**

* 属性ベースのフィルターでのパスワード同期がサポートされるようになりました。 詳細については、[フィルターによるパスワード同期](how-to-connect-sync-configure-filtering.md)に関するページを参照してください。
* ms-DS-ExternalDirectoryObjectID 属性が Active Directory に書き戻されます。 この機能により、Office 365 アプリケーションのサポートが追加されます。 OAuth2 を使用して、ハイブリッド Exchange デプロイのオンラインとオンプレミスのメールボックスへのアクセスが行われます。

**修正されたアップグレードの問題:**

* より新しいバージョンのサインイン アシスタントをサーバーで利用できます。
* Azure AD Sync をインストールするために、カスタム インストール パスが使用されていました。
* 無効なカスタム結合条件によって、アップグレードがブロックされます。

**その他の修正:**

* Office Pro Plus 用のテンプレートが修正されました。
* ダッシュで始まるユーザー名によって発生する、インストールの問題が修正されました。
* インストール ウィザードを 2 回目に実行しているときに sourceAnchor 設定が失われる問題を修正しました。
* パスワード同期の ETW トレースの問題が修正されました。

## <a name="104701023"></a>1.0.470.1023
リリース日:2014 年 10 月

**新機能:**

* 複数のオンプレミス Active Directory から Azure AD へのパスワードの同期。
* すべての Windows Server 言語にローカライズされたインストール UI。

**AADSync 1.0 GA からのアップグレード**

Azure AD Sync が既にインストールされている場合、標準の同期規則を変更したのであれば、追加の手順が 1 つ必要になります。 1\.0.470.1023 リリースにアップグレードした後で、変更した同期規則は複製されます。 変更された各同期規則で、次の操作を行ってください。

1. 変更した同期規則を探して、変更内容をメモしておきます。
1. 同期規則を削除します。
1. Azure AD Sync によって作成された新しい同期規則を探して、変更を再適用します。

**Active Directory アカウントのアクセス許可**

Active Directory アカウントには、Active Directory からのパスワード ハッシュを読み取ることができるように、追加のアクセス許可を与える必要があります。 付与するアクセス許可の名前は、[ディレクトリの変更のレプリケート] と [ディレクトリの変更をすべてにレプリケート] です。 パスワード ハッシュを読み取るためには、両方のアクセス許可が必要です。

## <a name="104190911"></a>1.0.419.0911
リリース日:2014 年 9 月

**Azure AD Sync の最初のリリースです。**

## <a name="next-steps"></a>次の手順
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
