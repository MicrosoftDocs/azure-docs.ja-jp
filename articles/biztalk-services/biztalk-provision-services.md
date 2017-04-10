---
title: "Azure Portal で Azure BizTalk Services を作成する | Microsoft Docs"
description: "Azure ポータルで BizTalk Services をプロビジョニングし、作成する方法について説明します。MABS、WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 9cf1faabe3ea12af0ee5fd8a825975e30947b03a
ms.openlocfilehash: 12606d312ba95d9ef73e988fa4677a8314f9a579


---
# <a name="create-biztalk-services-using-the-azure-portal"></a>Azure ポータルを使用して BizTalk Services を作成する

> [!TIP]
> Azure ポータルにサインインするには、Azure アカウントと Azure サブスクリプションが必要です。 アカウントがない場合は、無料の試用アカウントを数分で作成することができます。 [Azure 無料評価版のサイト](http://go.microsoft.com/fwlink/p/?LinkID=239738)を参照してください。
> 
> 

## <a name="create-a-biztalk-service"></a>BizTalk サービスを作成する
選択したエディションによっては、BizTalk サービスの設定の一部が使用できないことがあります。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 下部のナビゲーション ウィンドウで、**[新規]** をクリックします。  
   ![[新規] をクリック][NEWButton]
3. **[アプリケーション サービス]**  >  **[BizTalk サービス]**  >  **[カスタム作成]** の順に選択します。  
   ![[BizTalk サービス]、[カスタム作成] の順にクリックします。][NewBizTalkService]
4. BizTalk サービスの設定を入力します。
   
    <table border="1">
    <tr>
    <td><strong>BizTalk サービス名</strong></td>
    <td>どのような名前でも入力できますが、具体的な名前を入力してください。 次に例をいくつか示します。<br/><br/>
    <em>mycompany</em>.biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>.biztalk.windows.net<br/>
    <em>myapplication</em>.biztalk.windows.net<br/><br/>入力した名前には ".biztalk.windows.net" が自動的に追加されます。 これで、この BizTalk サービスにアクセスするための URL が作成され、たとえば <strong>https://<em>myapplication</em>.biztalk.windows.net</strong> となります。
    </td>
    </tr>
    <tr>
    <td><strong>エディション</strong></td>
    <td>テスト/開発フェーズで作業している場合は、<strong>開発者</strong>を選択します。 現在のフェーズが運用フェーズの場合は、<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk サービス: エディションのチャート</a>を使用して、<strong>Premium</strong>、<strong>Standard</strong>、<strong>Basic</strong> のどれが実際のビジネス シナリオに最適であるかを判断してください。
    </td>
    </tr>
    <tr>
    <td><strong>[リージョン]</strong></td>
    <td>BizTalk サービスをホストするリージョンを選択します。</td>
    </tr>
    <tr>
    <td><strong>ドメイン URL</strong></td>
    <td><strong>省略可能</strong>。 既定では、ドメイン URL は <em>YourBizTalkServiceName</em>.biztalk.windows.net です。 カスタム ドメインを入力することもできます。 たとえば、ドメインが <em>contoso</em> である場合は、次のように入力できます。 <br/><br/>
    <em>MyCompany</em>.contoso.com<br/>
    <em>MyCompanyMyApplication</em>.contoso.com<br/>
    <em>MyApplication</em>.contoso.com<br/>
    <em>YourBizTalkServiceName</em>.contoso.com<br/>
    </td>
    </tr>
    </table>
   次へ進む矢印を選択します。
5. ストレージとデータベースの設定を入力します。
   
    <table border="1">
    <tr>
    <td><strong>ストレージ アカウントの監視/アーカイブ</strong></td>
    <td>既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成します。 <br/><br/>新しいストレージ アカウントを作成する場合は、<strong>[ストレージ アカウント名]</strong> に入力します。</td>
    </tr>
    <tr>
    <td><strong>トラッキング データベース</strong></td>
    <td>既存の Azure SQL データベースを使用する場合に、そのデータベースを別の BizTalk サービスでも使用することはできません。 Azure SQL データベース サーバーの作成時に入力したログイン名とパスワードが必要です。<br/><br/><strong>ヒント</strong> トラッキング データベースと監視/アーカイブ ストレージ アカウントを BizTalk サービスと同じリージョンに作成します。</td>
    </tr>
    </table>
   次へ進む矢印を選択します。
6. データベースの設定を入力します。
   
    <table border="1">
    <tr>
    <td><strong>名前</strong></td>
    <td>前の画面で <strong>[新しい SQL データベース インスタンスを使用する]</strong> を選択した場合に指定できます。
    <br/><br/>
    BizTalk サービスで使用される SQL データベース名を入力します。</td>
    </tr>
    <tr>
    <td><strong>サーバー</strong></td>
    <td>前の画面で <strong>[新しい SQL データベース インスタンスを使用する]</strong> を選択した場合に指定できます。
    <br/><br/>
    既存の SQL データベース サーバーを選択するか、新しい SQL データベース サーバーを作成します。</td>
    </tr>
    <tr>
    <td><strong>サーバー ログイン名</strong></td>
    <td>ログイン ユーザー名を入力します。</td>
    </tr>
    <tr>
    <td><strong>サーバー ログイン パスワード</strong></td>
    <td>ログイン パスワードを入力します。</td>
    </tr>
    <tr>
    <td><strong>[リージョン]</strong></td>
    <td><strong>[新しい SQL データベース インスタンスを作成する]</strong> を選択した場合に指定可能になります。 SQL データベースをホストするリージョンを選択します。</td>
    </tr>
    </table>

チェック マークをオンにして、ウィザードを完了します。 進行状況アイコンが表示されます。  
![完了時に表示される進行状況アイコン][ProgressComplete]

完了すると、Azure BizTalk サービスが作成され、アプリケーションで使用できる状態になります。 既定の設定で十分です。 既定の設定を変更する場合は、左側のナビゲーション ウィンドウで **[BizTalk Services]** を選択し、BizTalk サービスを選択します。 追加の設定が、一番上の [[ダッシュボード]、[監視]、および [スケール] の各タブ](biztalk-dashboard-monitor-scale-tabs.md) に表示されます。

BizTalk サービスの状態によっては、一部の操作を完了できません。 これに該当する操作の一覧については、 [BizTalk サービスの状態のチャート](biztalk-service-state-chart.md)を参照してください。

## <a name="post-provisioning-steps"></a>プロビジョニング後の手順
* [証明書をローカル コンピューターにインストールする](#InstallCert)
* [運用対応証明書を追加する](#AddCert)
* [Access Control 名前空間を取得する](#ACS)

#### <a name="a-nameinstallcertainstall-the-certificate-on-a-local-computer"></a><a name="InstallCert"></a>証明書をローカル コンピューターにインストールする
BizTalk サービスをプロビジョニングするときに、自己署名証明書が作成されて BizTalk サービス サブスクリプションに関連付けられます。 コンピューターから BizTalk サービス アプリケーションをデプロイする、または BizTalk サービス エンドポイントにメッセージを送信するには、この証明書をダウンロードしてそのコンピューターにインストールする必要があります。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左側のナビゲーション ウィンドウで **[BizTalk Services]** を選択し、BizTalk サービス サブスクリプションを選択します。
3. **[ダッシュボード]** タブを選択します。
4. **[SSL 証明書のダウンロード]** を選択します。  
   ![SSL 証明書の変更][QuickGlance]
5. 証明書をダブルクリックし、ウィザードを最後まで実行して証明書をインストールします。 この証明書は必ず **[信頼されたルート証明機関]** ストアにインストールしてください。

#### <a name="a-nameaddcertaadd-a-production-ready-certificate"></a><a name="AddCert"></a>運用対応証明書を追加する
BizTalk サービスの作成時に自動的に作成される自己署名証明書は、開発環境に限定して使用するためのものです。 本稼働のときは、この証明書を運用対応証明書で置き換えます。

1. **[ダッシュボード]** タブで、**[SSL 証明書の更新]** を選択します。
2. BizTalk サービス名が含まれているプライベート SSL 証明書 (*CertificateName*.pfx) を一覧から選択し、パスワードを入力して、チェック マークをクリックします。

#### <a name="a-nameacsaget-the-access-control-namespace"></a><a name="ACS"></a>Access Control 名前空間を取得する
1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左側のナビゲーション ウィンドウで **[BizTalk Services]** を選択し、BizTalk サービスを選択します。
3. タスク バーで **[接続情報]** をクリックします。  
   ![[接続情報] の選択][ACSConnectInfo]
4. Access Control の値をコピーします。

Visual Studio から BizTalk サービス プロジェクトを展開するときは、この Access Control 名前空間を入力します。 BizTalk サービスの Access Control 名前空間が自動的に作成されます。

Access Control の値は、どのアプリケーションでも使用できます。 Azure BizTalk サービスが作成されると、この Access Control 名前空間によって BizTalk サービス デプロイメントでの認証が制御されます。 サブスクリプションを変更する場合や名前空間を管理する場合は、左側のナビゲーション ウィンドウで **[Active Directory]** をクリックし、名前空間を選択します。 タスク バーに、選択可能なオプションが表示されます。

**[管理]** をクリックすると、Access Control 管理ポータルが開きます。 Access Control 管理ポータルでは、BizTalk サービスにより**サービス ID** が使用されます。  
![Access Control 管理ポータルにおける ACS サービス ID][ACSServiceIdentities]

Access Control サービス ID は、アプリケーションまたはクライアントが Access Control で直接認証してトークンを受け取るための一連の資格情報です。

> [!IMPORTANT]
> BizTalk サービスでは、既定のサービス ID の**所有者**と**パスワード**値が使用されます。 パスワード値の代わりに対称キー値を使用した場合、次のエラーが発生する可能性があります。<br/><br/>"*指定された資格情報を使用して Access Control 管理サービス アカウントに接続できませんでした*"
> 
> 

[ACS 名前空間の管理](https://msdn.microsoft.com/library/azure/hh674478.aspx) に関するページを参照してください。

## <a name="requirements-explained"></a>要件の説明
これらの要件は、無料エディションには適用されません。

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>必要なもの</strong></td>
        <td><strong>必要である理由</strong></td>
</tr>
<tr>
<td>Azure サブスクリプション</td>
<td>どのユーザーが Azure ポータルにサインインできるかは、サブスクリプションによって決まります。 アカウント保有者がサブスクリプションを <a HREF="https://account.windowsazure.com/Subscriptions">Azure サブスクリプションのページ</a>で作成します。
<br/><br/>
1 つの Azure アカウントで複数のサブスクリプションを契約することもでき、管理は許可を受けたユーザーであればだれでも行うことができます。 たとえば、Azure アカウント保有者が <em>BizTalkServiceSubscription</em> という名前のサブスクリプションを作成して、社内の BizTalk 管理者 (たとえば ContosoBTSAdmins@live.com)) にこのサブスクリプションへのアクセスを許可するとします。 このシナリオでは、BizTalk 管理者は、Azure ポータルにサインインすると、Azure BizTalk Services を含む、サブスクリプション内のすべてのホステッド サービスに対する完全な管理者権限を持つことになります。 BizTalk 管理者は Azure アカウント保有者ではないため、課金情報へはアクセスできません。
<br/><br/>詳細については、
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Azure Portal でのサブスクリプションとストレージ アカウントの管理</a>に関するページをご覧ください。
</td>
</tr>
<tr>
<td>Azure SQL Database</td>
<td>BizTalk サービスで使用されるテーブル、ビュー、およびストアド プロシージャが格納されます。これには、追跡データも含まれます。
<br/><br/>
BizTalk サービスを作成するときは、既存の Azure SQL Server や Azure SQL Database を使用するか、新しいサーバーやデータベースを自動的に作成することができます。
<br/><br/>
SQL Database のスケールは自動的に構成されます。 一般的に、BizTalk サービスのスケールは既定の設定で十分です。 スケールを変更すると、料金に影響が及びます。 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">Azure SQL Database のアカウントと課金</a>に関するページをご覧ください
<br/><br/>
<strong>メモ</strong>
<br/>
<ul>
<li> 新しい Azure SQL サーバーとデータベースを作成すると、Azure サービスが自動的に有効になります。 BizTalk サービスを使用するには、Azure サービスが有効になっている必要があります。</li>
<li>既存の Azure SQL サーバー上に新しい Azure SQL データベースを作成した場合に、サーバーのファイアウォール ルールが変更されることはありません。 その結果として、他の Azure サービスがこのサーバーのデータベースにアクセスできなくなる可能性があります。</li>
</ul>
</td>
</tr>
<tr>
<td>Azure の Access Control 名前空間</td>
<td>Azure BizTalk サービスで認証を行います。 Visual Studio から BizTalk サービス プロジェクトを展開するときは、この Access Control 名前空間を入力します。 BizTalk サービスを作成すると、Access Control 名前空間が自動的に作成されます。</td>
</tr>

<tr>
<td>Azure ストレージ アカウント</td>
<td>次のような BizTalk サービスのデータを保存するためのテーブル、BLOB、およびキューにアクセスできるようになります。

<ul>
<li>BizTalk サービスを監視するログ ファイル。 監視出力は Azure Portal の **[監視]** タブにも表示されます。</li>
<li>パートナー間で X12 または AS2 契約を作成するときに、アーカイブ機能を有効にするとメッセージのプロパティを保存できます。 このデータは、このストレージ アカウントに保存されます。</li>
</ul>
<br/>
BizTalk サービスを作成するときに、既存のストレージ アカウントを使用することも、新しいストレージ アカウントを自動的に作成することもできます。
<br/><br/>
BizTalk サービスのストレージ設定は、既定の設定で十分です。
<br/><br/>
ストレージ アカウントを作成すると、プライマリ キーおよびセカンダリ キーが自動的に作成されます。 これらのキーは、ストレージ アカウントへのアクセスを制御します。 BizTalk サービスは自動的にプライマリ キーを使用します。
<br/><br/>
詳細については、<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">Storage</a> に関するページをご覧ください。
</td>
</tr>

<tr>
<td>SSL プライベート証明書</td>
<td>
Azure BizTalk サービスが作成されるときに、BizTalk サービス名が含まれている HTTPS URL も作成されます。 この URL は自動的に、開発専用の自己署名証明書を使用するように構成されます。 本稼働用には、プライベート SSL 証明書が必要です。
<br/><br/>
<strong>重要な SSL 証明書情報</strong>

<ul>
<li>証明書の有効期限は、5 年未満にしてください。</li>
<li>プライベート証明書はすべて、パスワードを必要とします。 このパスワードを知っている必要があります。また、他の管理者にこのパスワードを知らせておくことをお勧めします。</li>
<li>自己署名証明書は、テスト/開発環境で使用されます。 自己署名証明書を使用するときは、証明書を [個人用] 証明書ストアと [信頼されたルート証明機関] 証明書ストアにインポートします。</li>
</ul>
<br/>運用証明書要求を証明機関に送信するときに、次の証明書プロパティを指定します。
<br/>

<ul>
<li><strong>拡張キー使用法</strong>: 少なくとも、Azure BizTalk サービスにはサーバー認証が必要です。</li>
<li><strong>共通名</strong>: Azure BizTalk サービス URL の完全修飾ドメイン名 (FQDN) を入力します。 この記事の「<a HREF="#BizTalk">BizTalk サービスを作成する</a>」を参照してください。</li>
</ul>
<br/>
BizTalk サービスが作成された後で、新しい、または別の証明書を追加できます。
</td>
</tr>
</table>



## <a name="hybrid-connections"></a>ハイブリッド接続と
Azure BizTalk サービスを作成するときに、 **[ハイブリッド接続]** タブが使用可能になります。

![[ハイブリッド接続] タブ][HybridConnectionTab]

"ハイブリッド接続" を使用すると、Azure Web サイトや Azure モバイル サービスと、静的 TCP ポートを使用する内部設置型リソース (たとえば SQL Server、MySQL、HTTP Web API、Mobile Services、ほとんどのカスタム Web サービス) とを接続できます。  ハイブリッド接続と BizTalk Adapter サービスは別のものです。 BizTalk Adapter サービスは、Azure BizTalk サービスを内部設置型の基幹業務 (LOB) システムに接続するために使用されます。

 ハイブリッド接続の作成と管理の方法などの情報については、「 [ハイブリッド接続](integration-hybrid-connection-overview.md) 」を参照してください。

## <a name="next-steps"></a>次のステップ
BizTalk サービスが作成されたので、 [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ](biztalk-dashboard-monitor-scale-tabs.md)のページでこれらのタブについての理解を深めてください。 BizTalk サービスをアプリケーションで使用する準備ができました。 アプリケーションの作成を開始するには、 [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197)に関するページを参照してください。

## <a name="see-also"></a>関連項目
* [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](biztalk-editions-feature-chart.md)<br/>
* [BizTalk サービス: 状態のチャート](biztalk-service-state-chart.md)<br/>
* [BizTalk サービス: バックアップと復元に関するページ](biztalk-backup-restore.md)<br/>
* [BizTalk サービス: 調整](biztalk-throttling-thresholds.md)<br/>
* [BizTalk サービス: 発行者名および発行者キー](biztalk-issuer-name-issuer-key.md)<br/>
* [Visual Studio でのプロジェクトの作成](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [ハイブリッド接続](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png



<!--HONumber=Nov16_HO3-->


