---
title: "Access Control の使用方法 (Java) | Microsoft Docs"
description: "Azure 上で Java により Access Control を開発して使用する方法について説明します。"
services: active-directory
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 247dfd59-0221-4193-97ec-4f3ebe01d3c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 3e0f8d92495c976040113f8edc0d4c789920c549
ms.openlocfilehash: e22dca7b666720edfcbfd1b871d98b9468441fbd


---
# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Eclipse を使用して Azure の Access Control Service で Web ユーザーを認証する方法
このガイドでは、Azure Toolkit for Eclipse 内で Azure の Access Control Service (ACS) を使用する方法について説明します。 ACS の詳細については、「 [次のステップ](#next_steps) 」を参照してください。

> [!NOTE]
> Azure Access Control Services Filter は Community Technology Preview 版です。 プレリリース版ソフトウェアとしてマイクロソフトによって正式にサポートされていません。
> 
> 

## <a name="what-is-acs"></a>ACS とは
ほとんどの開発者は ID の専門家ではないため、一般的には時間を費やしてアプリケーションとサービス用に認証と承認のメカニズムを開発しません。 ACS は Azure サービスの 1 つで、Web アプリケーションとサービスにアクセスする必要があるユーザーを認証する簡単な方法を、コードに複雑な認証ロジックを組み込む必要なく、実現します。

ACS では、次の機能を使用できます。

* Windows Identity Foundation (WIF) との統合。
* Windows Live ID、Google、Yahoo!、Facebook などの一般的な Web ID プロバイダーのサポート。
* Active Directory フェデレーション サービス (AD FS) 2.0 のサポート。
* ACS 設定へのプログラムによるアクセスを提供する Open Data Protocol (OData) ベースの管理サービス。
* ACS 設定への管理アクセスが可能な管理ポータル。

ACS の詳細については、[Access Control サービス 2.0 に関するページ][Access Control Service 2.0]を参照してください。

## <a name="concepts"></a>概念
Azure ACS はクレーム ベース ID のプリンシパルに基づいており、オンプレミスまたはクラウドで実行中のアプリケーションに対する認証メカニズムを一貫したアプローチで作成できます。 クレーム ベース ID は、アプリケーションとサービスが必要とする組織内、その他の組織内、インターネット上のユーザーについての ID 情報を取得するための一般的な方法を提供します。

このガイドのタスクを完了するには、次の概念を理解している必要があります。

**クライアント** - この使用方法のガイドでは、クライアントとは Web アプリケーションへのアクセスを取得するブラウザーのことです。

**証明書利用者 (RP) アプリケーション** - RP アプリケーションとは、1 つの外部の機関に認証を委託する Web サイトまたはサービスです。 ID の分野では、"RP がその機関を信頼する" という表現を使います。 このガイドでは、アプリケーションが ACS を信頼するように構成する方法について説明します。

**トークン** - トークンはセキュリティ データの集まりで、通常はユーザーの成功した認証に対して発行されます。 これには、 *クレーム*(認証されたユーザーの属性) のセットが含まれます。 クレームは、ユーザー名、ユーザーが属するロールの識別子、ユーザーの年齢などを表すことができます。 トークンは通常、電子署名されます。 つまり、常に発行者を明らかにすることが可能で、そのコンテンツを改ざんできませんユーザーは、RP アプリケーションへのアクセスを得るために、RP アプリケーションが信頼している機関によって発行されたトークンを提示します。

**ID プロバイダー (IP)** - IP は、ユーザー ID を認証し、セキュリティ トークンを発行する機関です。 実際にトークンを発行する作業は、Security Token Service (STS) と呼ばれる特別なサービスを介して実行されます。 IP の典型的な例には Windows Live ID、Facebook、ビジネス ユーザー リポジトリ (たとえば Active Directory) などがあります。
ACS を構成して、IP を信頼する場合、システムは、その IP によって発行されたトークンを受け入れ、検証します。 ACS は同時に複数の IP を信頼できます。つまり、アプリケーションが ACS を信頼すると、ACS が信頼するすべての IP の認証されたユーザーすべてに、アプリケーションが直ちに提示されます。

**フェデレーション プロバイダー (FP)** - IP はユーザーについての直接の情報を持ち、資格情報を使ってユーザーを認証し、ユーザーのどのような情報を持っているかについてのクレームを発行します。 フェデレーション プロバイダー (FP) は、それとは別の種類の機関です。ユーザーを直接認証するのではなく、仲介者として機能し、1 つの RP と 1 つ以上の IP との間で認証を仲介します。 IP と FP はどちらもセキュリティ トークンを発行するため、Security Token Services (STS) を使用します。 ACS は FP の 1 つです。

**ACS 規則エンジン** - 信頼されている IP から受信するトークンを RP で使用されるトークンに変換するために使われるロジックは、単純なクレーム変換規則の形式に体系化されています。 ACS には、RP のために指定された任意の変換ロジックを適用できる規則エンジンが用意されています。

**ACS 名前空間** - 名前空間は、設定を構造化するために使う、ACS の最上位レベルの部分です。 名前空間は、信頼される IP、提供する RP アプリケーション、規則エンジンで受信トークンの処理に使用する規則などの一覧を保持します。 名前空間は、さまざまなエンドポイントを公開します。アプリケーションと開発者は、これらのエンドポイントを使用して、ACS がその機能を実行できるようにすることができます。

次の図は、Web アプリケーションで ACS 認証がどのように機能するかを 示しています。

![ACS フロー図][acs_flow]

1. クライアント (ここではブラウザー) が RP のページを要求します。
2. 要求がまだ認証されていないので、RP は信頼している機関 (つまり ACS) にユーザーをリダイレクトします。 ACS は、この RP 用に指定されている IP の選択肢をユーザーに提示します。 ユーザーが適切な IP を選択します。
3. クライアントは IP の認証ページに移動し、ユーザーにログオンを求めま す。
4. クライアントが認証されたら (たとえば、ID 資格情報が入力されたら)、IP はセキュリティ トークンを発行します。
5. セキュリティ トークンの発行後、IP はクライアントを ACS にリダイレクトし、クライアントは IP によって発行されたセキュリティ トークンを ACS に送信します。
6. ACS は IP によって発行されたセキュリティ トークンを検証し、 このトークンの ID クレームを ACS 規則エンジンに入力して出力 ID クレームを計算し、これらの出力クレームを含む新しいセキュリティ トークンを発行します。
7. ACS がクライアントを RP にリダイレクトします。 クライアントが ACS によって発行された新しいセキュリティ トークンを RP に送信します。 RP が ACS によって発行されたセキュリティ トークンの署名を検証し、このトークンのクレームを検証して、最初に要求されたページを返します。

## <a name="prerequisites"></a>前提条件
このガイドのタスクを完了するには、次のものが必要です。

* A Java Developer Kit (JDK) v 1.6 以降。
* Eclipse IDE for Java EE Developers Indigo 以降。 <http://www.eclipse.org/downloads/> からダウンロードできます。 
* Java ベースの Web サーバーまたはアプリケーション サーバーのディストリビューション (Apache Tomcat、GlassFish、JBoss Application Server、Jetty など)。
* Azure サブスクリプション。<http://www.microsoft.com/windowsazure/offers/> から入手できます。
* Azure Toolkit for Eclipse、2014 年 4 月のリリース以降。 詳細については、「 [Azure Toolkit for Eclipse のインストール](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx)」を参照してください。
* アプリケーションで使用する X.509 証明書。 この証明書は .cer (公開証明書) と .PFX (Personal Information Exchange) の両方の形式のものが必要です。 (この証明書の作成方法についてはこのチュートリアルで後で説明)。
* [Azure 用の Hello World アプリケーションを Eclipse で作成する方法に関するトピック](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)で説明されている、Azure コンピューティング エミュレーターとそのエミュレーターへのデプロイ手法に精通していること。

## <a name="create-an-acs-namespace"></a>ACS 名前空間の作成
Azure で Access Control Service (ACS) の使用を開始するには、ACS 名前空間を作成する必要があります。 名前空間では、アプリケーション内から ACS リソースのアドレスを指定するための一意のスコープが提供されます。

1. [Azure 管理ポータル][Azure Management Portal]にログインします。
2. **[Active Directory]**をクリックします。 
3. 新しい Access Control 名前空間を作成するには、**[新規]**、**[App Services]**、**[Access Control]**、**[簡易作成]** の順にクリックします。 
4. 名前空間の名前を入力します。 名前が一意であるかが確認されます。
5. 名前空間が使用されるリージョンを選択します。 パフォーマンスを最高にするには、アプリケーションを展開するリージョンと同じにし、[作成] をクリックします。
6. 複数のサブスクリプションがある場合は、ACS 名前空間に使用するサブスクリプションを選択します。
7. **[作成]**をクリックします。

名前空間が作成されて有効化されます。 新しい名前空間のステータスが **[アクティブ]** になるのを待ってから、次に進みます。 

## <a name="add-identity-providers"></a>ID プロバイダーの追加
このタスクでは、認証のために RP アプリケーションと共に使用できるように IP を追加します。 デモンストレーションを目的として、このタスクでは Windows Live を IP として追加する方法を説明しますが、ACS 管理ポータルの一覧にある任意の IP を使用することができます。

1. [Azure 管理ポータル][Azure Management Portal]で、**[Active Directory]** をクリックし、Access Control 名前空間を選択して、**[管理]** をクリックします。 ACS 管理ポータルが開きます。
2. ACS 管理ポータルの左のナビゲーション ウィンドウで、 **[ID プロバイダー]**をクリックします。
3. Windows Live ID は既定で有効になっており、削除することはできません。 このチュートリアルでは、Windows Live ID のみ使用します。 この画面では、ただし **[追加]** ボタンをクリックして、ほかの IP を追加できます。

これで、Windows Live ID が ACS 名前空間の IP として有効になりました。 次は、後で作成する Java Web アプリケーションを RP として指定します。

## <a name="add-a-relying-party-application"></a>証明書利用者アプリケーションの追加
このタスクでは、Java Web アプリケーションを有効な RP アプリケーションとして認識するように ACS を構成します。

1. ACS 管理ポータルで **[証明書利用者アプリケーション]**をクリックします。
2. **[証明書利用者アプリケーション]** ページで、**[追加]** をクリックします。
3. **[証明書利用者アプリケーションの追加]** ページで、次の操作を行います。
   
   1. **[名前]**に RP の名前を入力します。 このチュートリアルでは、「 **Azure Web App**」と入力します。
   2. **[モード]** で **[設定を手動で入力する]** を選択します。
   3. **[領域]**で、ACS によって発行されたセキュリティ トークンを適用する URI を入力します。 このタスクでは、「**http://localhost:8080/**」と入力します。
      ![コンピューティング エミュレーターで使用する証明書利用者領域][relying_party_realm_emulator]
   4. **[戻り先 URL]** で、ACS がセキュリティ トークンを返す URL を入力します。 このタスクでは、「**http://localhost:8080/MyACSHelloWorld/index.jsp**」と入力します。
      ![コンピューティング エミュレーターで使用する証明書利用者の戻り先 URL][relying_party_return_url_emulator]
   5. その他のフィールドは、既定値のままにします。
4. **[Save]**をクリックします。

これで、Azure コンピューティング エミュレーターで実行される Java Web アプリケーション (http://localhost:8080/) が ACS 名前空間の RP として正常に構成されました。 次に、ACS が RP のクレームを処理するために使用する規則を作成します。

## <a name="create-rules"></a>規則の作成
このタスクでは、クレームが IP から RP にどのように渡されるかを決定する規則を定義します。 このガイドでは、単に入力クレームの種類と値を出力トークンに直接コピーするだけで、フィルタリングや変更は行わないように、ACS を構成します。

1. ACS 管理ポータルのメイン ページで、 **[規則グループ]**をクリックします。
2. **[規則グループ]** ページで、**[Azure Web App の既定の規則グループ]** をクリックします。
3. **[規則グループの編集]** ページで **[生成]** をクリックします。
4. **[規則の生成: Azure Web App の既定の規則グループ]** ページで、[Windows Live ID] チェック ボックスがオンになっていることを確認し、**[生成]** をクリックします。    
5. **[規則グループの編集]** ページで **[保存]** をクリックします。

## <a name="upload-a-certificate-to-your-acs-namespace"></a>ACS 名前空間への証明書のアップロード
このタスクでは、ACS 名前空間によって作成されたトークン要求への署名に使用される .PFX 証明書をアップロードします。

1. ACS 管理ポータルのメイン ページで、 **[証明書とキー]**をクリックします。
2. **[証明書とキー]** ページで、 **[トークンの署名]** の上の **[追加]** をクリックします。
3. **[トークン署名証明書またはキーの追加]** ページで、次の操作を行います。
   1. **[使用目的]** セクションで、**[証明書利用者アプリケーション]** をクリックし、**[Azure Web App]** (前の手順で設定した証明書利用者アプリケーションの名前) を選択します。
   2. **[種類]** セクションで **[X.509 証明書]** を選択します。
   3. **[証明書]** セクションで、参照ボタンをクリックし、使用する X.509 証明書ファイルに移動します。 これが .PFX ファイルになります。 そのファイルを選択し、**[開く]** をクリックして、**[パスワード]** ボックスに証明書のパスワードを入力します。 テスト目的で、自己署名証明書を使用してもかまいません。 自己署名証明書を作成するには、**[ACS Filter Library (ACS Filter ライブラリ)]** ダイアログ ボックスの **[New (新規)]** ボタンを使用するか (後で説明)、または Azure Starter Kit for Java の[プロジェクト Web サイト][project website]の **encutil.exe** ユーティリティを使用します。
   4. **[プライマリにする]** チェック ボックスがオンになっていることを確認します。 **[トークン署名証明書またはキーの追加]** ページは次のようになります。
       ![トークン署名証明書の追加][add_token_signing_cert]
   5. **[保存]** をクリックして設定を保存し、**[トークン署名証明書またはキーの追加]** ページを閉じます。

次は、[アプリケーション統合] ページの情報を確認し、ACS を使用するように Java Web アプリケーションを構成するために必要な URI をコピーします。

## <a name="review-the-application-integration-page"></a>[アプリケーション統合] ページの確認
ACS 管理ポータルの [アプリケーション統合] ページでは、Java Web アプリケーション (RP アプリケーション) と ACS が連携する構成に必要なすべての情報とコードを確認できます。 この情報は、Java Web アプリケーションをフェデレーション認証用に構成するときに必要です。

1. ACS 管理ポータルで、 **[アプリケーション統合]**をクリックします。  
2. **[アプリケーション統合]** ページで **[ログイン ページ]** をクリックします。
3. **[ログイン ページ統合]** ページで**[Azure Web App]** をクリックします。

**[ログイン ページ統合: Azure Web App]** ページで、**[オプション 1: ACS ホステッド ログイン ページへのリンク]** に表示されている URL が Java Web アプリケーションで使用されます。 この値は、Azure の Access Control Services Filter ライブラリを Java アプリケーションに追加するときに必要になります。

## <a name="create-a-java-web-application"></a>Java Web アプリケーションの作成
1. Eclipse のメニューで、**[File]**、**[New]**、**[Dynamic Web Project]** の順にクリックします。 (**[File (ファイル)]** と **[New (新規)]** のクリック後、使用可能なプロジェクトとして **[Dynamic Web Project (動的 Web プロジェクト)]** が表示されない場合は、**[File (ファイル)]**、**[New (新規)]**、**[Project (プロジェクト)]** の順にクリックし、**[Web]** を展開して、**[Dynamic Web Project (動的 Web プロジェクト)]**、**[Next (次へ)]** の順にクリックします)。このチュートリアルでは、プロジェクトに **MyACSHelloWorld** という名前を付けます。 (この名前を使用していることを確認してください。このチュートリアルの以降の手順では、WAR ファイルの名前が MyACSHelloWorld であるとします)。 画面は次のようになります。
   
    ![ACS サンプル用の Hello World プロジェクトの作成][create_acs_hello_world]
   
    **[完了]**をクリックします。
2. Eclipse の Project Explorer ビューで、 **MyACSHelloWorld**を展開します。 **WebContent** を右クリックし、**[新規]**、**[JSP ファイル]** の順にクリックします。
3. **[New JSP File (新しい JSP ファイル)]** ダイアログ ボックスで、ファイルに **index.jsp** という名前を付けます。 次に示しているように、親フォルダーは MyACSHelloWorld/WebContent のままにすることに注意してください。
   
    ![ACS サンプル用の JSP ファイルの追加][add_jsp_file_acs]
   
    **[次へ]**をクリックします。
4. **[Select JSP Template (JSP テンプレートの選択)]** ダイアログ ボックスで、**[New JSP File (html) (新しい JSP ファイル (html))]** を選択し、**[完了]** をクリックします。
5. index.jsp ファイルが Eclipse で開いたら、**Hello ACS World!** を表示するためのテキストを追加します。 既存の `<body>` 要素に追加します。 更新した `<body>` の内容は次のようになります。
   
        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
   
    index.jsp を保存します。

## <a name="add-the-acs-filter-library-to-your-application"></a>アプリケーションへの ACS Filter ライブラリの追加
1. Eclipse の Project Explorer で、**MyACSHelloWorld** を右クリックし、**[Build Path]**、**[Configure Build Path]** の順にクリックします。
2. **[Java Build Path]** ダイアログ ボックスで **[Libraries]** タブをクリックします。
3. **[Add Library]**をクリックします。
4. **[Azure Access Control Services Filter (by MS Open Tech)]** をクリックし、**[Next]** をクリックします。 **[Location]** フィールドに表示されるパスは Eclipse のインストール場所によって異なる場合があります。  (**[Location]** フィールドに表示されるパスは Eclipse のインストール場所によって異なる場合があります。バージョン番号はソフトウェア更新状況によって異なる場合があります)。
   
    ![ACS Filter ライブラリの追加][add_acs_filter_lib]
5. ブラウザーで開いた管理ポータルの **[ログイン ページ統合]** ページで、**[オプション 1: ACS ホステッド ログイン ページへのリンク]** フィールドに表示されている URL をコピーし、Eclipse ダイアログ ボックスの **[ACS Authentication Endpoint]** フィールドに貼り付けます。
6. ブラウザーで開いた管理ポータルの **[証明書利用者アプリケーションの編集]** ページで、**[領域]** フィールドに表示されている URL をコピーし、Eclipse ダイアログ ボックスの **[Relying Party Realm]** フィールドに貼り付けます。
7. Eclipse ダイアログ ボックスの **[Security]** セクションで、既存の証明書を使用する場合は、**[Browse]** をクリックして、使用する証明書に移動します。その証明書を選択し、**[Open]** をクリックします。 または、新しい証明書を作成する場合は、**[New]** をクリックして **[New Certificate]** ダイアログ ボックスを表示し、新しい証明書のパスワード、.cer ファイル名、および .pfx ファイル名を指定します。
8. **[Embed the certificate in the WAR file]**チェック ボックスをオンにします。 証明書はこの方法で埋め込むことでデプロイに追加されます。 手動でコンポーネントとして追加する必要はありません(代わりに WAR ファイル以外からの証明書を保存する必要がある場合は、証明書をロール コンポーネントとして追加し、 **[Embed the certificate in the WAR file]**チェック ボックスをオフにすることができます)。
9. (省略可能) **[Require HTTPS connections]** チェック ボックスをオンにします。 このオプションを設定する場合は、HTTPS プロトコルを使用してアプリケーションにアクセスすることが必要になります。 HTTPS 接続が不要な場合は、このチェック ボックスをオフにします。
10. コンピューティング エミュレーターにデプロイする場合、 **[Azure ACS Filter]** 設定は次のようになります。
    
    ![コンピューティング エミュレーターにデプロイする場合の [Azure ACS Filter] 設定][add_acs_filter_lib_emulator]
11. **[完了]**をクリックします。
12. web.xml ファイルの作成を確認するダイアログ ボックスが表示されたら、 **[Yes]** をクリックします。
13. **[OK]** をクリックして、**[Java Build Path]** ダイアログ ボックスを閉じます。

## <a name="deploy-to-the-compute-emulator"></a>コンピューティング エミュレーターへのデプロイ
1. Eclipse の Project Explorer で、**MyACSHelloWorld** を右クリックし、**[Azure]**、**[Package for Azure]** の順にクリックします。
2. **[Project name]** で、「**MyAzureACSProject**」と入力し、**[Next]** をクリックします。
3. JDK とアプリケーション サーバーを選択します (これらの手順の詳細については、「[Creating a Hello World Application for Azure in Eclipse (Azure 用の Hello World アプリケーションを Eclipse で作成する方法)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)」のチュートリアルを参照)。
4. **[完了]**をクリックします。
5. **[Run in Azure Emulator]** ボタンをクリックします。
6. Java Web アプリケーションがコンピューティング エミュレーターで起動した後、ブラウザーのすべてのインスタンスを閉じます (ブラウザー セッションが開いていると ACS ログイン テストの妨げになるためです)。
7. アプリケーションを実行するために、ブラウザーで <http://localhost:8080/MyACSHelloWorld/> を開きます (または **[Require HTTPS connections]** チェック ボックスをオンにした場合は、<https://localhost:8080/MyACSHelloWorld/> を開きます)。 Windows Live ID でログインするように求められます。その資格情報は指定した証明書利用者アプリケーションの戻り先 URL に送信されます。
8. アプリケーションの表示が完了したら、 **[Reset Azure Emulator]** ボタンをクリックします。

## <a name="deploy-to-azure"></a>Azure へのデプロイ
Azure にデプロイするには、ACS 名前空間の証明書利用者領域と戻り先 URL を変更する必要があります。

1. Microsoft Azure 管理ポータルの **[証明書利用者アプリケーションの編集]** ページで、デプロイするサイトの URL になる**領域**を変更します。 **example** を、指定した展開の DNS 名に置き換えます。
   
    ![運用で使用する証明書利用者の領域][relying_party_realm_production]
2. アプリケーションの URL になる **戻り先 URL** を変更します。 **example** を、指定した展開の DNS 名に置き換えます。
   
    ![運用で使用する証明書利用者の戻り先 URL][relying_party_return_url_production]
3. **[保存]** をクリックして、変更した証明書利用者の領域と戻り先 URL を保存します。
4. **[ログイン ページ統合]** ページはブラウザーで開いたままにします。すぐ後にこのページの情報をコピーする必要があるためです。
5. Eclipse の Project Explorer で、**MyACSHelloWorld** を右クリックし、**[Build Path]**、**[Configure Build Path]** の順にクリックします。
6. **[Libraries]** タブをクリックし、**[Azure Access Control Services Filter]**、**[Edit]** の順にクリックします。
7. ブラウザーで開いた管理ポータルの **[ログイン ページ統合]** ページで、**[オプション 1: ACS ホステッド ログイン ページへのリンク]** フィールドに表示されている URL をコピーし、Eclipse ダイアログ ボックスの **[ACS Authentication Endpoint]** フィールドに貼り付けます。
8. ブラウザーで開いた管理ポータルの **[証明書利用者アプリケーションの編集]** ページで、**[領域]** フィールドに表示されている URL をコピーし、Eclipse ダイアログ ボックスの **[Relying Party Realm]** フィールドに貼り付けます。
9. Eclipse ダイアログ ボックスの **[Security]** セクションで、既存の証明書を使用する場合は、**[Browse]** をクリックして、使用する証明書に移動します。その証明書を選択し、**[Open]** をクリックします。 または、新しい証明書を作成する場合は、**[New]** をクリックして **[New Certificate]** ダイアログ ボックスを表示し、新しい証明書のパスワード、.cer ファイル名、および .pfx ファイル名を指定します。
10. **[Embed the certificate in the WAR file]** チェック ボックスはオンのままにして、証明書を WAR ファイルに埋め込むものとします。
11. (省略可能) **[Require HTTPS connections]** チェック ボックスをオンにします。 このオプションを設定する場合は、HTTPS プロトコルを使用してアプリケーションにアクセスすることが必要になります。 HTTPS 接続が不要な場合は、このチェック ボックスをオフにします。
12. Azure にデプロイする場合、[Azure ACS Filter] 設定は次のようになります。
    
    ![運用デプロイ用の [Azure ACS Filter] 設定][add_acs_filter_lib_production]
13. **[Finish]** をクリックして、**[Edit Library]** ダイアログ ボックスを閉じます。
14. **[OK]** をクリックして **[Properties for MyACSHelloWorld]** ダイアログ ボックスを閉じます。
15. Eclipse で、**[Publish to Azure Cloud]** ボタンをクリックします。 プロンプトに応答します。手順は「[Creating a Hello World Application for Azure in Eclipse (Azure 用の Hello World アプリケーションを Eclipse で作成する方法)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)」の「**To deploy your application to Azure (アプリケーションを Azure に展開するには)**」で行ったものと同様です。 

Web アプリケーションのデプロイ後、開いているブラウザー セッションをすべて閉じてから、Web アプリケーションを実行します。Windows Live ID 資格情報でログインするように求められます。その資格情報は証明書利用者アプリケーションの戻り先 URL に送信されます。

ACS Hello World アプリケーションの使用後は展開を削除することを忘れないでください (展開の削除方法については、「 [Creating a Hello World Application for Azure in Eclipse (Azure 用の Hello World アプリケーションを Eclipse で作成する方法)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) 」を参照)。

## <a name="a-namenextstepsanext-steps"></a><a name="next_steps"></a>次のステップ
ACS によってアプリケーションに返される SAML (Security Assertion Markup Language) を調べる場合は、「[How to view SAML returned by the Azure Access Control Service (Azure の Access Control サービスによって返される SAML を表示する方法)][How to view SAML returned by the Azure Access Control Service]」を参照してください。 さらに ACS の機能を調べたり、より洗練されたシナリオを試してみたりする場合は、「[Access Control Service 2.0][Access Control Service 2.0]」を参照してください。

また、この例では **[Embed the certificate in the WAR file]** オプションも使用しています。 このオプションを使用すると証明書のデプロイが簡単になります。 代わりに WAR ファイル以外からの署名証明書を保存する場合は、次の手法を使用できます。

1. **[Azure Access Control Services Filter]** ダイアログ ボックスの **[Security]** セクションで、「**${env.JAVA_HOME}/mycert.cer**」と入力し、**[Embed the certificate in the WAR file]** チェック ボックスをオフにします。 (証明書のファイル名が異なる場合は mycert.cer を調整)。**[Finish]** をクリックしてダイアログ ボックスを閉じます。
2. コンポーネントとして証明書をデプロイにコピーする場合: Eclipse の Project Explorer で、**MyAzureACSProject** をデプロイし、**WorkerRole1** を右クリックし、**[Properties]** をクリックします。**[Azure Role]** をデプロイし、**[Components]** をクリックします。
3. **[追加]**をクリックします。
4. **[Add Component]** ダイアログ ボックスで、次の操作を行います。
   
   1. **[Import]** セクション:
      1. **[File]** ボタンをクリックして、使用する証明書に移動します。 
      2. **[Method]** で、**[copy]** を選択します。
   2. **[As Name]**で、テキスト ボックスをクリックし、既定の名前をそのまま使用します。
   3. **[Deploy]** セクション:
      1. **[Method]** で、**[copy]** を選択します。
      2. **[To directory]** で、「**%JAVA_HOME%**」と入力します。
   4. **[Add Component]** ダイアログ ボックスは次のようになります。
      
       ![証明書コンポーネントの追加][add_cert_component]
   5. **[OK]**をクリックします。

これで、証明書はデプロイに追加されました。 WAR ファイルに証明書を埋め込むか、コンポーネントとしてデプロイに追加するかにかかわらず、「[ACS 名前空間への証明書のアップロード][Upload a certificate to your ACS namespace]」セクションで説明しているように、証明書を名前空間にアップロードする必要があることに注意してください。

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Upload a certificate to your ACS namespace]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[project website]: http://wastarterkit4java.codeplex.com/releases/view/61026
[How to view SAML returned by the Azure Access Control Service]: active-directory-java-view-saml-returned-by-access-control.md
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure Management Portal]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png




<!--HONumber=Jan17_HO3-->


