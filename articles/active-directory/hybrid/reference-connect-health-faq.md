---
title: Azure Active Directory Connect Health の FAQ | Microsoft Docs
description: この FAQ は、Azure AD Connect Health について寄せられる質問とその回答です。 サービスの課金モデル、機能、制限、サポートなど、その使用に関して多く寄せられる質問を取り上げています。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6185c5408f74b914ce5ad47634999786ba1d7ab6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367998"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health についてよく寄せられる質問
この記事には、Azure Active Directory (Azure AD) Connect Health に関してよく寄せられる質問 (FAQ) に対する回答が記載されています。 これらの FAQ では、課金モデル、機能、制限、サポートなど、サービスの使用方法に関する質問を取り上げています。

## <a name="general-questions"></a>一般的な質問
**Q:複数の Azure AD ディレクトリを管理しています。どのようにすれば Azure Active Directory Premium のディレクトリに切り替えることができますか。**

Azure AD テナントを切り替えるには、現在サインインしている **ユーザー名** を右上隅で選択して、適切なアカウントを選択します。 ここにアカウントが表示されていない場合は、 **[サインアウト]** を選択し、Azure Active Directory Premium (P1 または P2) が有効になっているディレクトリのグローバル管理者資格情報を使用してサインインします。

**Q:Azure AD Connect Health では、どのバージョンの ID の役割がサポートされていますか。**

次の表は、役割と、サポートされているオペレーティング システムのバージョンの一覧です。

