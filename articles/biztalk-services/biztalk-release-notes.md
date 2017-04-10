---
title: "Azure BizTalk Services のリリース ノート | Microsoft Docs"
description: "Azure BizTalk Services の既知の問題を一覧にまとめています"
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9cf1faabe3ea12af0ee5fd8a825975e30947b03a
ms.openlocfilehash: 84ad965ed6ef1711fda983220cf004fdd48d290d


---
# <a name="release-notes-for-azure-biztalk-services"></a>Azure BizTalk Services のリリース ノート
Microsoft Azure BizTalk Services リリース ノートでは、このリリースの既知の問題について説明します。

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>BizTalk Services の 11 月の更新における新機能
* BizTalk Services ポータルで保存時の暗号化を有効にすることができます。 「 [BizTalk Services ポータルで保存時の暗号化を有効にする](https://msdn.microsoft.com/library/azure/dn874052.aspx)」を参照してください。

## <a name="update-history"></a>更新履歴
### <a name="october-update"></a>10 月の更新
* 組織アカウントが次のようにサポートされます。  
  * **シナリオ**: Microsoft アカウント (user@live.com). など) を使用して BizTalk サービスのデプロイを登録した。このシナリオでは、Microsoft アカウント ユーザーのみが、BizTalk Services ポータルを使用してその BizTalk サービスを管理できます。 組織アカウントは使用できません。  
  * **シナリオ**: Azure Active Directory の組織アカウント (user@fabrikam.com、user@contoso.com). など) を使用して BizTalk サービスのデプロイを登録した。このシナリオでは、同じ組織内の Azure Active Directory ユーザーのみが BizTalk Services ポータルを使用してその BizTalk サービスを管理できます。 Microsoft アカウントは使用できません。  
