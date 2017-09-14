---
title: "Kerberos の制約付き委任を使用するようにアプリケーション プロキシ アプリケーションを構成する方法 | Microsoft Docs"
description: "Azure AD アプリケーション プロキシ アプリケーションに Kerberos の制約付き委任を構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 0651f497de446bd6b66f8eaccd7114d262925afd
ms.contentlocale: ja-jp
ms.lasthandoff: 06/03/2017

---

# <a name="how-to-configure-an-application-proxy-application-to-use-kerberos-constrained-delegation"></a>Kerberos の制約付き委任を使用するようにアプリケーション プロキシ アプリケーションを構成する方法

発行されたアプリケーションに対して SSO を実現する手法は、アプリケーションごとに異なる場合があります。手軽に SSO を実現する手段として Azure アプリケーション プロキシに用意されている選択肢の一つが、Kerberos の制約付き委任 (KCD) です。 この方法では、バックエンド アプリケーションに対し、ユーザーの代理で制約付きの Kerberos 認証を実行するようにコネクタ ホストが構成されます。

KCD を有効にするための実際の手順は比較的簡単です。要求される知識も、SSO の認証フローと各種コンポーネントについての基本的な理解さえあれば、通常は問題ありません。 しかし、KCD SSO が思いどおりに機能しない場合のトラブルシューティングに役立つ情報は、決して豊富とはいえません。

そこでこの記事では、きわめて一般的な問題のトラブルシューティングと独力での解決に役立つと思われる情報を集約して紹介します。 同時に、より複雑で問題の多い環境を診断するうえでの指針についても取り上げます。

この記事は、次のことを前提としています。

-   Azure アプリケーション プロキシが、Microsoft の[ドキュメント](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable)に従ってデプロイされ、非 KCD アプリケーションへの一般的なアクセスが正常に動作している。

-   発行されているターゲット アプリケーションが IIS と Microsoft による Kerberos の実装に基づいている。