|Role| オペレーティング システム/バージョン|
|--|--|
|Active Directory フェデレーション サービス (AD FS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|
|Azure AD Connect | バージョン 1.0.9125 以上|
|Active Directory Domain Services (AD DS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|

Windows Server Core インストールはサポートされません。

サービスが提供する機能は、役割とオペレーティング システムによって異なる場合があることに注意してください。 つまり、すべての機能をすべてのオペレーティング システムのバージョンで利用できるとは限りません。 詳しくは、機能の説明をご覧ください。

**Q:インフラストラクチャの監視には、いくつライセンスが必要ですか。**

* 最初の Connect Health エージェントに、少なくとも 1 つの Azure AD Premium (P1 または P2) ライセンスが必要です。
* 追加登録されるエージェントごとに、25 個の追加 Azure AD Premium (P1 または P2) ライセンスが必要です。
* エージェントの数は、監視対象のすべての役割 (AD FS、Azure AD Connect、AD DS) で登録されるエージェントの合計数に相当します。
* AAD Connect Health のライセンスでは、特定のユーザーにライセンスを割り当てる必要はありません。 必要な数の有効なライセンスを用意することだけが必要です。

ライセンスの情報については、[Azure AD の料金に関するページ](https://aka.ms/aadpricing)もご覧ください。

例:

| 登録されているエージェント | 必要なライセンスの数 | 監視構成の例 |
| ------ | --------------- | --- |
| 1 | 1 | Azure AD Connect サーバー × 1 |
| 2 | 26| Azure AD Connect サーバー × 1、ドメイン コントローラー × 1 |
| 3 | 51 | Active Directory フェデレーション サービス (AD FS) サーバー × 1、AD FS プロキシ × 1、ドメイン コントローラー × 1 |
| 4 | 76 | AD FS サーバー × 1、AD FS プロキシ × 1、ドメイン コントローラー × 2 |
| 5 | 101 | Azure AD Connect サーバー × 1、AD FS サーバー × 1、AD FS プロキシ × 1、ドメイン コントローラー × 2 |

**Q:Azure AD Connect Health は、Azure Germany Cloud をサポートしていますか。**

[同期エラー レポート機能](how-to-connect-health-sync.md#object-level-synchronization-error-report)を除いて、Azure AD Connect Health は、Germany Cloud ではサポートされていません。

| ロール | 特徴 | German Cloud でサポートされている |
| ------ | --------------- | --- |
| Connect Health for Sync | 監視/分析情報/アラート/分析 | いいえ |
|  | 同期エラー レポート | はい |
| Connect Health for ADFS | 監視/分析情報/アラート/分析 | いいえ |
| Connect Health for ADDS | 監視/分析情報/アラート/分析 | いいえ |

同期のための Connect Health のエージェントの接続を確保するには、必要に応じて[インストール要件](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints)を構成します。

## <a name="installation-questions"></a>インストールに関する質問

**Q:各サーバーに Azure AD Connect Health エージェントをインストールすると、どのような影響がありますか。**

Microsoft Azure AD Connect Health エージェント、AD FS、Web アプリケーション プロキシ サーバー、Azure AD Connect (同期) サーバー、ドメイン コントローラーをインストールすることによる、CPU、メモリ消費、ネットワーク帯域幅、ストレージへの影響は最小限です。

おおよその数値を以下に示します。

* CPU 消費率: 約 1 ～ 5% 上昇。
* メモリ使用量:システム メモリ合計の最大 10%。

> [!NOTE]
> Azure と通信できない場合、エージェントは定義済みの上限までデータをローカルに保存します。 エージェントは、"最も長く使用されていない" という基準で、"キャッシュされた" データを上書きします。
>
>

* Azure AD Connect Health エージェントのローカル バッファー ストレージ: 約 20 MB。
* AD FS サーバーの場合、Azure AD Connect Health エージェントの AD FS 監査チャネルがすべての監査データを上書き前に処理できるように、1,024 MB (1 GB) のディスク領域をプロビジョニングすることをお勧めします。

**Q:Azure AD Connect Health エージェントのインストール時にサーバーを再起動する必要はありますか。**

いいえ。 エージェントをインストールする場合、サーバーを再起動する必要はありません。 ただし、前提条件の一部のインストール手順では、サーバーの再起動が必要です。

たとえば、Windows Server 2008 R2 では、.NET Framework 4.5 をインストールする際にサーバーの再起動が必要です。

**Q:Azure AD Connect Health はパススルー HTTP プロキシで動作しますか。**

はい。 実行中の操作については、HTTP プロキシを使用して送信 HTTP 要求を転送するように Health エージェントを構成できます。
詳しくは、[Health エージェント対応の HTTP プロキシの構成](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)に関するページをご覧ください。

エージェントの登録時にプロキシを構成する必要がある場合は、Internet Explorer のプロキシ設定を事前に変更する必要があることがあります。

1. Internet Explorer を開いて、 **[設定]**  >  **[インターネット オプション]**  >  **[接続]**  >  **[LAN の設定]** の順に移動します。
2. **[LAN にプロキシ サーバーを使用する]** をオンにします。
3. HTTP と HTTPS/Secure でプロキシ ポートが異なる場合は、 **[詳細設定]** を選択します。

**Q:Azure AD Connect Health では、HTTP プロキシに接続するときの基本認証がサポートされていますか。**

いいえ。 基本認証に対して任意のユーザー名とパスワードを指定するメカニズムは、現在サポートされていません。

**Q:Azure AD Connect Health エージェントを使用するには、どのファイアウォール ポートを開放すればよいですか。**

ファイアウォールのポートとその他の接続要件の一覧については、「[必要条件](how-to-connect-health-agent-install.md#requirements)」セクションをご覧ください。

**Q:Azure AD Connect Health ポータルに同じ名前のサーバーが 2 つ表示されるのはなぜですか。**

サーバーからエージェントを削除しても、サーバーは Azure AD Connect Health ポータルから自動的に削除されません。 サーバーから手動でエージェントを削除したか、サーバー自体を削除した場合は、Azure AD Connect Health ポータルから手動でサーバー エントリを削除する必要があります。

サーバーを再イメージ化したり、詳細情報 (マシン名など) が同じになっているサーバーを新規に作成したりすることがありますが、 Azure AD Connect Health ポータルから登録済みサーバーを削除せずに新しいサーバーにエージェントをインストールした場合、同じ名前の 2 つのエントリが表示される可能性があります。

この場合、古いサーバーに対応するエントリを手動で削除します。 間違いなく、このサーバーのデータは古くなっています。

**Q:Azure AD Connect Health エージェントを Windows Server Core にインストールできますか?**

いいえ。  Server Core へのインストールはサポートされていません。

## <a name="health-agent-registration-and-data-freshness"></a>Health エージェントの登録とデータの新しさ

**Q:Health エージェントの登録が失敗する一般的な理由と、問題をトラブルシューティングする方法を教えてください。**

Health エージェントの登録は、次の理由により失敗する可能性があります。

* ファイアウォールによってトラフィックがブロックされているため、エージェントが必要なエンドポイントと通信できません。 これは特に、Web アプリケーション プロキシ サーバーで起こりがちです。 必要なエンドポイントとポートへの発信通信が許可されていることを確認してください。 詳しくは、「[必要条件](how-to-connect-health-agent-install.md#requirements)」セクションをご覧ください。
* 発信通信がネットワーク層による TLS 検査の対象になっています。 これが原因で、エージェントによって使われる証明書が検査サーバー/エンティティによって置き換えられ、エージェントの登録を完了する手順が失敗します。
* ユーザーに、エージェントの登録を実行するためのアクセス権がありません。 グローバル管理者には、既定でアクセス権が与えられます。 [Azure ロール ベースのアクセス制御 (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac) を使って、他のユーザーにアクセスを委任できます。

**Q:"Health サービス データが最新ではありません。" というアラートが通知されます。この問題をトラブルシューティングする方法を教えてください。**

Azure AD Connect Health では、過去 2 時間でサーバーから一部のデータ ポイントが送られなかった場合に、このアラートが生成されます。 詳細については、[こちら](how-to-connect-health-data-freshness.md)を参照してください。

## <a name="operations-questions"></a>操作に関する質問
**Q:Web アプリケーション プロキシ サーバーで監査を有効にする必要はありますか。**

いいえ。Web アプリケーション プロキシ サーバーで監査を有効にする必要はありません。

**Q:Azure AD Connect Health アラートはどのように解決すればよいですか。**

Azure AD Connect Health アラートは、成功条件を満たすと解決されます。 Azure AD Connect Health エージェントは、定期的に成功条件を検出してサービスにレポートします。 一部のアラートは、時間に基づいて抑制されます。 つまり、アラートの生成から 72 時間以内に同じエラー条件が観察されない場合、アラートは自動的に解決されます。

**Q:"テスト認証要求 (代理トランザクション) は、トークンの取得に失敗しました。" というアラートが表示されます。この問題をトラブルシューティングする方法を教えてください。**

このアラートは、AD FS サーバーにインストールされている正常性エージェントが、正常性エージェントが開始する代理トランザクションの一環でトークンの取得に失敗した場合、Azure AD Connect Health for AD FS によって生成されます。 正常性エージェントは、ローカル システムのコンテキストを使用して、自己証明書利用者のトークンを取得しようとします。 これは、AD FS がトークンを発行できる状態であることを確認する包括的なテストです。

このテストは多くの場合、正常性エージェントが AD FS ファーム名を解決できないために失敗します。 これは、AD FS サーバーがネットワーク ロード バランサーの背後にあり、要求が (ロード バランサーの前にある通常のクライアントではなく)、ロード バランサーの背後にあるノードから開始された場合に発生します。 これは、"C:\Windows\System32\drivers\etc" の下にある "hosts" ファイルを AD FS サーバーの IP アドレスまたは (sts.contoso.com など) AD FS ファーム名のループバック IP アドレス (127.0.0.1) を含めるよう更新して修正できます。 ホスト ファイルを追加すると、ネットワーク呼び出しが省かれ、正常性エージェントがトークンを取得できるようになります。

**Q:最近のランサムウェア攻撃の修正プログラムが自分のコンピューターに適用されていないと記載された電子メールを受信しました。なぜこの電子メールを受信したのですか。**

Azure AD Connect Health サービスでは、必要な修正プログラムがインストールされていることを確認するため、監視するすべてのコンピューターをスキャンします。 重要な修正プログラムが最低 1 台のコンピューターにインストールされていない場合、そのテナント管理者にその電子メールが送信されます。 この判断には、次のロジックが使用されます。
1. コンピューターにインストールされているすべての修正プログラムが検索されます。
2. 定義済み一覧にある修正プログラムが少なくとも 1 つがあることが確認されます。
3. ある場合、マシンは保護されています。 ない場合、マシンは攻撃される可能性があります。

このチェックは、次の PowerShell スクリプトを使用して、手動で実行できます。 上記のロジックが実装されます。

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**Q:PowerShell のコマンドレット <i>Get-MsolDirSyncProvisioningError</i> の場合、結果に表示される同期エラーが少ないのはなぜですか。**

<i>Get MsolDirSyncProvisioningError</i> では DirSync プロビジョニング エラーのみが返されます。 また、Connect Health ポータルには、エクスポート エラーなどの他の種類の同期エラーも表示されます。 これは、Azure AD Connect の差分結果と一致します。 詳細については、[Azure AD Connect 同期エラー](./tshoot-connect-sync-errors.md)に関するページを参照してください。

**Q:ADFS 監査が生成されていないのはなぜですか。**

<i>Get-AdfsProperties -AuditLevel</i> PowerShell コマンドレットを使用して、監査ログが無効な状態になっていないことを確認してください。 詳しくは、[ADFS 監査ログ](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016)に関する記事をご覧ください。 高度な監査設定が ADFS サーバーにプッシュされた場合、auditpol.exe でのすべての変更が上書きされることに注意してくださいます ("生成されたアプリケーション" が構成されていない場合のイベント)。 その場合は、ローカル セキュリティ ポリシーを "生成されたアプリケーション" の失敗と成功を記録するように設定してください。

**Q:エージェント認定資格証は有効期限が切れる前に自動更新されますか?**
エージェント認定資格証は、有効期限日の **6 か月** 前に自動更新されます。 更新されない場合は、エージェントのネットワーク接続が安定していることを確認してください。 エージェントのサービスを再起動する、または最新バージョンを更新してもこの問題を解決できる場合があります。



## <a name="related-links"></a>関連リンク
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health エージェントのインストール](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health の操作](how-to-connect-health-operations.md)
* [AD FS での Azure AD Connect Health の使用](how-to-connect-health-adfs.md)
* [Azure AD Connect Health for Sync の使用](how-to-connect-health-sync.md)
* [AD DS での Azure AD Connect Health の使用](how-to-connect-health-adds.md)
* [Azure AD Connect Health のバージョン履歴](reference-connect-health-version-history.md)
