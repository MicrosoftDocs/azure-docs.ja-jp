---
title: "Logic Apps でのファイル コネクタの使用 | Microsoft Docs"
description: "ファイル コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
author: rajeshramabathiran
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 07ceb81a-f8f6-4901-a7a2-06a9ac47efd5
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: rajram
translationtype: Human Translation
ms.sourcegitcommit: 5b1b65e3d1066bea6958fa6461a157ee39fbe7dc
ms.openlocfilehash: 35bd4561f6e8f4482cb084eb293b506eb9269cf7
ms.lasthandoff: 02/15/2017


---
# <a name="get-started-with-the-file-connector-and-add-it-to-your-logic-app"></a>ファイル コネクタの使用開始とロジック アプリへの追加
> [!NOTE]
> 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。
>
>

ファイル システムに接続して、アップロードやダウンロードなど、ホスト コンピューター上のファイルの操作を行います。 ロジック アプリはさまざまなデータ ソースを基にトリガーでき、データの取得と処理のためのコネクタを提供します。 このファイル コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。

ファイル コネクタは、ホスト ファイル システムへのハイブリッド接続にハイブリッド接続マネージャーを使用します。

## <a name="creating-a-file-connector-for-your-logic-app"></a>Logic App 用のファイル コネクタを作成する
ファイル コネクタを使用するには、まずファイル コネクタ API アプリのインスタンスを作成する必要があります。 そのためには、次の手順に従います。

1. Azure ポータルの左側にある [+ 新規] を使用して Azure Marketplace を開きます。
2. "ファイル コネクタ" を検索します。
3. 検索結果から **[File Connector (preview) (ファイル コネクタ (プレビュー))]** を選択します。
4. **[作成]** ボタンを選択します。
5. 次のように、ファイル コネクタを構成します。  
   ![][1]

   * **[名前]** - ファイル コネクタの名前を指定します。
   * **[パッケージの設定]**
     * **[ルート フォルダー]** - ホスト コンピューター上のルート フォルダーのパスを指定します。 例: D:\FileConnectorTest
     * **[Service Bus の接続文字列]** - Service Bus の接続文字列を指定します。 Service Bus 名前空間の種類が、Basic ではなく Standard になっていることを確認し、Service Bus Relay を使用できるようにします。  Service Bus Relay はハイブリッド接続マネージャーへの接続に使用されます。
   * **[App Service プラン]** - App Service プランを選択または作成します。
   * **[価格レベル]** - コネクタの価格レベルを選択します。
   * **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
   * **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
   * **[場所]** - コネクタをデプロイする地域を選択します。
6. [作成] をクリックします。 新しいファイル コネクタが作成されます。

## <a name="configure-hybrid-connection-manager"></a>ハイブリッド接続マネージャーの構成
API App のインスタンスを作成したら、このダッシュボードに移動します。  [参照]、[API Apps]、[ファイル コネクタの API Apps の選択] の順にクリックします。  ここから、ハイブリッド接続マネージャーを構成する必要があります。
ハイブリッド接続マネージャーの構成とトラブルシューティングの詳細については、「 [ハイブリッド接続マネージャーの使用]」を参照してください。

## <a name="using-the-file-connector-in-your-logic-app"></a>Logic App でファイル コネクタを使用する
API アプリが作成されたら、Logic App のアクションとしてファイル コネクタを使用できます。 そのためには、次の手順を実行する必要があります。

1. 新しい Logic App を作成し、ファイル コネクタと同じリソース グループを選択します。 詳細については、 [新しいロジック アプリの作成]に関するページを参照してください。
2. 作成した Logic App で [トリガーとアクション] を選択してロジック アプリ デザイナーを開き、フローを構成します。
3. ファイル コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API Apps)] セクションに表示されます。
4. "ファイル コネクタ" をクリックして、ファイル コネクタの API アプリをエディターにドロップできます。 ファイル コネクタでは、1 つのトリガーと 4 つのアクションが表示されます。  
   ![][5]
5. それぞれに特定のプロパティが表示されます。 次の画像は、トリガーのプロパティと Get File アクションのプロパティを示しています。  
   ![][6]
6. これらを構成すると、フローでトリガーとアクションを使用できるようになります。 同様に、他のアクションも構成できます。

> [!NOTE]
> ファイル トリガーは、ファイルをフォルダーから正常に読み取った後、そのファイルを削除します。
>
>

## <a name="file-connector-rest-apis"></a>ファイル コネクタの REST API
ロジック アプリの外部のコネクタを使用するには、そのコネクタで公開されている REST API を活用できます。 [参照]、[API App]、[ファイル コネクタ] の順に選択して、この API 定義を表示できます。 [概要] セクションの [API の定義] レンズをクリックすると、このコネクタで公開されているすべての API が表示されます。  
![][7]

API の詳細については、 [ファイル コネクタの API の定義]に関するページを参照してください。

## <a name="do-more-with-your-connector"></a>コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。 「 [Logic Apps とは](../logic-apps/logic-apps-what-are-logic-apps.md)」を参照してください。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、[ロジック アプリの試用](https://azure.microsoft.com/try/app-service/logic/)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
>
>

「 [Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。 「[ロジック アプリを監視する](../logic-apps/logic-apps-monitor-your-logic-apps.md)」を参照してください。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[新しいロジック アプリの作成]: app-service-logic-create-a-logic-app.md
[ファイル コネクタの API の定義]: https://msdn.microsoft.com/library/dn936296.aspx
[ハイブリッド接続マネージャーの使用]: ../app-service-web/web-sites-hybrid-connection-get-started.md