* Azure クラシック ポータルで BizTalk サービスを作成すると、ユーザーは BizTalk Services ポータルに自動的に登録されます。
  * **シナリオ**: Azure クラシック ポータルにサインインして、BizTalk サービスを作成した後、初めて **[管理]** を選択した。 BizTalk Services ポータルが開くと、BizTalk サービスが自動的に登録され、デプロイできる状態になります。  
    「 [BizTalk Services ポータルでの BizTalk サービス配置の登録と更新](https://msdn.microsoft.com/library/azure/hh689837.aspx)」を参照してください。  

### <a name="august-14-update"></a>8 月 14 日の更新
* 契約とブリッジの分離 - BizTalk Services ポータルで取引先契約とブリッジが分離されました。 契約とブリッジを個別に作成するようになりました。実行時には EDI メッセージの値に基づいてブリッジが契約に解決されます。 「[Azure BizTalk Services でのアグリーメントの作成](https://msdn.microsoft.com/library/azure/hh689908.aspx)」、「[BizTalk Services ポータルを使用した EDI ブリッジの作成](https://msdn.microsoft.com/library/azure/dn793986.aspx)」、「[BizTalk Services ポータルを使用した AS2 ブリッジの作成](https://msdn.microsoft.com/library/azure/dn793993.aspx)」、および「[実行時にブリッジをアグリーメントに解決する操作](https://msdn.microsoft.com/library/azure/dn794001.aspx)」を参照してください。  
* 契約のテンプレートを作成するためのオプションが廃止されました。  
* 送信側契約は、スキーマごとに異なる区切り記号セットを指定できるようになりました。 この構成は、送信側契約のプロトコル設定で指定します。 詳細については、「[Azure BizTalk Services での X12 アグリーメントの作成](https://msdn.microsoft.com/library/azure/hh689847.aspx)」および「[Azure BizTalk Services で EDIFACT アグリーメントを作成する](https://msdn.microsoft.com/library/azure/dn606267.aspx)」を参照してください。 同じ目的のために、TPM OM API にも 2 つの新しいエンティティが追加されました。 「[X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx)」および「[EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx)」を参照してください。  
* 派生型を含む標準 XSD コンストラクトがサポートされるようになりました。 「[マップでの標準 XSD コンストラクトの使用](https://msdn.microsoft.com/library/azure/dn793987.aspx)」および「[マッピングのシナリオと例で派生型を使用する](https://msdn.microsoft.com/library/azure/dn793997.aspx)」を参照してください。  
* AS2 でメッセージ署名のための新しい MIC アルゴリズムと新しい暗号化アルゴリズムがサポートされるようになりました。 「 [Azure BizTalk Services で AS2 アグリーメントを作成する](https://msdn.microsoft.com/library/azure/hh689890.aspx)」をご覧ください。  
  ## <a name="know-issues"></a>既知の問題

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>BizTalk Services ポータル更新後の接続に関する問題
  BizTalk Services ポータルが開いているときに BizTalk Services をアップグレードし、サービスへの変更を取り込むと、BizTalk Services ポータルの接続に問題が発生することがあります。  
  対処法としては、ブラウザーを再起動するか、ブラウザーのキャッシュを削除するか、プライベート モードでポータルを起動します。  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>BizTalk Services プロジェクトのエラーまたは警告をクリックしたとき、Visual Studio IDE がアーティファクトを特定できない
この問題を解決するには、Visual Studio 2012 Update 3 RC 1 をインストールします。  

### <a name="custom-binding-project-reference"></a>カスタム バインド プロジェクトの参照
Visual Studio ソリューションの BizTalk Services プロジェクトでの次の状況について考えてみます。  

* 同じ Visual Studio ソリューションに、BizTalk Services プロジェクトとカスタム バインド プロジェクトがある。 BizTalk Services プロジェクトには、このカスタム バインド プロジェクト ファイルへの参照が含まれている。
* BizTalk Services プロジェクトには、このカスタム バインド/動作 DLL への参照が含まれている。

Visual Studio では、ソリューションが正常に "ビルド" されます。 その後、ソリューションを "リビルド" または "クリーン" した 後で、リビルドまたはクリーンを再度行うと、  
  "<Path to DLL> から "bin\Debug\FileName.dll" にファイルをコピーできません" というエラーが発生します。 ファイル ‘bin\Debug\FileName.dll’ は、別のプロセスで使用されているため、アクセスできません。  

#### <a name="workaround"></a>対処法
* [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) をインストールしている場合、次の 2 つの選択肢があります。
  
  * Visual Studio を再起動します。
  * ソリューションを再起動します。 起動後、ソリューションのビルドのみを実行します。  
* [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) がインストールされていない場合は、タスク マネージャーを起動し、[プロセス] タブをクリックして、MSBuild.exe プロセスをクリックし、[プロセスの終了] ボタンをクリックします。  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>印刷不可能な文字が HTTP ヘッダーとして昇格されている場合、ブリッジと BizTalk Services ポータルから BasicHttpRelay エンドポイントにルーティングできない
メッセージの昇格されたプロパティの一部として印刷不可能な文字を使用している場合は、BasicHttpRelay バインドを使用しているリレー先にそのメッセージをルーティングすることはできません。 また、追跡の一部として使用できる昇格されたプロパティは、BLOB 用には URL にエンコードされますが、送信先用にはエンコードされません。  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>[非同期 MDN の送信] オプションが選択されていない場合でも、MDN が非同期で送信される
次のシナリオが該当します: **[非同期 MDN の送信]** チェック ボックスをオンにし、非同期 MDN の送信先の URL を指定した後で、**[非同期 MDN の送信]** チェック ボックスをオフにすると、非同期 MDN を送信するオプションが選択されていなくても MDN が引き続き指定した URL に送信される。  
対処法としては、 **[非同期 MDN の送信]** チェック ボックスをオフにする前に指定済みの URL を消去してから、AS2 契約をデプロイします。  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>有効なインターチェンジの後に空白文字があると、保留エンドポイントに空のメッセージが送信される
IEA セグメントの後に空白文字があると、逆アセンブラーはそこを現在のインターチェンジの終端として処理し、その後の一連の空白文字を次のメッセージと見なします。 後のメッセージは有効なインターチェンジではないため、1 件の成功メッセージがルーティング先に送信され、1 件の空のメッセージが保留エンドポイントに送信される場合があります。  

### <a name="tracking-in-biztalk-services-portal"></a>BizTalk Services ポータルにおける追跡
追跡イベントは EDI メッセージ処理と相関関係までキャプチャされます。 プロトコル ステージとは別の要素でメッセージにエラーが発生した場合、追跡は正常と表示されます。 この状況では、エラーの詳細は **[追跡]** の **[詳細]** 列の [LOG] セクションで確認できます。
X12 の受信と送信の設定に関するトピック (「[Azure BizTalk Services での X12 アグリーメントの作成](https://msdn.microsoft.com/library/azure/hh689847.aspx)」) に、プロトコル ステージに関する情報が記載されています。  

### <a name="update-agreement"></a>契約の更新
BizTalk Services ポータルでは、契約の構成時に ID の修飾子を変更できます。 結果として、プロパティに一貫性がなくなる可能性があります。 たとえば、修飾子として ZZ:1234567 および ZZ:7654321 を使用する契約があるとします。 BizTalk Services ポータルのプロファイル設定で、ZZ:1234567 を 01:ChangedValue に変更し、 契約を開くと、ZZ:1234567 ではなく 01:ChangedValue が表示されます。
ID の修飾子を変更する場合は、契約を削除し、パートナー プロファイルで **ID** を更新した後で、契約を再度作成してください。  

> AZURE.WARNING この動作は X12 と AS2 に影響を与えます。  
> 
> 

### <a name="as2-attachments"></a>AS2 添付ファイル
AS2 メッセージの添付ファイルは送信と受信のどちらでもサポートされていません。 具体的には、添付ファイルは確認なしで無視され、メッセージ本文は通常の AS2 メッセージとして処理されます。  

### <a name="resources-remembering-path"></a>リソース: パスの記憶
**リソース**を追加する際、リソースを追加するために前に使用したパスがダイアログ ウィンドウに記憶されていない場合があります。 前に使用したパスを記憶するには、Internet Explorer の **[信頼済みサイト]** に BizTalk Services ポータル Web サイトを追加してみてください。  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>ブリッジのエンティティ名を変更し、変更を保存しないでプロジェクトを閉じると、再度エンティティを開いたときにエラーが発生する
次の順序で操作が実行されるシナリオが該当します。  

* ブリッジ (XML 一方向ブリッジなど) を BizTalk Services プロジェクトに追加する。  
* ブリッジの名前を変更するために [エンティティ名] プロパティの値を指定する。 これによって、関連する .bridgeconfig ファイルの名前が指定した名前に変更される。  
* 変更を保存しないで .bcs ファイルを閉じる (Visual Studio のタブを閉じる)。  
* ソリューション エクスプローラーから再度 .bcs ファイルを開く。  
  関連する .bridgeconfig ファイルは指定した新しい名前に変更されているのに対して、デザイン画面のエンティティ名は以前の名前のままです。 ブリッジ コンポーネントをダブルクリックしてブリッジ構成を開こうとすると、次のエラーが発生します。  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist` このシナリオを回避するには、必ず BizTalk サービス プロジェクトのエンティティの名前を変更した後で、変更を保存します。  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>アーティファクトが Visual Studio プロジェクトから除外されているのに、BizTalk サービス プロジェクトのビルドが成功する
アーティファクト (XSD ファイルなど) を BizTalk サービス プロジェクトに追加し、そのアーティファクトをブリッジ構成に含めた (たとえば、要求メッセージの種類としてそれを指定した) 後で、Visual Studio プロジェクトから除外したシナリオが該当します。 このような場合、除外したアーティファクトがディスク上の、Visual Studio プロジェクトに含めたときと同じ場所にある限り、プロジェクトをビルドしてもエラーは発生しません。
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>BizTalk サービス プロジェクトで、ブリッジの構成時にスキーマの可用性がチェックされない
BizTalk サービス プロジェクトでは、プロジェクトに追加されたスキーマが別のスキーマをインポートした場合、インポートされたスキーマがプロジェクトに追加されたかどうかをチェックしません。 そのようなプロジェクトをビルドしても、ビルド エラーは発生しません。
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>XML 要求-応答ブリッジの応答メッセージの文字セットが常に UTF-8 になる
このリリースでは、XML 要求-応答ブリッジの応答メッセージの文字セットは常に UTF-8 に設定されます。
  
### <a name="user-defined-datatypes"></a>ユーザー定義のデータ型
BizTalk アダプター サービス機能に含まれている BizTalk Adapter Pack アダプターでは、アダプター操作でユーザー定義のデータ型を使用できます。
ユーザー定義のデータ型を使用する際は、ファイル (.dll) をドライブ \Program Files\Microsoft BizTalk Adapter Service\BAServiceRuntime\bin\ か BizTalk アダプター サービスをホストしているサーバーのグローバル アセンブリ キャッシュ (GAC) にコピーします。 コピーしなかった場合、クライアントで次のエラーが発生する可能性があります。  
```
<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<faultcode>s:Client</faultcode>
<faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
<detail>
  <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
  </AFConnectRuntimeFault>
</detail>
</s:Fault>
```  
  
> [!IMPORTANT]
> GACUtil.exe を使用して、ファイルをグローバル アセンブリ キャッシュにインストールすることをお勧めします。 GACUtil.exe には、このツールと Visual Studio コマンド ライン オプションの使用方法に関する説明があります。  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>BizTalk アダプター サービス Web サイトの再起動
**BizTalk Adapter サービスのランタイム***をインストールすると、**BAService** アプリケーションが含まれた **BizTalk Adapter サービス** Web サイトが IIS に作成されます。**BAService** アプリケーションでは、オンプレミスのサービス エンドポイントの範囲をクラウドまで拡大するために、リレー バインドを内部で使用しています。 サービスがオンプレミスでホストされている場合、オンプレミス サービスが開始されて初めて、対応するリレー エンドポイントが Service Bus に登録されます。  

アプリケーションを停止して開始すると、アプリケーションを自動開始する構成は有効になりません。 そのため、**BAService** が停止した場合は、常に **BizTalk アダプター サービス** Web サイトを再起動する必要があります。 **BAService** アプリケーションは、開始も停止もしないでください。

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>LOB コンポーネントのアドレスとエンティティ名に特殊文字を使用できない
LOB コンポーネントのアドレスとエンティティ名には特殊文字を使用しないでください。 使用すると、BizTalk サービス プロジェクトのデプロイ時にエラーが発生します。 "%" など特定の文字を使用すると、BizTalk アダプター サービス Web サイトが停止状態になることがあります。その場合は、手動で開始する必要があります。

### <a name="test-map-with-get-context-property"></a>コンテキスト プロパティの取得によるマップのテスト
変換に**コンテキスト プロパティの取得**マップ操作が含まれていると、**マップのテスト**が失敗します。 一時的な対処法としては、" **コンテキスト プロパティの取得** " マップ操作を、ダミー データが含まれた "文字列連結" マップ操作に置き換えます。 これにより、ターゲット スキーマにデータが入力され、他の変換機能をテストできます。

### <a name="test-map-property-does-not-display"></a>"マップのテスト" プロパティが表示されない
**マップのテスト** プロパティが Visual Studio に表示されない現象は、 **[プロパティ]** ウィンドウと **[ソリューション エクスプローラー]** ウィンドウが同時にドックされていない場合に発生します。 これを解決するには、**[プロパティ]** ウィンドウと **[ソリューション エクスプローラー]** ウィンドウをドックします。  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>[DateTime 再フォーマット] ボックスの一覧がグレー表示される
"DateTime 再フォーマット" マップ操作がデザイン画面に追加されて構成されると、[フォーマット] ボックスの一覧がグレー表示される場合があります。 これは、コンピューターのディスプレイが **[中 – 125%]** または **[大 – 150%]** に設定されている場合に発生することがあります。 この問題を解決するには、次の手順に従って、ディスプレイを **[小 - 100% (既定)]** に設定します。  

1. **コントロール パネル**を開き、**[デスクトップのカスタマイズ]** をクリックします。
2. **[ディスプレイ]**をクリックします。
3. **[小 - 100% (既定)]** をクリックし、**[適用]** をクリックします。

これで、 **[フォーマット]** ボックスの一覧は正常に動作するようになります。

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>BizTalk Services ポータルの契約が重複している
次のシナリオで考えてみましょう。

1. 取引先管理 OM API を使用して契約を作成する。
2. BizTalk Services ポータルで、契約を 2 つの異なるタブで開く。
3. 両方のタブで契約をデプロイする。
4. 結果として、両方の契約がデプロイされ、BizTalk Services ポータルのエントリが重複する。

**対処法**」を参照してください。 BizTalk Services ポータルで重複する契約の 1 つを開き、デプロイを解除します。  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>アーティファクト ストアで証明書を更新した後も、更新された証明書がブリッジで使用されない
次のシナリオで考えてみましょう。  

**シナリオ 1: ブリッジからサービス エンドポイントへのメッセージ転送を保護するために、サムプリント ベースの証明書を使用している**  
BizTalk サービス プロジェクトでサムプリント ベースの証明書を使用しているシナリオで、 BizTalk Services ポータルの証明書を名前は変えずに異なるサムプリントを使用して更新したのに、BizTalk サービス プロジェクトはそれに応じた更新をしていない場合が該当します。 このようなシナリオでは、以前の証明書データがチャネル キャッシュ内にまだ存在しているために、ブリッジがメッセージの処理を続行することがあります。 その場合、メッセージの処理が失敗します。  

**対処法**: BizTalk サービス プロジェクトで証明書を更新し、プロジェクトを再デプロイします。  

**シナリオ 2: ブリッジからサービス エンドポイントへのメッセージ転送を保護するために、名前ベースの動作を使用して証明書を識別している**

BizTalk サービス プロジェクトで名前ベースの動作を使用して証明書を確認しているシナリオで、 BizTalk Services ポータルの証明書を更新したのに、BizTalk サービス プロジェクトはそれに応じた更新をしていない場合が該当します。 このようなシナリオでは、以前の証明書データがチャネル キャッシュ内にまだ存在しているために、ブリッジがメッセージの処理を続行することがあります。 その場合、メッセージの処理が失敗します。  

**対処法**: BizTalk サービス プロジェクトで証明書を更新し、プロジェクトを再デプロイします。  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>SQL Database がオフラインの場合でも、ブリッジがメッセージの処理を続行する
(デプロイされたアーティファクトやパイプラインなどの実行中の情報が格納されている) Microsoft Azure SQL Database がオフラインになっても、BizTalk Services ブリッジはしばらくの間メッセージの処理を続行します。 これは、BizTalk Services がキャッシュされたアーティファクトとブリッジ構成を使用しているためです。
SQL Database がオフラインのときにブリッジがメッセージを処理しないようにするには、BizTalk Services PowerShell コマンドレットを使用して、BizTalk サービスを停止または一時停止します。 操作を管理するための Windows PowerShell コマンドレットについては、 [Azure BizTalk Services 管理のサンプル](http://go.microsoft.com/fwlink/p/?LinkID=329019) に関するページを参照してください。  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>ブリッジのカスタム コード コンポーネント内の XML メッセージを読み込むと、余分な BOM 文字が追加される
ブリッジのカスタム コード内の XML メッセージを読み込むシナリオが該当します。 .NET API System.Text.Encoding.UTF8.GetString(bytes) を使用すると、余分な BOM 文字が出力メッセージの先頭に追加されます。 そのため、出力に余分な BOM 文字が追加されないようにするには、 ```System.IO.StreamReader().ReadToEnd()```を使用する必要があります。

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>WCF を使用したブリッジへのメッセージ送信が拡張しない
WCF を使用してブリッジに送信されたメッセージが拡張しない場合、 スケーラブルなクライアントが必要であれば、代わりに HttpWebRequest を使用してください。

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>アップグレード: BizTalk Services プレビューから一般公開 (GA) にアップグレードした後にトークン プロバイダーのエラーが発生する
アクティブなバッチが含まれた EDI 契約または AS2 契約がある場合、 BizTalk Servicesをプレビューから GA にアップグレードすると、次のエラーが発生することがあります。

* エラー: トークン プロバイダーは、セキュリティ トークンを提供できませんでした。 トークン プロバイダーから返されるメッセージ: リモート名を解決できませんでした。
* バッチ タスクがキャンセルされます。

**対処法**: BizTalk Services を GA にアップグレードした後に、契約を再度デプロイします。  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>アップグレード: BizTalk Services SDK をアップグレードした後に、ツールボックスに古いブリッジ アイコンが表示される
ブリッジを表す古いアイコンがある旧バージョンの BizTalk Services SDK をアップグレードした後も、ツールボックスには引き続きブリッジの古いアイコンが表示されます。 ただし、ブリッジを BizTalk サービス プロジェクトのデザイン画面に追加すると、画面に新しいアイコンが表示されます。  

**対処法**」を参照してください。 <system drive>:\Users\<\AppData\Local\Microsoft\VisualStudio\11.0 にある .tbd ファイルを削除することで、この問題を回避できます。  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>アップグレード: BizTalk ポータルをプレビューから GA に更新すると、EDI 機能を使用できないというエラーが表示されることがある
BizTalk Services ポータルにログインしているときに BizTalk Services をプレビューから GA にアップグレードすると、ポータルに次のエラーが発生することがあります。  

この機能は、このエディションの Microsoft Azure BizTalk Services には含まれていません。 この機能を使用するには、対応するエディションに切り替えてください。  

**解決策**: ポータルからログアウトし、ブラウザーを閉じて再び開き、再度ポータルにログインします。  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>アップグレード: BizTalk Services を GA にアップグレードした後に新しい追跡データが表示されない
BizTalk Services プレビュー サブスクリプションに XML ブリッジがデプロイされているシナリオを想定します。 ブリッジにメッセージを送信すると、対応する追跡データが BizTalk Services ポータルに表示されます。 現時点では、BizTalk Services ポータルと BizTalk Services ランタイム ビットを GA にアップグレードし、以前にデプロイしたのと同じブリッジ エンドポイントにメッセージを送信すると、アップグレード後に送信したメッセージの追跡データは表示されません。  

### <a name="pipelines-versus-bridges"></a>パイプラインとブリッジ
このドキュメントでは、"パイプライン" という用語と "ブリッジ" という用語が同じ意味で使われています。 どちらも本質的には同じ意味で、BizTalk Services にデプロイされたメッセージ処理単位を指します。  

### <a name="concepts"></a>概念
[BizTalk Services](https://msdn.microsoft.com/library/azure/hh689864.aspx)   




<!--HONumber=Nov16_HO3-->


