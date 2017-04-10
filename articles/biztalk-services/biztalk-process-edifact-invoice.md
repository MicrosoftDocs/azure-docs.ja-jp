---
title: "チュートリアル: Azure BizTalk Services を使用して EDIFACT 請求書を処理する | Microsoft Docs"
description: "Box コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
ms.assetid: 7e0b93fa-3e2b-4a9c-89ef-abf1d3aa8fa9
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b7ad7b91c6b836f26b45959ef65a99666a4bf69a


---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>チュートリアル: Azure BizTalk Services を使用して EDIFACT 請求書を処理する
BizTalk Services ポータルを使用して、X12 契約と EDIFACT 契約を構成し、デプロイできます。 このチュートリアルでは、EDIFACT 契約を作成し、取引先間で請求書を交換する方法について説明します。 このチュートリアルは、EDIFACT メッセージを交換している Northwind と Contoso という 2 社の取引先を対象とするエンド ツー エンドのビジネス ソリューションに基づいて作成されています。  

## <a name="sample-based-on-this-tutorial"></a>このチュートリアルをベースにしたサンプル
このチュートリアルは、 **MSDN のコード ギャラリー**からダウンロードできる、 [BizTalk Services を使用した EDIFACT 請求書の送信](http://go.microsoft.com/fwlink/?LinkId=401005)に関するサンプルに基づいて作成されています。 このサンプルを参照しながらチュートリアルを進めると、サンプルがどのように作成されたかを理解できます。 また、このチュートリアルを使用して、ソリューションを自分でゼロから作成することもできます。 ソリューションがどのように作成されるかを理解できるように、このチュートリアルでは後者のアプローチを目標としています。 このチュートリアルは、できる限りサンプルと整合するよう配慮されており、アーティファクト (スキーマや変換など) にはサンプルで使用されるものと同じ名前を使用しています。  

> [!NOTE]
> このソリューションには EAI ブリッジから EDI ブリッジへのメッセージ送信が含まれるため、 [BizTalk Services ブリッジのチェーン](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) に関するサンプルが再利用されています。  
> 
> 

## <a name="what-does-the-solution-do"></a>このソリューションの内容
このソリューションでは、Northwind が Contoso から EDIFACT 請求書を受け取ります。 請求書が標準の EDI 形式ではないため、 Northwind に送信する前に、請求書を EDIFACT 請求書 (INVOIC とも呼ばれます) ドキュメントに変換する必要があります。 受信後、Northwind は EDIFACT 請求書を処理し、制御メッセージ (CONTRL とも呼ばれます) を Contoso に返す必要があります。

![][1]  

このビジネス シナリオを達成するために、Contoso は Microsoft Azure BizTalk Services が備える機能を使用します。

* Contoso は EAI ブリッジを使用して、元の請求書を EDIFACT INVOIC に変換します。
* BizTalk Services ポータルで構成された契約の一部としてデプロイされている EDI 送信ブリッジに、EAI ブリッジからメッセージが送信されます。
* EDI 送信ブリッジは EDIFACT INVOIC を処理し、Northwind にルーティングします。
* 請求書を受信すると、Northwind は、契約の一部としてデプロイされている EDI 受信ブリッジに CONTRL メッセージを返します。  

> [!NOTE]
> このソリューションでは、各請求書を個別に送信する代わりに、バッチ処理機能を使用して請求書を一括送信する方法もオプションとして紹介します。  
> 
> 

シナリオの実行にあたって、Contoso から Northwind への請求書の送信と、Northwind からの受信確認の受信に、Service Bus のキューを使用します。 このキューはクライアント アプリケーションを使用して作成できます。クライアント アプリケーションはダウンロードで入手できるほか、このチュートリアルの過程で取得できるサンプル パッケージにも含まれています。  

## <a name="prerequisites"></a>前提条件
* Service Bus 名前空間が必要です。 名前空間を作成する手順については、 [Service Bus サービスの名前空間を作成または変更する方法](https://msdn.microsoft.com/library/azure/hh674478.aspx)に関するページを参照してください。 このチュートリアルでは、「 **edifactbts**」という名前の Service Bus 名前空間が既にプロビジョニングされているものと想定しています。
* BizTalk Services サブスクリプションが必要です。 手順については、 [Azure クラシック ポータルを使用して BizTalk サービスを作成する方法](http://go.microsoft.com/fwlink/?LinkID=302280)に関するページを参照してください。 このチュートリアルでは、「 **contosowabs**」という名前の BizTalk Services サブスクリプションがあるものと想定しています。
* BizTalk Services ポータルに BizTalk Services サブスクリプションを登録する必要があります。 手順については、 [BizTalk Services ポータルでの BizTalk サービス配置の登録と更新](https://msdn.microsoft.com/library/hh689837.aspx)
* Visual Studio をインストールする必要があります。
* BizTalk Services SDK をインストールする必要があります。 SDK は [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>手順1. Service Bus キューを作成する
このソリューションでは、Service Bus キューを使用して、取引先間でメッセージを交換します。 Contoso と Northwind はメッセージをキューに送り、EAI ブリッジと EDI ブリッジがこのキューからメッセージを取得して処理します。 このソリューションでは、次の 3 つの Service Bus キューが必要です。

* **northwindreceive** – Northwind は、このキューを介して Contoso からの請求書を受信します。
* **contosoreceive** – Contoso は、このキューを介して Northwind からの受信確認を受信します。
* **suspended** – 保留メッセージはすべて、このキューにルーティングされます。 処理中にエラーが発生すると、メッセージは保留になります。

サンプル パッケージに含まれているクライアント アプリケーションを使用して、これらの Service Bus キューを作成できます。  

1. サンプルをダウンロードした場所で、 **Tutorial Sending Invoices Using BizTalk Services EDI Bridges.sln**を開きます。
2. **F5** キーを押し、**チュートリアル クライアント** アプリケーションをビルドして起動します。
3. 表示された画面で、Service Bus ACS 名前空間、発行者名、発行者キーを入力します。
   
   ![][2]  
4. Service Bus 名前空間に 3 つのキューが作成されることがメッセージ ボックスに表示されます。 **[OK]**をクリックします。
5. チュートリアル クライアントを実行したままにしておきます。 **[Service Bus]**  >  ***使用する Service Bus 名前空間***  >  **[キュー]** の順にクリックし、3 つのキューが作成されていることを確認します。  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>手順 2. 取引先契約を作成してデプロイする
Contoso と Northwind の間の取引先契約を作成します。 取引先契約とは、2 つのビジネス パートナーの間の取引契約を定義するものです。使用するメッセージ スキーマやメッセージング プロトコルなどが含まれます。取引先契約には 2 つの EDI ブリッジが含まれます。1 つはメッセージを取引先に送信するためのもので (**EDI 送信ブリッジ**と呼びます)、もう 1 つは取引先からメッセージを受信するためのものです (**EDI 受信ブリッジ**と呼びます)。

このソリューションのコンテキストでは、EDI 送信ブリッジは契約の送信側に相当し、Contoso から Northwind に EDIFACT 請求書を送信するために使用されます。 同様に、EDI 受信ブリッジは契約の受信側に相当し、Northwind からの受信確認を受信するために使用されます。  

### <a name="create-the-trading-partners"></a>取引先を作成する
最初に、Contoso と Northwind 用に取引先を作成します。  

1. BizTalk Services ポータルの **[パートナー]** タブで、**[追加]** をクリックします。
2. [新しいパートナー] ページでパートナー名として「**Contoso**」と入力し、**[保存]** をクリックします。
3. この手順を繰り返し、2 つ目のパートナーとして「 **Northwind**」を作成します。  

### <a name="create-the-agreement"></a>契約を作成する
取引先のビジネス プロファイルの間に取引先契約を作成します。 このソリューションでは、パートナーの作成時に自動的に作成される既定のパートナー プロファイルを使用します。  

1. BizTalk Services ポータルで、**[契約]**  >  **[追加]** の順にクリックします。
2. 新しい契約の **[全般設定]** ページで、下図に示すように値を指定し、**[続行]** をクリックします。
   
   ![][3]  
   
   **[続行]** をクリックすると、**[受信の設定]** タブと **[送信の設定]** タブが利用できるようになります。
3. Contoso と Northwind の間に送信契約を作成します。 この契約によって、Contoso が EDIFACT 請求書を Northwind に送信する方法が制御されます。
   
   1. **[送信の設定]**をクリックします。
   2. **[受信 URL]**、**[変換]**、**[バッチ処理]** の各タブは既定値のままにします。
   3. **[プロトコル]** タブの **[スキーマ]** セクションで、**EFACT_D93A_INVOIC.xsd** スキーマをアップロードします。 このスキーマは、サンプル パッケージと共に提供されています。
      
      ![][4]  
   4. **[トランスポート]** タブで、Service Bus キューの詳細を指定します。 送信側の契約については、**northwindreceive** キューを使用して EDIFACT 請求書を Northwind に送信します。処理中にエラーが発生し、保留になったメッセージがあれば、**suspended** キューにルーティングされます。 これは「**手順 1. Service Bus キューを作成する**」(このトピック) で作成したキューです。
      
      ![][5]  
      
      **[トランスポート設定] > [トランスポートの種類]** と **[メッセージ保留の設定] > [トランスポートの種類]** で、[Azure Service Bus] を選択し、図に示すように値を入力します。
4. Contoso と Northwind の間に受信契約を作成します。 この契約によって、Contoso が Northwind から受信確認を受信する方法が制御されます。
   
   1. **[受信の設定]**をクリックします。
   2. **[トランスポート]** タブと **[変換]** タブは既定値のままにします。
   3. **[プロトコル]** タブの **[スキーマ]** セクションで、**EFACT_4.1_CONTRL.xsd** スキーマをアップロードします。 このスキーマは、サンプル パッケージと共に提供されています。
   4. **[ルート]** タブで、Northwind から Contoso にのみ受信確認を送信するためのフィルターを作成します。 **[ルートの設定]** で、**[追加]** をクリックし、ルーティング フィルターを作成します。
      
      ![][6]  
      
      1. **[ルール名]**、**[ルート ルール]**、**[ルーティング先]** に、図に示すように値を入力します。
      2. [ **Save**] をクリックします。
   5. **[ルート]** タブに戻り、保留受信確認 (処理中にエラーが発生した受信確認) の送信先を指定します。 トランスポートの種類を Azure Service Bus に、ルーティング先を**キュー**に、認証の種類を **Shared Access Signature** (SAS) に設定します。Service Bus 名前空間に SAS 接続文字列を入力し、キュー名に「**suspended**」と入力します。
5. 最後に、 **[デプロイ]** をクリックし、契約をデプロイします。 送信契約と受信契約がデプロイされるエンドポイントを書き留めます。
   
   * **[送信の設定]** タブの **[受信 URL]** に表示されたエンドポイントを書き留めます。 EDI 送信ブリッジを使用して Contoso から Northwind にメッセージを送信するには、このエンドポイントにメッセージを送信する必要があります。
   * **[受信の設定]** タブの **[トランスポート]** に表示されたエンドポイントを書き留めます。 EDI 受信ブリッジを使用して Northwind から Contoso にメッセージを送信するには、このエンドポイントにメッセージを送信する必要があります。  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>手順 3. BizTalk Services プロジェクトを作成してデプロイする
前の手順で、EDIFACT 請求書と受信確認を処理するために EDI 送信契約と EDI 受信契約をデプロイしました。 これらの契約では、標準の EDIFACT メッセージ スキーマに準拠するメッセージのみを処理できます。 ただし、このソリューションのシナリオでは、Contoso は社内独自のスキーマで Northwind に請求書を送信します。 そのため、メッセージは、EDI 送信ブリッジに送信される前に、社内スキーマから標準の EDIFACT 請求書スキーマに変換される必要があります。 これを実行するのが BizTalk Services EAI プロジェクトです。

メッセージ変換用の BizTalk Services プロジェクト **InvoiceProcessingBridge**も、ダウンロードしたサンプルに含まれています。 プロジェクトには、次のアーティファクトが含まれています。

* **INHOUSEINVOICE.XSD** – Northwind に送信される社内請求書スキーマ
* **EFACT_D93A_INVOIC.XSD** – 標準の EDIFACT 請求書のスキーマ。
* **EFACT_4.1_CONTRL.XSD** – Northwind が Contoso に送信する EDIFACT 受信確認のスキーマ。
* **INHOUSEINVOICE_to_D93AINVOIC.TRFM** – 社内請求書スキーマを標準 EDIFACT 請求書スキーマにマッピングする変換。  

### <a name="create-the-biztalk-services-project"></a>BizTalk Services プロジェクトを作成する
1. Visual Studio ソリューションで、InvoiceProcessingBridge プロジェクトを展開し、 **MessageFlowItinerary.bcs** ファイルを開きます。
2. キャンバスの任意の場所をクリックし、[プロパティ] ボックスの **[BizTalk サービスの URL]** に BizTalk Services サブスクリプション名を指定します。 たとえば、「 `https://contosowabs.biztalk.windows.net`」のように入力します。
   
   ![][7]  
3. ツールボックスから、 **XML 一方向ブリッジ** をキャンバスにドラッグします。 ブリッジの **[エンティティ名]** プロパティと **[相対アドレス]** プロパティを **ProcessInvoiceBridge** に設定します。 **[ProcessInvoiceBridge]** をダブルクリックし、ブリッジ構成画面を開きます。
4. **[メッセージの種類]** ボックスで、プラス (**+**) ボタンをクリックし、受信メッセージのスキーマを指定します。 EAI ブリッジの受信メッセージは常に社内請求書であるため、これを **[INHOUSEINVOICE]**に設定します。
   
   ![][8]  
5. **[XML 変換]** 図形をクリックし、[プロパティ] ボックスで、**[マップ]** プロパティの省略記号 (**...**) ボタンをクリックします。 **[マップの選択]** ダイアログ ボックスで、**INHOUSEINVOICE_to_D93AINVOIC** 変換ファイルを選択し、**[OK]** をクリックします。
   
   ![][9]  
6. **MessageFlowItinerary.bcs** に戻り、ツールボックスから、**双方向外部サービス エンドポイント**を **ProcessInvoiceBridge** の右にドラッグします。 その **[エンティティ名]** プロパティを **EDIBridge** に設定します。
7. ソリューション エクスプローラーで、**MessageFlowItinerary.bcs** を展開し、**EDIBridge.config** ファイルをダブルクリックします。 **EDIBridge.config** の内容を以下のコードに置き換えます。
   
   > [!NOTE]
   > .config ファイルの編集が必要になるのには理由があります。 ブリッジ デザイナー キャンバスに追加した外部サービス エンドポイントは、前にデプロイした EDI ブリッジを表しています。 EDI ブリッジは送信側と受信側がある双方向ブリッジです。 一方、ブリッジ デザイナーに追加した EAI ブリッジは一方向ブリッジです。 そこで、2 つのブリッジそれぞれのメッセージ交換パターンを処理するために、.config ファイルに設定を追加して、カスタムのブリッジ動作を使用しています。 また、このカスタムの動作は EDI 送信ブリッジ エンドポイントの認証も処理します。このカスタムの動作は、独立したサンプルとして、「[BizTalk Services Bridge chaining sample - EAI to EDI (BizTalk Services ブリッジの EAI から EDI へのチェーンのサンプル)](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104)」で提供されています。 このソリューションでは、このサンプルを再利用しています。  
   > 
   > 
   
   ```
   <?xml version="1.0" encoding="utf-8"?>
   <configuration>
     <system.serviceModel>
       <extensions>
         <behaviorExtensions>
           <add name="BridgeAuthentication" 
                 type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
         </behaviorExtensions>
       </extensions>
       <behaviors>
         <endpointBehaviors>
           <behavior name="BridgeAuthenticationConfiguration">
             <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
             <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                   issuername="owner" 
                                   issuersecret="[YOUR ACS SECRET]" />
             <webHttp />
           </behavior>
         </endpointBehaviors>
       </behaviors>
       <bindings>
         <webHttpBinding>
           <binding name="BridgeBindingConfiguration">
             <security mode="Transport" />
           </binding>
         </webHttpBinding>
       </bindings>
       <client>
         <clear />
         <!--
           Go BizTalk Portal > Agreement > Send Settings > Inbound URL
           Copy the Endpoint URL and paste it in the below address field
         -->
         <endpoint name="TwoWayExternalServiceEndpointReference1" 
                   address="[YOUR EDI BRIDGE SEND URI]" 
                   behaviorConfiguration="BridgeAuthenticationConfiguration" 
                   binding="webHttpBinding" 
                   bindingConfiguration="BridgeBindingConfiguration" 
                   contract="System.ServiceModel.Routing.IRequestReplyRouter" />
       </client>
     </system.serviceModel>
   </configuration>
   
   ```
8. EDIBridge.config ファイルを更新して、構成の詳細を追加します。
   
   * *<behaviors>* で、ACS 名前空間と、BizTalk Services サブスクリプションに関連付けられているキーを入力します。
   * *<client>* で、EDI 送信契約がデプロイされるエンドポイントを入力します。
   
   変更を保存し、構成ファイルを閉じます。
9. ツールボックスの **[コネクタ]** をクリックし、**ProcessInvoiceBridge** コンポーネントと **EDIBridge** コンポーネントを結合します。 コネクタを選択し、[プロパティ] ボックスで、**[フィルター条件]** を **[すべてに一致]** に設定します。 これにより、EAI ブリッジで処理されたすべてのメッセージが EDI ブリッジに送信されます。
   
   ![][10]  
10. ソリューションの変更を保存します。  

### <a name="deploy-the-project"></a>プロジェクトをデプロイする
1. BizTalk Services プロジェクトを作成したコンピューターに、BizTalk Services サブスクリプションの SSL 証明書をダウンロードし、インストールします。 BizTalk Services で **[ダッシュボード]** をクリックし、**[SSL 証明書のダウンロード]** をクリックします。 証明書をダブルクリックし、画面の指示に従ってインストールを完了します。 必ず **[信頼されたルート証明機関]** 証明書ストアに証明書をインストールします。
2. Visual Studio ソリューション エクスプローラーで、**[InvoiceProcessingBridge]** プロジェクトを右クリックし、**[デプロイ]** をクリックします。
3. 図に示すように値を入力し、 **[デプロイ]**をクリックします。 BizTalk Services ダッシュボードから **[接続情報]** をクリックすると、BizTalk Services の ACS 資格情報を取得できます。
   
   ![][11]  
   
   出力ペインで、EAI ブリッジがデプロイされているエンドポイントをコピーします (例: `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`)。 このエンドポイント URL は後で必要になります。  

## <a name="step-4-test-the-solution"></a>手順 4. ソリューションをテストする
このトピックでは、サンプルの一部として提供される **チュートリアル クライアント** アプリケーションを使用して、ソリューションをテストする方法について説明します。  

1. Visual Studio で、F5 を押して、 **チュートリアル クライアント**を起動します。
2. この画面には、Service Bus キューを作成する手順で用いた値が事前入力されています。 **[次へ]**をクリックします。
3. 次のウィンドウで、BizTalk Services サブスクリプションの ACS 資格情報と、EAI ブリッジおよび EDI (受信) ブリッジがデプロイされているエンドポイントを入力します。
   
   EAI ブリッジのエンドポイントは前の手順でコピーしています。 EDI 受信ブリッジのエンドポイントは、BizTalk Services ポータルで、[契約]、[受信の設定]、[トランスポート]、[エンドポイント] の順に進んで確認できます。
   
   ![][12]  
4. 次のウィンドウで、Contoso の **[社内請求書の送信]** ボタンをクリックします。 [ファイルを開く] ダイアログ ボックスで、INHOUSEINVOICE.txt ファイルを開きます。 ファイルの内容を確認し、 **[OK]** をクリックして請求書を送信します。
   
   ![][13]  
5. 数秒後に、Northwind に請求書が届きます。 **[メッセージの表示]** リンクをクリックし、Northwind が受信した請求書を確認します。 Northwind が受信した請求書が標準 EDIFACT スキーマであり、Contoso が送信した請求書が社内スキーマである点に注目してください。
   
   ![][14]  
6. 請求書を選択し、 **[受信確認の送信]**をクリックします。 ダイアログ ボックスが表示されたら、受信された請求書と送信される受信確認でインターチェンジ ID が同じであることに注目してください。 **[受信確認の送信]** ダイアログ ボックスで [OK] をクリックします。
   
   ![][15]  
7. 数秒後に、Contoso に受信確認が届きます。
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>手順 5 (任意). EDIFACT 請求書を一括送信する
BizTalk Services EDI ブリッジでは、メッセージを一括送信することもできます。 この機能は、受信側のパートナーがメッセージを個別に受信するのではなく、(特定の基準を満たす) メッセージを一括で受信したい場合に役に立ちます。

このバッチ処理で最も重要な要素は、バッチの実際のリリースです。これはリリース条件とも呼ばれます。 リリース条件は、受信パートナーが希望するメッセージの受信方法に基づいて作成できます。 バッチ処理が有効になっている場合、EDI ブリッジはリリース条件が満たされるまで送信メッセージを受信パートナーに送信しません。 たとえば、メッセージ サイズを基準にするバッチ条件の場合、"n" 通のメッセージがたまってからバッチが送信されます。 バッチ条件は時間を基準にして作成することもできます。毎日指定した時間にバッチを送信できます。 このソリューションでは、メッセージ サイズを基準にした条件を試します。

1. BizTalk Services ポータルで、前に作成した契約をクリックします。 [送信の設定]、[バッチ処理]、[バッチの追加] の順にクリックします。
2. バッチ名に「**InvoiceBatch**」と入力し、説明を入力して、**[次へ]** をクリックします。
3. 一括処理するメッセージを定義するバッチ条件を指定します。 このソリューションでは、すべてのメッセージを一括処理します。 そのため、[詳細な定義を使用する] オプションを選択し、「 **1 = 1**」と入力します。 これは常に真になる条件であるため、すべてのメッセージが一括処理されます。 **[次へ]**をクリックします。
   
   ![][17]  
4. バッチ リリース条件を指定します。 ドロップダウン リストから、**[MessageCountBased]** を選択し、**[カウント]** に「**3**」を指定します。 これで、3 つのメッセージをまとめたバッチが Northwind に送信されます。 **[次へ]**をクリックします。
   
   ![][18]  
5. 概要を確認し、 **[保存]**をクリックします。 **[デプロイ]** をクリックし、契約を再デプロイします。
6. **チュートリアル クライアント**に戻り、**[社内請求書の送信]** をクリックし、画面の指示に従って請求書を送信します。 バッチ サイズに到達していないため、請求書は Northwind に届きません。 3 つの請求書メッセージが Northwind に届くように、この手順をさらに 2 回繰り返します。 これで 3 通のメッセージというバッチ リリース条件が満たされたため、Northwind に請求書が届きます。

<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG




<!--HONumber=Dec16_HO2-->