-   サーバーとアプリケーションのホストが単一の Active Directory ドメインに存在する。 クロス ドメインとフォレストのシナリオの詳細については、[KCD のホワイトペーパー](http://aka.ms/KCDPaper)を参照してください。

-   対象アプリケーションは事前認証が有効な Azure テナントで発行され、ユーザーは Azure に対してフォーム ベース認証で本人確認を行う。 リッチ クライアント認証のシナリオは、この記事では取り上げませんが、将来追加される予定です。

## <a name="prerequisites"></a>前提条件

Azure アプリケーション プロキシは、かなり多くの種類のインフラストラクチャや環境にデプロイすることができるので、当然そのアーキテクチャは組織によって異なります。 KCD に関連した問題で最も多く見られる原因は、環境そのものではなく、単純な構成の間違いやうっかりミスです。

このような理由から、トラブルシューティングを開始する前に必ず、[アプリケーション プロキシでの KCD SSO の使用に関する記事](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)に記載されている前提条件をすべて満たしていることを確認してください。

Windows Server 2012 R2 における KCD の構成に関するセクションは特に重要です。KCD の構成に関して、以前のバージョンの Windows とは根本的に異なるアプローチが採用されているためです。その他、次の事柄についても十分考慮するようにしてください。

-   特定のドメイン コントローラーとのセキュリティで保護されたチャネル ダイアログをドメイン メンバー サーバーが開くことは珍しくありません。 いつでも別のダイアログに移動するため、通常コネクタ ホストは、特定のローカル サイトの DC とのみ通信できるという制約は受けません。

-   上記の点と同様、クロス ドメインのシナリオでは、参照によってローカル ネットワークの境界外に存在する DC にコネクタ ホストが誘導される場合があります。 このシナリオでは、他の各ドメインの DC に向かうトラフィックを許可することも重要となります。そうしなければ委任に失敗します。

-   可能であれば、コネクタ ホストと DC との間にアクティブな IPS/IDS デバイスを配置することは避けてください。これらのデバイスによって過剰な措置が講じられ、重要な RPC トラフィックが遮断されることが少なくありません。

問題はすばやく効率的に解決したいものですが、時間がかかる場合もあります。できる限り、委任の試行とテストは、ごく単純化したシナリオで行うようにしてください。 一般に、不確定要素が多いほど、取り組むべき問題も多くなります。 たとえばテストを単一のコネクタに限定すれば貴重な時間を節約でき、問題が解決された後で新たにコネクタを追加することができます。

いくつかの環境要因が問題の一因となっている可能性もあります。繰り返しになりますが、可能な限りアーキテクチャを単純化してテストを行うようにしてください。 たとえば、内部ファイアウォールの ACL が正しく構成されていないケースは多く見られます。可能であれば、コネクタからのすべてのトラフィックを直接 DC とバックエンド アプリケーションに流すようにしてください。 

実際、コネクタは、そのターゲットにできるだけ近づけて配置するのが鉄則です。 ファイアウォールを接続した状態でテストすると不要な複雑さを招き、調査が長引く結果となります。

KCD の問題にはどのようなものがあるのでしょうか。 問題の最初の兆候は通常、ブラウザーに現れます。以下に、KCD SSO 失敗のいくつかの典型的な症状を紹介します。

   ![KCD の正しくない構成によるエラー](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![権限がないために承認に失敗](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

いずれも症状は同じで SSO に失敗しており、結果的にユーザーはアプリケーションへのアクセスを拒否されています。

## <a name="troubleshooting"></a>トラブルシューティング

トラブルシューティングの方法は、問題と実際の症状によって異なります。 先に進む前に、次のリンク先を参照することをお勧めします。まだご覧になっていない役立つ情報があるかもしれません。

-   [アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)

-   [Kerberos のエラーと症状](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#kerberos-errors)

-   [オンプレミス ID とクラウド ID が同一でない場合の SSO の操作](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd#working-with-sso-when-on-premises-and-cloud-identities-are-not-identical)

ここまできたら主な問題は間違いなく存在します。 さらに掘り下げる必要があります。そこで、トラブルシューティングしやすいよう、フローを 3 つの段階に分けましょう。

**クライアント事前認証** - 外部ユーザーが Azure に対しブラウザー経由で認証を行う。

KCD SSO が正しく機能するためには、Azure に対する事前認証の成功が不可欠です。 まずこの点を確認し、問題があれば解決しておく必要があります。 事前認証段階は、KCD や発行されたアプリケーションとは無関係です。 対象のアカウントが Azure に存在し、無効化/ブロックされていないことをサニティ チェックすれば、不具合を修正するのは、それほど難しいことではありません。 通常、ブラウザーに返されたエラー応答を見れば原因を把握することができます。 確信が持てない場合は、Microsoft から提供されている他のトラブルシューティング ドキュメントも見て検証してください。

**委任サービス** - Azure プロキシ コネクタがユーザーの代理で、KDC (Kerberos Distribution Center) から Kerberos サービス チケットを取得する。

クライアントと Azure フロントエンドとの間の外部通信は、KCD にまったく関係がありませんが、KCD の働きを担保するようなものでなければなりません。 Kerberos チケットを取得するための有効なユーザー ID を Azure プロキシ サービスに提供できるようにするためです。 それがなければ、KCD は成立せず、失敗することになるでしょう。

既に述べたように、エラーの原因の手掛かりは、多くの場合、ブラウザーのエラー メッセージに含まれています。 必ず、応答に含まれているアクティビティ ID とタイムスタンプをメモに書き留めてください。Azure プロキシのイベント ログで、発生した挙動と実際のイベントとの相関性を把握することができます。

   ![KCD の正しくない構成によるエラー](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

また、イベント ログには、該当するエントリがイベント 13019 またはイベント 12027 として記録されます。 コネクタのイベント ログは、**[アプリケーションとサービス ログ]** &gt; **[Microsoft]** &gt; **[AadApplicationProxy]** &gt; **[コネクタ]** &gt; **[管理者]** の順に移動して確認できます。

   ![イベント 13019 (アプリケーション プロキシのイベント ログ)](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![イベント 12027 (アプリケーション プロキシのイベント ログ)](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   内部 DNS では、アプリケーションのアドレスに CName ではなく A レコードを使用します。

-   指定されたターゲット アカウントの SPN を対象とした委任の権限がコネクタ ホストに付与されていること、また **[任意の認証プロトコルを使う]** が選択されていることをもう一度確認します。 この点については、[SSO の構成に関する記事](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)で取り上げています。

-   該当する SPN のインスタンスが AD に 1 つか存在しないことを確認します。ドメイン メンバーとなっている任意のホストのコマンド プロンプトから **setspn -x** を実行してください。

-   [発行する Kerberos トークンの最大サイズ](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/)を制限するためのドメイン ポリシーが適用されているかどうかを確認します。このポリシーで制限を超えていると見なされたトークンは、コネクタが取得できません。

問題をさらに深く掘り下げるための次の手順としては、コネクタ ホストとドメインの KDC との間で交わされるデータをネットワーク トレースでキャプチャすることになるでしょう。 この点については、[トラブルシューティングについて詳しく解説したホワイト ペーパー](https://aka.ms/proxytshootpaper)を参照してください。

チケットの発行に問題がなさそうなら、アプリケーションから 401 が返されたために認証に失敗した、という内容のイベントをログで確認します。 これは通常、ターゲット アプリケーションによってチケットが拒否されていることを示すので、次の段階に進むことになります。

**ターゲット アプリケーション** - コネクタから提供された Kerberos チケットを利用する。

この段階では既に、最初のアプリケーション要求内のヘッダーとしてコネクタからバックエンドに Kerberos サービス チケットが送信済みであると考えられます。

-   ポータルで定義されたアプリケーションの内部 URL を使用して、コネクタ ホスト上のブラウザーから直接アプリケーションにアクセスできることを確認します。 その後、正常にログインすることができます。 この点については、コネクタのトラブルシューティング ページに詳しい説明があります。

-   引き続きコネクタ ホストで、ブラウザーとアプリケーション間の認証に Kerberos が使われていることを次のいずれかの方法で確認します。

1.  コネクタ ホストから [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) を使用するか、Internet Explorer で開発ツール (**F12**) を実行します。 アプリケーションに内部 URL で移動し、アプリケーションからの応答として返された WWW authorization ヘッダーを調べて、ネゴシエートまたは Kerberos が存在することを確認します。 その後ブラウザーからアプリケーションへの応答として返される Kerberos BLOB は通常、**YII** で始まります。この例では、Kerberos が機能しているようすがうかがえます。 一方、NTLM は必ず **TlRMTVNTUAAB** で始まります (Base64 からデコードされるときに NTLMSSP になります)。 この BLOB が **TlRMTVNTUAAB** で始まっている場合は、Kerberos が**利用できない**ことを意味します。 そのようになっていなければ、Kerberos は利用できる可能性があります。

  * Fiddler を使用する場合、この方法を利用するには、IIS のアプリケーション構成で拡張保護を一時的に無効化する必要があります。

     ![ブラウザーのネットワーク検査ウィンドウ](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *図:* これは、先頭が TIRMTVNTUAAB ではないことから Kerberos は利用可能な状態です。 これは先頭が YII ではない Kerberos BLOB の例です。

2.  IIS サイトのプロバイダー リストから一時的に NTLM を削除し、コネクタ ホスト上の IE から直接アプリにアクセスします。 プロバイダー リストに NTLM がなければ、Kerberos のみを使用してアプリケーションにアクセスできるはずです。 もし失敗するようなら、アプリケーションの構成に問題があり、Kerberos 認証が正常に機能していないことが考えられます。

Kerberos が利用できない場合は、IIS でアプリケーションの認証設定をチェックし、Negotiate が一番上に、そのすぐ下に NTLM が列挙されていることを確認します  (Negotiate:kerberos や Negotiate:PKU2U ではないこと)。 Kerberos が機能している場合にのみ先に進んでください。

   ![Windows 認証プロバイダー](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   Kerberos と NTLM が適切に配置された状態で、アプリケーションの事前認証をポータルで一時的に無効にします。 インターネットから外部 URL を使ってアプリケーションにアクセスできるかどうかを確認します。 認証を求めるメッセージが表示されます。前の手順と同じアカウントで認証を行うことができるはずです。 それができない場合は、バックエンド アプリケーションの問題であり、KCD にはまったく問題がないと考えられます。

-   ポータルで事前認証を再度有効にし、アプリケーションにその外部 URL を使って接続を試みることで、Azure に対する認証を行います。 SSO が失敗した場合、アクセス不可のエラー メッセージがブラウザーに表示され、ログにはイベント 13022 が記録されます。

    *Microsoft AAD Application Proxy Connector cannot authenticate the user because the backend server responds to Kerberos authentication attempts with an HTTP 401 error. (バックエンド サーバーが Kerberos 認証試行に対して HTTP 401 エラーを返すため、Microsoft AAD アプリケーション プロキシ コネクタはユーザーを認証できません。)*

    ![HTTTP 401 アクセス不可エラー](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   IIS アプリケーションに設定されているアプリケーション プールが、AD で SPN を構成したときと同じアカウントを使うように構成されていることを確認します。IIS での操作手順を次の図に示します。

    ![IIS アプリケーションの構成ウィンドウ](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    ID が判明したら、そのアカウントが当該の SPN を使って構成されていることに間違いがないか、コマンド プロンプトから以下のコマンドを実行して確認します。 たとえば「**setspn ? q http/spn.wacketywack.com**」と入力します。

    ![SetSPN コマンド ウィンドウ](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   ポータルのアプリケーション設定に対して定義された SPN が、アプリケーションのアプリケーション プールで使用されているターゲット AD アカウントに対して構成されている SPN と同じであることを確認します。

   ![Azure Portal の SPN 構成](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   IIS に移動してアプリケーションの **[構成エディター]** オプションを選択し、**system.webServer/security/authentication/windowsAuthentication** に移動して、**UseAppPoolCredentials** が true に設定されていることを確認します。

   ![IIS の構成におけるアプリ プールの資格情報オプション](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

カーネル モードを有効にすることは、Kerberos の動作パフォーマンスを高めるうえでは効果的ですが、その場合、要求したサービスのチケットの暗号化解除に "コンピューター アカウント" が使用されます。 これは "ローカル システム" とも呼ばれます。これが true に設定されていると、ファーム内の複数のサーバーにアプリケーションをホストしたときに KCD が破綻します。

-   もう 1 つのチェックとして、**拡張**保護を無効にすることもできます。 ごく限られた (既定の Web サイトのサブフォルダーとしてアプリケーションが発行される) 構成で、これを有効にしたときに KCD に支障が生じた事例が過去にありました。 これ自体は匿名認証でのみ構成されます。ダイアログ全体が灰色表示され、アクティブな設定は一切、子オブジェクトに継承されないことがわかります。 ただし可能であれば、これを有効にすることをお勧めします。つまりテストではそのようにしますが、忘れずに有効に戻すようにしてください。

こうしたチェックを重ねていけば、発行されたアプリケーションを使用できる状態になるでしょう。 コネクタをさらに追加して委任を構成することができます。しかし、問題が一向に解消されない場合は、詳細なテクニカル チュートリアル ([Azure AD アプリケーション プロキシのトラブルシューティングに関する完全ガイド](https://aka.ms/proxytshootpaper)) を一読されることをお勧めします。

それでも問題が解消しない場合は、遠慮なくサポートにお問い合わせください。 ポータル内から直接サポート チケットを作成していただければ、追ってエンジニアがご連絡差し上げます。

## <a name="other-scenarios"></a>その他のシナリオ

-   Azure アプリケーション プロキシは、Kerberos チケットを要求したうえで、その要求をアプリケーションに送信します。 一部のサード パーティ アプリケーション (Tableau Server など) は、このような認証方法に十分対応しておらず、従来のネゴシエーションが必要となります。 最初の要求が匿名となり、アプリケーションは 401 を介し、サポートしている認証の種類で応答することができます。

-   ダブルホップ認証。アプリケーションが階層化されてバックエンドとフロントエンドがあり、どちらも認証を必要とするようなシナリオでこの認証が使用されます (SQL Reporting Services など)。

## <a name="next-steps"></a>次のステップ
[管理対象ドメインで Kerberos の制約付き委任 (KCD) を構成する](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-enable-kcd)

