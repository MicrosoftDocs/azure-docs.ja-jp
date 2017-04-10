---
title: "テンプレートからワークフローを作成する - Azure Logic Apps | Microsoft Docs"
description: "Azure ロジック アプリ テンプレートを使用してワークフローをすばやく作成し、アプリに接続してデータを統合する方法の概要を示します。"
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: klam
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: 17486fd41b471356648656b65ca66e1234313d4c
ms.lasthandoff: 03/01/2017


---
# <a name="configure-a-workflow-using-a-pre-built-template-or-pattern-to-get-started-quickly"></a>ビルド済みのテンプレートまたはパターンを使用してワークフローをすばやく構成する

## <a name="what-are-logic-app-templates"></a>ロジック アプリ テンプレートとは
ロジック アプリ テンプレートは、あらかじめ作成されたロジック アプリで、これを使用すると独自のワークフローの作成をすばやく開始できます。 

これらのテンプレートは、ロジック アプリを使用して構築できるさまざまなパターンを見つけるのに適した方法です。 これらのテンプレートは、そのまま使用することも、自分のシナリオに合わせて変更することもできます。

## <a name="overview-of-available-templates"></a>使用可能なテンプレートの概要
ロジック アプリ プラットフォームでは、現在、使用可能な多くのテンプレートが発行されています。 一部のカテゴリの例と、そのカテゴリで使用されるコネクタの種類を次に示します。

### <a name="enterprise-cloud-templates"></a>エンタープライズ クラウド テンプレート
エンタープライズ クラウドのニーズに応じた Dynamics CRM、Salesforce、Box、Azure BLOB などのコネクタを統合するテンプレートです。 これらのテンプレートでできることの例として、潜在顧客の整理や企業のファイル データのバックアップなどがあります。

### <a name="enterprise-integration-pack-templates"></a>Enterprise Integration Pack テンプレート
VETER (検証、抽出、変換、強化、ルーティング) パイプラインの構成、AS2 経由での X12 EDI ドキュメントの受信と XML への変換、X12 メッセージと AS2 メッセージの処理。

### <a name="protocol-pattern-templates"></a>プロトコル パターン テンプレート
これらのテンプレートは、HTTP 経由の要求と応答や FTP と SFTP 間の統合など、プロトコル パターンを含むロジック アプリで構成されます。 これらをそのまま使用するか、より複雑なプロトコル パターンを作成するための基礎として使用します。  

### <a name="personal-productivity-templates"></a>個人的生産性テンプレート
個人的生産性の向上に役立つパターンには、毎日のアラームを設定するテンプレート、重要な作業項目を To Do リストに変換するテンプレート、長期にわたる冗長なタスクを&1; 人のユーザーの承認手順に自動化するテンプレートが含まれます。

### <a name="consumer-cloud-templates"></a>コンシューマー クラウド テンプレート
Twitter、Slack、電子メールなどのソーシャル メディア サービスと統合し、最終的にソーシャル メディア マーケティング戦略を強化できるシンプルなテンプレートです。 これには、従来繰り返しタスクに費やしていた時間を節約することで生産性の向上に役立つ、クラウドのコピーなどのテンプレートも含まれます。 

## <a name="how-to-create-a-logic-app-using-a-template"></a>テンプレートを使用したロジック アプリの作成方法
ロジック アプリ テンプレートの使用を開始するには、ロジック アプリ デザイナーに移動します。 既存のロジック アプリを開いてデザイナーを起動すると、ロジック アプリは自動的にデザイナー ビューに読み込まれます。 一方、新しいロジック アプリを作成する場合は、次の画面が表示されます。  
 ![](../../includes/media/app-service-logic-templates/template7.png)  

この画面で、空のロジック アプリを使用するか、あらかじめ作成されているテンプレートを使用するかを選択できます。 テンプレートのいずれかを選択すると、追加情報が表示されます。 この例では、" *新しいファイルが Dropbox に作成されたときにそのファイルを OneDrive にコピーする* " テンプレートを使用します。  
 ![](../../includes/media/app-service-logic-templates/template2.png)  

このテンプレートを使用することにした場合は、 *[このテンプレートを使用する]* ボタンを選択するだけです。 テンプレートで使用するコネクタに基づいて自身のアカウントにサインインするよう求められます。 また、これらのコネクタとの接続を既に確立している場合は、次に示すように [続行] を選択できます。  
 ![](../../includes/media/app-service-logic-templates/template3.png)  

接続を確立して *[続行]*を選択すると、ロジック アプリがデザイナー ビューで開きます。  
 ![](../../includes/media/app-service-logic-templates/template4.png)  

上記の例では、多くのテンプレートと同様に、必須プロパティ フィールドには、コネクタ内で値が設定されているものもありますが、ロジック アプリを適切にデプロイできるように値の入力が必要なフィールドもあります。 いくつかのフィールドに値を入力しないままデプロイしようとすると、エラー メッセージで通知されます。

テンプレート ビューアーに戻る場合は、上部のナビゲーション バーの *[テンプレート]* ボタンを選択します。 テンプレート ビューアーに戻ると、保存されていない作業は失われます。 テンプレート ビューアーに戻る前に、このことを通知する警告メッセージが表示されます。  
 ![](../../includes/media/app-service-logic-templates/template5.png)  

## <a name="how-to-deploy-a-logic-app-created-from-a-template"></a>テンプレートから作成したロジック アプリをデプロイする方法
テンプレートを読み込んで必要な変更を行ったら、左上隅にある [保存] ボタンをクリックします。 これにより、ロジック アプリが保存され、発行されます。  
 ![](../../includes/media/app-service-logic-templates/template6.png)  

既存のロジック アプリ テンプレートにさらに手順を追加する方法や一般的な編集を行う方法の詳細については、 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関するページを参照してください。


