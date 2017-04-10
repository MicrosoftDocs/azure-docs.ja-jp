---
title: "Logic Apps コネクタの概要 | Microsoft Docs"
description: "ロジック アプリで使用されるコネクタの概要"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: ca8dab2e-9b69-4b1e-865d-1facd9f0cdac
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 44a756ba437b2ce9f625017998d9ae7d09acae1f
ms.openlocfilehash: 9cbb258ae9e32549669623e6824dd9b18fa1f68f
ms.lasthandoff: 01/20/2017


---
# <a name="using-connectors-in-a-logic-app"></a>ロジック アプリでのコネクタの使用
コネクタは、サービス、プロトコル、およびプラットフォームでの、イベント、データ、およびアクションへのすばやいアクセスを提供します。  Logic Apps がサポートするコネクタの完全な一覧については、 [こちらを参照](apis-list.md)してください。  コネクタは、ロジック アプリでトリガーまたはアクションとして使用できますが、使用には構成された *"接続"* が必要な場合があります (例: Twitter アカウントがユーザーの代わりにアクセスしたり投稿することを承認する場合)。

## <a name="basics"></a>基本
コネクタは、ロジック アプリの一部としてアクセスできるホストされたサービスで、Dynamics、Azure、Salesforce [など](apis-list.md)他のサービスとの統合に使用されます。  コネクタのデプロイと管理は Microsoft が行いますので、スケール、スループット、およびセキュリティの管理が万全な、統合ワークフローを構築することができます。  **[Show Microsoft managed API (Microsoft Managed API を表示)]**でコネクタ アクションやトリガーを検索/選択することで、コネクタをロジック アプリに追加できます。

![トリガーを選択する [アクション] メニュー][1]

コネクタ アクションやトリガーには、それぞれ構成する一連のプロパティがあります。  [情報]ボタンをクリックしてアクションの詳細を確認したり、ドキュメントを参照して [詳細を確認](apis-list.md)します。

まだコネクタになっていないサービスや API と統合する場合は、 [カスタム コネクタ](../logic-apps/logic-apps-create-api-app.md) を通じてロジック アプリを拡張するか、または HTTP などのプロトコル経由で直接サービスに連絡します。

## <a name="triggers"></a>トリガー
一部のコネクタはトリガーを備えており、コネクタからのイベントによってロジック アプリが起動し、データがトリガーの一部として渡されます。  トリガーは常に、ロジック アプリの最初の手順です。  一般的なトリガーには次のような操作が含まれます。

* 繰り返し―1 時間おきに実行
* HTTP 要求を受信したとき
* 項目がキューに追加されたとき
* 電子メールを受信したとき

一部のトリガーは、ロジック アプリへの通知を通じてイベントが発生すると直ちに起動しますが、トリガーの中には、ロジック アプリがイベントのサービスをチェックする頻度 (最大 15 秒おき) に定期実行期間を構成する必要があるものもあります。  

イベントを受信すると、ロジック アプリの実行が開始され、ワークフローのアクションが開始されます。  また、ワークフロー全体で、トリガーからのあらゆるデータにアクセスできます (たとえば "新しいツイート" トリガーがツイートを実行に渡します)。

## <a name="actions"></a>アクション
ほとんどのコネクタには、ワークフローの一部として実行される、1 つまたは複数のアクションがあります。  アクションとは、トリガーから実行が開始された後に発生する手順のことです。  アクションを追加するには **[新しい手順]** ボタンをクリックして、使用するコネクタを検索します。  選択すると (そして必要な [接続](#connections) を構成すると) 構成するアクション カードが表示されます。  出力用のトークンをクリックして前の手順からデータを選択したり、必要に応じて他の構成を入力します。

![コネクタ アクションの構成][2]

## <a name="connections"></a>接続
ほとんどのコネクタは、使用する前に *"接続"* を構成する必要があります。  *"接続"* とは、コネクタへのアクセスに必要なログインや接続の構成のことです。  OAuth を使用するコネクタにとって、接続の作成は、アクセス トークンの暗号化と Azure シークレット ストアへの安全な保存が可能なサービス (Office 365、Salesforce、または GitHub など) へのサインインを意味します。  その他のコネクタ (FTP や SQL など) では、サーバーのアドレス、ユーザー名、およびパスワードなどの構成を含む接続が必要です。  これらの接続構成の詳細も暗号化され、安全に保存されます。  接続は、サービスが許可する限り、サービスにアクセスすることができます。  Azure Active Directory の OAuth 接続 (Office 365 や Dynamics など) については、アクセス トークンを無制限に更新し続けることができます。  他のサービスでは、更新せずにトークンを利用できる期間が制限されている場合があります。  一般に、パスワードの変更など特定のアクションで、すべてのアクセス トークンが無効になります。  

Azure で接続の表示と管理を行うには、**[参照]** をクリックして **[API 接続]** を選択します。  API 接続リソースから、作成した接続の表示、編集、更新、および再承認を行います。

## <a name="next-steps"></a>次のステップ
* [初めてのロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)
* [ロジック アプリの一般的な使用方法と例](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Enterprise Integration のトリガーとアクションの使用を開始する](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png

