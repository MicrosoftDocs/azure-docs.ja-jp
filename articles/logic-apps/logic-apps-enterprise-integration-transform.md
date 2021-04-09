---
title: XML を形式間で変換する
description: Azure Logic Apps と Enterprise Integration Pack でデータ形式間の XML 変換 (マップ) を作成します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 038c1d4c0f0b5ffd7b9aabea2de32e3a44e3b221
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654134"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Azure Logic Apps と Enterprise Integration Pack でデータ形式間の XML 変換マップを作成する

Enterprise Integration Transform コネクタは、データをある形式から別の形式に変換します。 たとえば、受信メッセージに YearMonthDay 形式で現在の日付が含まれていたとします。 この日付を、変換を使用して MonthDayYear 形式に変更できます。

## <a name="what-does-a-transform-do"></a>変換でできること
変換 (マップともいう) は、送信元 XML スキーマ (入力) と送信先 XML スキーマ (出力) で構成されます。 さまざまな組み込み関数を使用すると、文字列操作、条件付き割り当て、算術式、日時の書式設定や、ループ構造の操作などで、データを操作および制御できます。

## <a name="how-to-create-a-transform"></a>変換を作成する方法
Visual Studio [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas)を使用して、変換/マップを作成できます。 変換の作成とテストが終了したら、変換を統合アカウントにアップロードします。 

## <a name="how-to-use-a-transform"></a>変換を使用する方法
変換/マップを統合アカウントにアップロードしたら、それを使ってロジック アプリを作成できます。 ロジック アプリは、ロジック アプリがトリガーされたとき (かつ変換が必要な入力コンテンツがあるとき) に変換を実行します。

**変換を使用する手順は次のとおりです**。

### <a name="prerequisites"></a>前提条件

* 統合アカウントを作成してマップを追加する  

前提条件が整ったら、ロジック アプリの作成を開始します。  

1. ロジック アプリを作成し、マップが含まれている[統合アカウントにリンク](./logic-apps-enterprise-integration-create-integration-account.md "ロジック アプリに統合アカウントをリンクする方法を学習する")します。
2. ロジック アプリに **Request** トリガーを追加します  
   ![Request トリガーが選択されている "Microsoft のマネージド API を表示" ドロップダウンのスクリーンショット。 このドロップダウンは Visual Studio Enterprise Integration SDK を使用して作成されたロジック アプリにあります。](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. まず **[アクションの追加]**  を選択し、 **[XML の変換]** アクションを追加します。  
   !["アクションの追加" ボタンのスクリーンショット。Request トリガー画面で選択されています。](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. 検索ボックスに「*transform*」と入力し、すべてのアクションから使用するアクションだけをフィルター処理します  
   ![Request トリガーに追加できるよう、"Microsoft のマネージド API を表示" で XML 変換アクションを検索する方法を示すスクリーンショット。](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. **[XML の変換]** アクションを選択します。   
6. 変換する XML **コンテンツ** を追加します。 HTTP 要求で受信する XML データは、すべて **コンテンツ** として使用することができます。 この例では、ロジック アプリをトリガーした HTTP 要求の本文を選択します。

   > [!NOTE]
   > **[XML の変換]** のコンテンツが XML で記述されていることを確認してください。 コンテンツが XML で記述されていない、または base64 でエンコードされている場合は、コンテンツを処理する式を指定する必要があります。 たとえば、デコードされたコンテンツには ```@base64ToBinary```、XML として処理するコンテンツには ```@xml``` などの[関数](logic-apps-workflow-definition-language.md#functions)を使用できます。
 

7. 変換を実行するのに使用する **マップ** の名前を選択します。 マップが既に統合アカウント内に存在している必要があります。 前の手順で、既にロジック アプリには、マップが含まれる統合アカウントへのアクセス権が付与されています。      
   ![Request トリガーの [XML の変換] 画面のコンテンツ フィールドとマップ フィールドを示すスクリーンショット。](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. 作業内容を保存します。  
    ![Logic Apps デザイナーの [保存] ボタンを示すスクリーンショット。](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

この時点で、マップの設定が終了します。 実際のアプリケーションでは、変換されたデータを SalesForce などの LOB アプリケーション内に格納する必要がある場合があります。 変換の出力は、アクションとして Salesforce に簡単に送信することができます。 

これで、HTTP エンドポイントに要求を送信して、変換をテストすることができます。  


## <a name="features-and-use-cases"></a>機能とユース ケース
* マップで作成される変換は、あるドキュメントから別のドキュメントに名前と住所をコピーするなど、単純な内容になります。 または、設定不要なマップ操作を使用して、より複雑な変換を作成することができます。  
* 文字列、日時の関数などの複合的なマップ操作や関数をすぐに使用できます。  
* スキーマ間でデータを直接コピーできます。 SDK に含まれるマッパーを使用すれば、この操作は送信元スキーマ内の要素から送信先スキーマの対応する部分に線を引くくらい簡単です。  
* マップを作成するときには、作成したすべてのリレーションシップやリンクを示すマップがグラフィカルに表示されます。
* [マップのテスト] 機能を使用してサンプル XML メッセージを追加します。 クリックするだけで、作成したマップをテストして、生成された出力を参照できます。  
* 既存のマップをアップロードできます。  
* XML 形式のサポートが含まれます。

## <a name="advanced-features"></a>高度な機能

### <a name="reference-assembly-or-custom-code-from-maps"></a>マップからのアセンブリまたはカスタム コードの参照 
変換アクションは、外部アセンブリへの参照を含むマップまたは変換もサポートしています。 この機能により、XSLT マップからカスタム .NET コードへの直接の呼び出しが可能になります。 マップでアセンブリを使用するための前提条件を次に示します。

* マップとそのマップから参照されるアセンブリは、[統合アカウントにアップロードする](./logic-apps-enterprise-integration-maps.md)必要があります。 

  > [!NOTE]
  > マップとアセンブリは、特定の順序でアップロードする必要があります。 アセンブリは、そのアセンブリを参照するマップをアップロードする前にアップロードする必要があります。

* マップには、次の属性とアセンブリ コードへの呼び出しを含む CDATA セクションも必要です。

    * **name** は、カスタム アセンブリの名前です。
    * **namespace** は、カスタム コードが含まれているアセンブリ内の名前空間です。

  この例は、"XslUtilitiesLib" という名前のアセンブリを参照し、そのアセンブリから `circumreference` メソッドを呼び出すマップを示しています。

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
   <circles>
    <xsl:for-each select="circle">
      <circle>
        <xsl:copy-of select="node()"/>
          <circumference>
            <xsl:value-of select="user:circumference(radius)"/>
          </circumference>
      </circle>
    </xsl:for-each>
   </circles>
  </xsl:template>
    </xsl:stylesheet>
  ```


### <a name="byte-order-mark"></a>バイト オーダー マーク
既定では、変換からの応答はバイト オーダー マーク (BOM) から開始します。 この機能には、コード ビュー エディターでの作業中にのみアクセスできます。 この機能を無効にするには、`transformOptions` プロパティに `disableByteOrderMark` を指定します。

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```





## <a name="learn-more"></a>詳細情報
* [Enterprise Integration Pack についての詳細情報](../logic-apps/logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack について学習する")  
* [マップについての詳細情報](../logic-apps/logic-apps-enterprise-integration-maps.md "Enterprise Integration マップについて学習する")  