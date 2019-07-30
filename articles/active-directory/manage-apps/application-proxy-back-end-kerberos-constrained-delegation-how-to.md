---
title: Application Proxy | Microsoft Docsのための、制限付き委任構成のトラブルシューティングの Kerberos
description: Application Proxy のための、制限付き委任構成のトラブルシューティング Kerberos
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c758b473dcdf36456bcc3569c18849488ad14983
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702654"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Application Proxy のための、制限付き委任構成のトラブルシューティング Kerberos

公開されたアプリケーションに対して SSO を実現するために使用可能な方法は、アプリケーションごとにやや異なる場合があります。 Azure Active Directory (Azure AD) アプリケーション プロキシ が既定で提供するオプションの 1 つに、Kerberos 制約付き委任 (KCD) があります。 ユーザーに代わってバックエンド アプリケーションに対する制約付き Kerberos 認証を実行するように、コネクタを構成することができます。

KCD を有効にするための手順は簡単です。 SSO をサポートするさまざまなコンポーネントおよび認証フローを解釈するのに、基本的な知識以上は必要ありません。 KCD SSO 期待どおりに機能しない場合があります。 これらのシナリオのトラブルシューティングのシナリオには、情報の適切なソースが必要です。

そこでこの記事では、きわめて一般的な問題のトラブルシューティングと独力での解決に役立つと思われる情報を集約して紹介します。 より複雑な実装の問題の診断についても説明します。

この記事は以下を前提としています。

