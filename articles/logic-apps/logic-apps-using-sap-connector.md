---
title: "Azure Logic Apps によるオンプレミスの SAP システムへの接続 | Microsoft ドキュメント"
description: "オンプレミスのデータ ゲートウェイを使用して、ロジック アプリ ワークフローからオンプレミスの SAP システムに接続します"
services: logic-apps
author: padmavc
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: LADocs; padmavc
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 3fea93f558d5a4ef62550fd1f6486903cb812930
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---

# <a name="connect-to-an-on-premises-sap-system-from-logic-apps-with-the-sap-connector"></a>SAP コネクタを使用した、Logic Apps からオンプレミスの SAP システムへの接続 

オンプレミス データ ゲートウェイを使用すると、データを管理し、オンプレミスのリソースに安全にアクセスできます。 このトピックでは、オンプレミスの SAP システムに Logic Apps を接続する方法を説明します。 この例では、ロジック アプリは HTTP 経由で IDOC を要求し、応答を返信します。    

> [!NOTE]
> 現時点での制限事項: 
> - 応答に必要なすべての手順が[要求タイムアウト制限](./logic-apps-limits-and-config.md)内に完了しない場合、ロジック アプリがタイムアウトします。 このシナリオでは、要求がブロックされる可能性があります。 
> - 利用可能なフィールドの一部がファイル ピッカーに表示されません。 このシナリオでは、手動でパスを追加することができます。

## <a name="prerequisites"></a>前提条件

- 最新の[オンプレミス データ ゲートウェイ](https://www.microsoft.com/download/details.aspx?id=53127) バージョン 1.15.6150.1 以降をインストールして構成します。 [ロジック アプリでのオンプレミス データ ゲートウェイへの接続](http://aka.ms/logicapps-gateway)に関するページに、この手順が記載されています。 続行する前に、オンプレミスのコンピューターにゲートウェイをインストールする必要があります。

- データ ゲートウェイをインストールしたのと同じコンピューターに、最新の SAP クライアント ライブラリをダウンロードし、インストールします。 次のいずれかの SAP バージョンを使用してください。 
    - SAP サーバー
        - .NET Connector (NCo) 3.0 をサポートする任意の SAP サーバー
 
    - SAP クライアント
        - SAP .NET Connector (NCo) 3.0

## <a name="add-triggers-and-actions-for-connecting-to-your-sap-system"></a>SAP システムに接続するためのトリガーとアクションの追加

SAP コネクタにはアクションがありますが、トリガーはありません。 そのため、ワークフローの開始時には別のトリガーを使用します。 

1. 要求/応答のトリガーを追加して、**[新しいステップ]** を選択します。

2. **[アクションの追加]** を選択し、検索フィールドに「`SAP`」と入力して SAP コネクタを選択します。    

     ![SAP アプリケーション サーバーまたは SAP メッセージ サーバーの選択](media/logic-apps-using-sap-connector/sap-action.png)

3. SAP のセットアップに応じて、[**SAP アプリケーション サーバー**](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)か [**SAP メッセージ サーバー**](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)を選択します。 既存の接続がない場合は、作成するよう求めるメッセージが表示されます。

   1. **[Connect via on-premises data gateway (オンプレミス データ ゲートウェイ経由で接続)]** を選択し、SAP システムの詳細を入力します。   

       ![SAP への接続文字列を追加する](media/logic-apps-using-sap-connector/picture2.png)  

   2. **[ゲートウェイ]** で、既存のゲートウェイを選択するか、**[ゲートウェイのインストール]** を選択して、新しいゲートウェイをインストールします。

        ![新しいゲートウェイのインストール](media/logic-apps-using-sap-connector/install-gateway.png)
  
   3. すべての詳細を入力したら、**[作成]** を選択します。 
   Logic Apps により、接続の構成とテストが行われ、適切に機能していることが確認されます。

4. SAP 接続の名前を入力します。

5. いくつかオプションがあるので、 IDOC カテゴリを検索するには、一覧から選択選択するか、 パスを手動で入力して、**body** フィールドの HTTP 応答を選択します。

     ![SAP アクション](media/logic-apps-using-sap-connector/picture3.png)

6. アクションを追加して、**HTTP 応答**を作成します。 応答メッセージは、SAP の出力結果からのものにする必要があります。

7. ロジック アプリを保存し、 HTTP トリガー URL を通じて IDOC を送信してテストします。 IDOC が送信されたら、ロジック アプリからの応答を待ちます。   

     > [!TIP]
     > [Logic Apps を監視する](../logic-apps/logic-apps-monitor-your-logic-apps.md)方法を確認してください。

SAP コネクタがロジック アプリに追加されたので、他の機能も見てみましょう。

- BAPI
- RFC

## <a name="get-help"></a>問い合わせ

[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)では、質問の投稿や質問への回答を行うことができるほか、他の Azure Logic Apps ユーザーがどのようなことを行っているかがわかります。

[Azure Logic Apps ユーザー フィードバック サイト](http://aka.ms/logicapps-wish)でアイデアへの投票やアイデアの投稿を行って、Azure Logic Apps とコネクタの改善にご協力ください。

## <a name="next-steps"></a>次のステップ

- [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) で検証、変換、および BizTalk に似たその他の機能を使用する。 
- ロジック アプリから[オンプレミスのデータへの接続](../logic-apps/logic-apps-gateway-connection.md)

