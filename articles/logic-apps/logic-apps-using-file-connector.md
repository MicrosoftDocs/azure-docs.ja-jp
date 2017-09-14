---
title: "Azure Logic Apps からオンプレミスのファイル システム フォルダーへの接続 | Microsoft ドキュメント"
description: "オンプレミスのデータ ゲートウェイおよびファイル システム コネクタを使用して、ロジック アプリ ワークフローからオンプレミスのファイル システムに接続します"
keywords: "ファイル システム"
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2017
ms.author: LADocs; deli
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: f33e7c58103c57e17e4e273caba1ab9b83f0cd2b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017

---

# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>ファイル システム コネクタを使用した、Logic Apps からオンプレミスのファイル システムへの接続

ハイブリッド クラウド接続は、Logic Apps の中核です。オンプレミスのデータ ゲートウェイを使用すると、Logic Apps からデータを管理し、オンプレミスのリソースに安全にアクセスできます。 この記事では、Dropbox、ファイル共有にアップロードされたファイルをコピーし、電子メールを送信するという基本的なシナリオで、オンプレミスのファイル システムに接続する方法について説明します。

## <a name="prerequisites"></a>前提条件

- 最新の[オンプレミス データ ゲートウェイ](https://www.microsoft.com/download/details.aspx?id=53127)をインストールして構成します。
- 最新のオンプレミス データ ゲートウェイのバージョン 1.15.6150.1 以降をインストールします。 [オンプレミス データ ゲートウェイへの接続](http://aka.ms/logicapps-gateway)に関するページに、この手順が記載されています。 残りの手順を続行する前に、オンプレミス コンピューターにゲートウェイをインストールする必要があります。

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>ファイル システムに接続するためのトリガーとアクションの追加

1. ロジック アプリを作成し、Dropbox の **[ファイルが作成されたとき]** トリガーを追加します。 
2. トリガー内で、**[次のステップ]** > **[アクションの追加]** の順に選択します。 
3. 検索ボックスで、「`file system`」と入力し、ファイル システム コネクタでサポートされているすべてのアクションを表示します。

   ![ファイル コネクタの検索](media/logic-apps-using-file-connector/search-file-connector.png)

2. **[ファイルの作成]** アクションを選択し、ファイル システムへの接続を作成します。

   既存の接続がない場合は、作成するよう求めるメッセージが表示されます。

   1. **[オンプレミスのデータ ゲートウェイ経由で接続する]** を選択します。 その他のプロパティが表示されます。
   2. ファイル システムのルート フォルダーを選択します。
      
       > [!NOTE]
       > ルート フォルダーはメインの親フォルダーで、すべてのファイル関連のアクションの相対パスに使用されます。 このフォルダーには、オンプレミスのデータ ゲートウェイがインストールされているコンピューターのローカル フォルダー、またはコンピューターがアクセス可能なネットワーク共有を指定できます。

   3. ゲートウェイのユーザー名とパスワードを入力します。
   4. 前の手順でインストールしたゲートウェイを選択します。

       ![接続の構成](media/logic-apps-using-file-connector/create-file.png)

3. すべての詳細を入力したら、**[作成]** を選択します。 

   Logic Apps により、接続の構成とテストが行われ、適切に機能していることが確認されます。 
   接続が正しくセットアップされると、事前に選択したアクションのオプションが表示されます。 
   これで、ファイル システム コネクタを使用する準備ができました。

4. Dropbox からオンプレミスのファイル共有のルート フォルダーにコピーするファイルを指定します。

   ![ファイル アクションの作成](media/logic-apps-using-file-connector/create-file-filled.png)

5. ロジック アプリでファイルがコピーされたら、電子メールを送信する Outlook アクションを追加し、関連のユーザーに新しいファイルについて知らせます。 受信者、タイトル、および電子メールの本文を入力します。 

   動的コンテンツ セレクターで、ファイル コネクタからのデータ出力を選択し、電子メールに詳細を追加できます。

   ![電子メール送信アクション](media/logic-apps-using-file-connector/send-email.png)

6. ロジック アプリを保存し、 Dropbox にファイルをアップロードして、アプリをテストします。 ファイルはオンプレミスのファイル共有にコピーされ、操作に関す電子メール通知が届きます。

   > [!TIP] 
   > 「[Logic Apps を監視する](../logic-apps/logic-apps-monitor-your-logic-apps.md)」方法を確認してください。

これで完了です。オンプレミスのファイル システムに接続するロジック アプリが機能するようになりました。 コネクタが提供するその他の機能をお試しください。機能の例は、次のとおりです。

- ファイルを作成する
- フォルダー内のファイルを一覧表示する
- ファイルを追加する
- ファイルを削除する
- ファイルの内容を取得する
- パスを使用してファイルの内容を取得する
- ファイルのメタデータを取得する
- パスを使用してファイルのメタデータを取得する
- ルート フォルダー内のファイルを一覧表示する
- ファイルを更新する

## <a name="view-the-swagger"></a>Swagger の表示
[Swagger の詳細](/connectors/fileconnector/)を参照してください。 

## <a name="get-help"></a>問い合わせ

[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)では、質問の投稿や質問への回答を行うことができるほか、他の Azure Logic Apps ユーザーがどのようなことを行っているかがわかります。

[Azure Logic Apps ユーザー フィードバック サイト](http://aka.ms/logicapps-wish)でアイデアへの投票やアイデアの投稿を行って、Azure Logic Apps とコネクタの改善にご協力ください。

## <a name="next-steps"></a>次のステップ

- ロジック アプリから[オンプレミスのデータへの接続](../logic-apps/logic-apps-gateway-connection.md)
- [エンタープライズ統合](../logic-apps/logic-apps-enterprise-integration-overview.md)に関する情報をご覧ください

