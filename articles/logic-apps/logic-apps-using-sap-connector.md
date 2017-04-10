---
title: "Azure Logic Apps でオンプレミスの SAP システムに接続する | Microsoft Docs"
description: "オンプレミス データ ゲートウェイを使用して、ロジック アプリ ワークフローのオンプレミスの SAP システムに接続する"
services: logic-apps
documentationcenter: 
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 72ac4936c656847fa07f1c1a37d6ddbf4ec1acf4
ms.openlocfilehash: 62b30acf324a5ed6a1b817157c86575d83b4104e


---
# <a name="use-the-sap-connector-in-your-logic-app"></a>ロジック アプリで SAP コネクタを使用する 

オンプレミス データ ゲートウェイを使用すると、データを管理し、オンプレミスのリソースに安全にアクセスできます。 このトピックでは、HTTP 経由で IDOC を要求して返信を送信することで、オンプレミスの SAP システムに接続する方法について説明します。    

 > [!NOTE]
> 現時点での制限事項:
 > - 90 秒を超える要求があると Logic Apps がタイムアウトします。 このシナリオでは、要求がブロックされる可能性があります。 
 > - 利用可能なフィールドの一部がファイル ピッカーに表示されません。 このシナリオでは、手動でパスを追加することができます。

## <a name="prerequisites"></a>前提条件
- 最新の[オンプレミス データ ゲートウェイ](https://www.microsoft.com/download/details.aspx?id=53127) バージョン 1.15.6150.1 以降をインストールして構成します。 [ロジック アプリでのオンプレミス データ ゲートウェイへの接続](http://aka.ms/logicapps-gateway)に関するページに、この手順が記載されています。 続行する前に、オンプレミスのコンピューターにゲートウェイをインストールする必要があります。

- データ ゲートウェイをインストールしたのと同じコンピューターに、最新の SAP クライアント ライブラリをダウンロードし、インストールします。 次のいずれかの SAP バージョンを使用してください。 
    - SAP サーバー
        - SAP ECC 6.0 Unicode
        - SAP ECC 6.0 Unicode EHP 4.0
        - SAP ECC 6.0 EHP 7.0 およびそれ以前のすべての EHP バージョン
 
    - SAP クライアント
        - SAP RFC SDK 7.20 UNICODE
        - SAP .NET Connector (NCo) 3.0

## <a name="add-the-sap-connector"></a>SAP コネクタの追加

SAP コネクタにはアクションがありますが、 トリガーはありません。 そのため、ワークフローの開始時には別のトリガーを使用します。 

1. 要求/応答のトリガーを追加して、**[新しいステップ]** を選択します。
2. **[アクションの追加]** を選択し、検索フィールドに「`SAP`」と入力して SAP コネクタを選択します。    
 ![SAP アプリケーション サーバーまたは SAP メッセージ サーバーの選択](media/logic-apps-using-sap-connector/picture1.png)

3. SAP のセットアップに応じて、**SAP** [アプリケーション サーバー](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)か[メッセージ サーバー](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)を選択します。 既存の接続がない場合は、作成するよう求めるメッセージが表示されます。 
    1. **[Connect via on-premises data gateway (オンプレミス データ ゲートウェイ経由で接続)]** を選択し、SAP システムの詳細を入力します。   
 ![SAP への接続文字列を追加する](media/logic-apps-using-sap-connector/picture2.png)  
    2. 既存の**ゲートウェイ**を選択するか、 **[ゲートウェイのインストール]** を選択して新しいゲートウェイをインストールします。    
 ![新しいゲートウェイのインストール](media/logic-apps-using-sap-connector/install-gateway.png)
  
    3. すべての詳細を入力したら、**[作成]** を選択します。 Logic Apps により、接続が構成およびテストされ、適切に機能していることが確認されます。

4. SAP 接続の名前を入力します。

5. いくつかオプションがあるので、 ファイル ピッカーを使用して IDOC カテゴリを探すか、 パスを手動で入力して、**body** フィールドの HTTP 応答を選択します。     
 ![SAP アクション](media/logic-apps-using-sap-connector/picture3.png)

6. 新しいアクションを追加して HTTP 応答を作成します。 応答メッセージは、SAP の出力結果からのものにする必要があります。

7. ロジック アプリを保存し、 HTTP トリガー URL を通じて IDOC を送信してテストします。 IDOC が送信されたら、ロジック アプリからの応答を待機します。   

  > [!TIP]
  > [Logic Apps を監視する](../logic-apps/logic-apps-monitor-your-logic-apps.md)方法を確認してください。

SAP コネクタがロジック アプリに追加されたので、他の機能も見てみましょう。

  - BAPI
  - RFC

## <a name="next-steps"></a>次のステップ
- [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) で検証、変換、および BizTalk に似たその他の機能を使用する。 
- Logic Apps への[オンプレミス接続](../logic-apps/logic-apps-gateway-connection.md)を作成します。



<!--HONumber=Feb17_HO1-->