- Azure アプリケーション プロキシのデプロイが[アプリケーション プロキシの開始](application-proxy-add-on-premises-application.md)および、非 KCD アプリケーション作業への一般的アクセスのそれぞれにおいて、正常に動作している。
- 公開されているターゲット アプリケーションが IIS と Microsoft による Kerberos の実装に基づいている。
- サーバーとアプリケーションのホストが単一の Azure Active Directory ドメインに存在する。 クロス ドメインとフォレストのシナリオの詳細については、[KCD ホワイトペーパー](https://aka.ms/KCDPaper)を参照してください。
- サブジェクト アプリケーションが事前認証が有効である Azure tenant で出版される。 ユーザーがフォーム ベース認証を使用して Azure へ認証されます。 リッチ クライアントの認証シナリオは、この記事で説明していません。 近い将来のどこかで、追加できます。

## <a name="prerequisites"></a>前提条件

Azure AD アプリケーション プロキシは、各種のインフラストラクチャや環境に展開できます。 アーキテクチャは、組織によって異なります。 KCD に関連する問題の最も一般的な原因は、環境がないことです。 単純な構成の不備や一般的なミスが、ほとんどの問題を発生させます。

このため、トラブルシューティングを開始する前に、[アプリケーション プロキシと KCD SSO を使用して](application-proxy-configure-single-sign-on-with-kcd.md)前提条件を満たしているかを確認することが最善策です。 2012R2 で Kerberos の制約付き委任の構成を行うことに関するセクションに注意してください。 このプロセスは、Windows の前のバージョンでKCDを構成するための異なるアプローチを使用します。 またこれらの懸念事項にも留意してください：

- ドメイン メンバーのサーバーが特定のドメイン コントローラー (DC) と安全なチャンネルのダイアログを開くことは、よくあります。 その後、特定に時点で、サーバーは別のダイアログボックスに移動することがあります。 したがって、コネクタのホストは、特定のローカル サイト DC と通信を持つことに制限はありません。
- 上記の点と同様、クロス ドメインのシナリオでは、参照によってローカルネットワークの境界外に存在する DC にコネクタホストが　誘導される場合があります。 これらの場合、他のそれぞれのドメインを代表する DC に、トラフィックを送信させることが、同じように重要です。 そうでないと、委任が失敗となります。
- 可能であれば、コネクタのホストと DC の間に、アクティブなIPSまたは IDS デバイスの配置することを回避します。 これらのデバイスは、時々、コアな RPC トラフィックにより、過度に邪魔されたり妨害されます。

単純なシナリオにおける委任をテスト。 一般に、不確定要素が多いほど、取り組むべき問題も多くなります。 時間を節約するには、テストを 1 つのコネクタに制限します。 問題が解決された後は、追加のコネクタを加えます。

いくつかの環境的要因が、問題の原因となる可能性もあります。 これらの要因を回避するのには、テスト中に、できるだけアーキテクチャを最小限に抑えます。 たとえば、正しく構成されていない内部ファイアウォール ACL は一般的です。 可能であれば、すべてのトラフィックを、,コネクタから、直接的に、 DC とバックエンド アプリケーションに送信します。

実際、コネクタは、そのターゲットにできるだけ近づけて配置するのが鉄則です。 テストするときに、インラインに存在するファイアウォールは、不必要に複雑を追加し、調査を長引かせることがあります。

何が、KCD 問題を示していますか？ KCD SSO が失敗しているいくつかの一般的な指示があります。 ブラウザーに、問題が発生した最初の兆候が表示されます。

![例:KCD の正しくない構成によるエラー](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![例:権限がないために承認に失敗](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

これらのイメージの両方が同じ現象であるSSO 失敗を示します。 アプリケーションへのユーザー アクセスが拒否されました。

## <a name="troubleshooting"></a>トラブルシューティング

トラブルシューティングの方法は、問題と実際の症状によって異なります。 先に行く前に、次の記事を探索します。 これらは、有用なトラブルシューティング情報を提供します：

- [アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](application-proxy-troubleshoot.md)
- [Kerberos のエラーと症状](application-proxy-troubleshoot.md#kerberos-errors)
- [オンプレミスと とクラウド ID が同一でない場合の SSO の操作](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

この点にたどり着くと、主な問題が存在します。 はじめに、トラブルシューティングしやすいよう、フローを 3 つの段階に分けましょう。

### <a name="client-pre-authentication"></a>クライアントの事前認証

外部ユーザーが Azure に対しブラウザー経由で認証を行います。 KCD SSO が正しく機能するためには、Azure に対する事前認証の成功が不可欠です。 問題がある場合は、テストしてこの問題に対処する必要があります。 事前認証段階は、KCD や公開されたアプリケーションとは無関係です。 対象のアカウントが Azure に存在し、不透明性をサニティ チェックすれば、不具合を修正するのは、それほど難しいことではありません。 また、無効かまたはブロックされていないかを確認します。 通常、ブラウザーに返されたエラー応答を見れば、原因を把握することができます。 わからない場合は、検証のために、他の Microsoft のトラブルシューティング記事を確認してください。

### <a name="delegation-service"></a>委任サービス

ユーザーから、Kerberos キー配布センター (KCD) からユーザーのために、 Kerberos サービス チケットを取得する Azure プロキシ コネクタです。

クライアントと Azure フロントエンドとの間の外部通信は、KCD の働きに負荷をかけるものではありません。 これらの通信は、KCD が動作することのみを確認します。 Kerberos チケットを取得するために使用される、有効なユーザー ID は、 Azure プロキシ サービスによって提供されます。 この ID を持たないと、 KCD は不可能であり、 失敗します。

既に述べたように、エラーの原因の手掛かりは、多くの場合、ブラウザーのエラー メッセージに含まれています。 アクティビティ ID と、応答内のタイムスタンプを確認してください。 この情報は、 Azure プロキシ イベント ログ内の実際のイベントに、動作を関連付けるために役立ちます。

![例:KCD の正しくない構成によるエラー](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

また、イベント ログ内に見られる、該当するエントリが、イベント 13019 またはイベント 12027 として記録されます。 **Microsoft**&gt;**AadApplicationProxy**&gt;**Connector**&gt;**Admin** の**アプリケーションおよびサービスログ**&gt; 内に、コネクタのイベントログを見つけます。

![イベント 13019 (アプリケーション プロキシのイベント ログ)](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![イベント 12027 (アプリケーション プロキシのイベント ログ)](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. 内部 DNS では、アプリケーションのアドレスに **CName** ではなく **A** レコードを使用します。
1. 指定されたターゲット アカウントの SPN を対象とした委任の権限がコネクタ ホストに付与されていることをもう一度確認します。 再確認**任意の認証プロトコルを使用する**が選択されていることを確認します。 このトピックの詳細については、[SSO 構成に関する記事](application-proxy-configure-single-sign-on-with-kcd.md)を参照してください。
1. Azure AD で、SPN の 1 つだけのインスタンスがあることを確認します。 `setspn -x`任意のドメインのメンバー ホストのコマンド プロンプトからの発行です。
1. [発行済みの Kerberos トークンの最大サイズ](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/)を制限するようにドメインポリシーが強化されていることを確認します。 このポリシーは、過剰な場合、コネクタがトークンを取得することを停止します。

問題をさらに深く掘り下げるためのに良い手順としては、コネクタ ホストとドメインの KDC との間の変更をネットワーク トレースでキャプチャすることです。 詳細については、[トラブルシューティングについて詳しく解説したホワイトペーパー](https://aka.ms/proxytshootpaper)を参照してください。

チケットの発行に問題がなさそうなら、アプリケーションから 401 が返されたために認証に失敗した、という内容のイベントをログで確認します。 このイベントは、対象のアプリケーションに、チケットが拒否されたことを示します。 次の段階へ移動します。

### <a name="target-application"></a>ターゲット アプリケーション

コネクタから提供された Kerberos チケットの消費者。 この段階で、コネクタが、Kerberos サービス チケットをバックエンドに送信することを予想します。 このチケットは、最初のアプリケーション要求のヘッダーです。

1. ポータルで定義されたアプリケーション内の URL を使用して、コネクタ ホストのブラウザからアプリケーションに直接アクセスできることを認証します。 その後、首尾良くサインインできます。 詳細は、コネクタの**トラブル シューティング**のページをご覧ください。
1. 引き続き、コネクタホストで、ブラウザとアプリケーション間の認証には、 Kerberos を使用していることを確認します。 次のうちの 1 つの行為を行ってください：
1. Internet Explorer で DevTools を働かせるか (**F12**)、またはコネクタホストから、 [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) を使用します。 内部 URL を使用して、アプリケーションに移動します。 アプリケーションからの応答として返された WWW 認証 ヘッダーを調べて、ネゴシエートまたは Kerberos が存在することを確認します。

   - ブラウザからアプリケーションへの応答で返される、次の Kerberos blobは、**YII**で開始します。 これらの文字は、Kerberos が実行されていることを確認します。 一方、Micrisod NT LAN Manager（NTLM） は、いつも**TlRMTVNTUAAB** で始まります。これは、Base64 からデコードされるときにNTLM Security Support Provider (NTLMSSP) を読みます。 blob が開始するときに、この **TlRMTVNTUAAB** を見るときは、Kerberos が利用できないことを意味します。 **TlRMTVNTUAAB** を見なければ、Kerberos は利用できる可能性があります。

      > [!NOTE]
      > Fiddler を使用する場合、この方法を利用するには、IIS のアプリケーション構成で拡張保護を一時的に無効化する必要があります。

      ![ブラウザーのネットワーク検査ウィンドウ](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - この図では、blob の先頭が **TIRMTVNTUAAB** で始まっていません。 この例では Kerberos が使用可能であり、および Kerberos blob は**YII** で始まりません。

1. IIS サイトのプロバイダーリストから、NTLM を一時的に削除します。 コネクタのホスト上で Internet Explorer から直接アプリにアクセスします。 NTLM は、プロバイダーの一覧上から、なくなりました。 Kerberos をのみを使用してアプリケーションにアクセスすることができます。 アクセスに失敗した場合、アプリケーションの構成の問題である可能性があります。 Kerberos 認証が機能していません。

   - Kerberos を使用できない場合は、IIS でアプリケーションの認証設定を確認します。 **Negotiate** がリストの最上部にあり、そのすぐ下に NTLM があることを確認します。 **Not Negotiate**が表示される場合、 **Kerberos またはネゴシエート**、または **PKU2U は、** Kerberos が機能するときのみ、存続します。

     ![Windows 認証プロバイダー](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Kerberos と NTLM が適切に配置された状態で、アプリケーションの事前認証を、ポータルで一時的に無効にします。 インターネットから外部 URL を使って、アプリケーションにアクセスできるかどうかを確認します。 認証情報が求められます。 前の手順で使用されるのと同じアカウントで、これを行うことができます。 それ以外の場合は、KCD はなく、バックエンド アプリケーションに問題があります。
   - ポータルで事前認証を再度有効にします。 その外部 URL を介して、アプリケーションに接続を試みることで、Azure による認証を行います。 SSO が失敗した場合、アクセス不可のエラー メッセージがブラウザーに表示され、ログにはイベント 13022 が記録されます：

     *Microsoft AAD Application Proxy Connector cannot authenticate the user because the backend server responds to Kerberos authentication attempts with an HTTP 401 error. (バックエンド サーバーが Kerberos 認証試行に対して HTTP 401 エラーを返すため、Microsoft AAD アプリケーション プロキシ コネクタはユーザーを認証できません。)*

      ![HTTTP 401 アクセス不可エラーが表示されます](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - IIS アプリケーションをチェックします。 構成済みのアプリケーション プールと SPN が、Azure AD で同じアカウントを使用するように構成されていることを確認します。 次の図に示されているように、IISをナビゲートします：

      ![IIS アプリケーションの構成ウィンドウ](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Id を確認した後は、このアカウントが、問題のSPN と構成されていることを確認します。 例: `setspn –q http/spn.wacketywack.com`。 コマンド プロンプト ウィンドウに次のテキストを入力します：

      ![SetSPN コマンド ウィンドウが表示されます](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - ポータルで、アプリケーションの設定に対して定義されている SPN を確認してください。 ターゲットの Azure AD のアカウントに対して構成されている同一の SPN が、アプリケーションのアプリケーション プールで使用されることを確認します。

      ![Azure Portal の SPN 構成](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - IIS に移動して、　アプリケーションのための、**構成エディター**のオプションを選択します。 **system.webServer/security/authentication/windowsAuthentication**にナビゲートします。 **UseAppPoolCredentials**の値は、**True**であることを確認してください。

      ![IIS の構成におけるアプリ プールの資格情報オプション](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      この値は**True**に変更できます。 次のコマンドを実行して、バックエンド サーバーからすべてのキャッシュされた Kerberos チケットを削除します：

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

詳細については、「[Purge the Kerberos client ticket cache for all sessions](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf)」 (すべてのセッションの Kerberos クライアント チケットのキャッシュをパージする) をご覧ください。

カーネル モードが有効な場合は、Kerberos 操作のパフォーマンスが向上します。 コンピューター アカウントを使用して復号化る要される、要求されたサービス チケットも生成されます。 このアカウントは、ローカル システムとも呼ばれます。 KCDを破壊するために、この値を **True**に設定します。 アプリケーションがファームの複数のサーバーにわたってホストされている場合です。

- 更なるチェックとして、 **拡張** 保護機能も無効にします。 一部のシナリオで、**拡張**保護が、特定の構成で有効化されたときに、保護が KCD を中断します。 こような場合、アプリケーションは、既定の web サイトのサブフォルダーとして発行されました。 このアプリケーションは、匿名認証のためのみに、構成されます。 すべてのダイアログ ボックスは淡色表示され、子オブジェクは、アクティブな設定を継承しないことを提案します。 テストするが、可能な限り、この値が**有効になっている**ように復元することを忘れないように推奨いたします。

  この追加のチェックは、公開されたアプリケーションの使用を追跡できます。 委任に構成されている追加のコネクタを、スピンアップすることができます。 詳細については、より詳細な技術ウォークスルー か、[Azure AD アプリケーション プロキシのトラブルシューティング](https://aka.ms/proxytshootpaper)をご覧ください。

より進化させることはできませんが、Microsoft がサポート致します。 ポータル内で直接サポート チケットを作成します。 エンジニアが連絡を差し上げます。

## <a name="other-scenarios"></a>その他のシナリオ

- Azure アプリケーション プロキシは、Kerberos チケットを要求したうえで、その要求をアプリケーションに送信します。 一部のサードパーティ製アプリケーションは、この認証方法を受け付けません。 これらのアプリケーションは、より従来のネゴシエーションを実行することが予想されます。 最初の要求は、匿名です。401 を介して、サポートするタイプの認証で応答することを、アプリケーションに許可します。
- ダブルホップ認証。アプリケーションが階層化されてバックエンドとフロントエンドがあり、どちらも認証を必要とするようなシナリオでこの認証が使用されます (SQL Reporting Services など)。 マルチホップ シナリオを構成するには、サポートの記事を参照してください。 [Kerberos 制約付き委任は、プロトコルの切り替えと複数ホップのシナリオを要求することがある](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul)の記事です。

## <a name="next-steps"></a>次の手順

[管理されたドメインで OU を作成する](../../active-directory-domain-services/deploy-kcd.md)。
