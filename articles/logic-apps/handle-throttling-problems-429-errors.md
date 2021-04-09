---
title: 調整の問題、または '429 - 要求が多すぎます' エラーを処理する
description: Azure Logic Apps で、調整の問題、または 'HTTP 429 要求が多すぎます' エラーを回避する方法
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: ea153b1927a337be29c2eb69e2417cc250abf5e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94366053"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Azure Logic Apps で調整の問題 (429 - "要求が多すぎます" エラー) を処理する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) では、調整の発生時にロジック アプリから ["HTTP 429 要求が多すぎます" エラー](https://developer.mozilla.org/docs/Web/HTTP/Status/429)が返されます。これは、一定時間にわたって要求数が送信先の処理速度を超えた場合に発生します。 調整により、データ処理の遅延、パフォーマンス速度の低下、指定した再試行ポリシーの超過などのエラーといった問題が発生する可能性があります。

![SQL Server コネクタでの調整](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

ロジック アプリで発生する可能性のある一般的な調整の種類を次に示します。

* [ロジック アプリ](#logic-app-throttling)
* [コネクタ](#connector-throttling)
* [接続先のサービスまたはシステム](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>ロジック アプリの調整

Azure Logic Apps サービスには、独自の[スループット制限](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)があります。 そのため、ロジック アプリがこれらの制限を超えると、特定のインスタンスや実行だけでなく、ロジック アプリのリソースが調整されます。

このレベルの調整イベントを見つけるには、Azure portal のロジック アプリの **[メトリック]** ペインを確認します。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. ロジック アプリのメニューで **[監視]** の下にある **[メトリック]** を選択します。

1. **[グラフのタイトル]** で、 **[メトリックの追加]** を選択して、既存のメトリックに追加します。

1. 最初のメトリック バーの **[メトリック]** リストから **[アクションの調整イベント]** を選択します。 2 つ目のメトリック バーで、 **[メトリック]** リストから **[トリガー調整イベント]** を選択します。

このレベルで調整を処理するには、次の選択肢があります。

* 同時に実行できるロジック アプリ インスタンスの数を制限します。

  既定では、ロジック アプリのトリガー条件が同時に複数回満たされた場合、ロジック アプリの複数のトリガー インスタンスが同時に (つまり "*並列して*") 実行されます。 この動作は、先行するワークフロー インスタンスが実行を完了する前に個々のトリガー インスタンスが開始されることを意味します。

  同時に実行できるトリガー インスタンスの既定数は[無制限](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)ですが、[トリガーのコンカレンシー設定をオンにする](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)ことでこの数を制限できます。また、必要に応じて、既定値以外の制限を選択できます。

* 高スループット モードを有効にします。

  ロジック アプリには、[5 分間のローリング間隔で実行できるアクションの数に既定の制限](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)があります。 この制限を最大アクション数に引き上げるには、ロジック アプリで[高スループット モード](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode)をオンにします。

* トリガーの配列のデバッチ ("分割") 動作を無効にします。

  処理する残りのワークフロー アクションの配列がトリガーから返される場合、トリガーの [ **[分割]** 設定](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)によって配列項目が分割され、各配列項目のワークフロー インスタンスが開始され、[ **[分割] 制限**](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)を上限として複数の同時実行が効率的にトリガーされます。 調整を制御するには、 **[分割]** 動作をオフにし、呼び出しごとに 1 つの項目を処理するのではなく、ロジック アプリから 1 回の呼び出しで配列全体を処理するようにします。

* アクションを小さなロジック アプリにリファクタリングします。

  前述のように、ロジック アプリは [5 分間で実行できるアクションの既定数](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)に制限されています。 [高スループット モード](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode)を有効にするとこの制限を引き上げることができますが、各ロジック アプリで実行されるアクションの数が制限を超えないようにロジック アプリのアクションを小さなロジック アプリに分割するかどうかを検討することもできます。 このように、1 つのロジック アプリ リソースの負荷を軽減し、複数のロジック アプリに負荷を分散します。 このソリューションは、大規模なデータ セットを処理するアクションや、同時に実行されるアクション、ループ反復、または各ループ反復内のアクションがアクション実行の制限を超えるほど多くなるアクションに適しています。

  たとえば、このロジック アプリでは、SQL Server データベースからテーブルを取得するためのすべての作業を実行し、各テーブルから行を取得します。 **For each** ループは各テーブルで同時に反復されるため、**Get rows** アクションから各テーブルの行が返されます。 こうしたテーブルのデータ量によっては、これらのアクションがアクション実行の制限を超える可能性があります。

  ![リファクタリング "前" のロジック アプリ](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  リファクタリングが完了すると、ロジック アプリは親と子のロジック アプリになります。 親では SQL Server からテーブルを取得し、各テーブルの子ロジック アプリを呼び出して行を取得します。

  ![1 つのアクション用のロジック アプリを作成する](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  親ロジック アプリによって呼び出され、各テーブルの行を取得する子ロジック アプリを次に示します。

  ![2 つ目のアクション用に別のロジック アプリを作成する](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>コネクタの調整

各コネクタには、独自の調整制限があります。これについては、コネクタのテクニカル リファレンス ページを参照してください。 たとえば、[Azure Service Bus コネクタ](/connectors/servicebus/)には、1 分あたり最大 6,000 回の呼び出しを許可する調整制限がありますが、SQL Server コネクタには、[操作の種類に基づいて変化する調整制限](/connectors/sql/)があります。

HTTP などの一部のトリガーとアクションには、[再試行ポリシーの制限](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits)に基づいてカスタマイズして例外処理を実装することができる ["再試行ポリシー"](../logic-apps/logic-apps-exception-handling.md#retry-policies) があります。 このポリシーを使用すると、元の要求が失敗するかタイムアウトして 408、429、または 5xx 応答の結果になったときに、トリガーまたはアクションで要求を再試行するかどうかと、その頻度を指定できます。 そのため、調整が開始され、429 エラーが返された場合、Logic Apps は、サポートされていれば再試行ポリシーに従います。

トリガーまたはアクションで再試行ポリシーがサポートされているかどうかを調べるには、トリガーまたはアクションの設定を確認します。 トリガーまたはアクションの再試行回数を確認するには、ロジック アプリの実行履歴にアクセスして、確認する実行を選択し、そのトリガーまたはアクションを展開して、入力、出力、および再試行の詳細を表示します。次に例を示します。

![アクションの実行履歴、再試行、入力、および出力を確認する](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

再試行履歴にはエラー情報が含まれていますが、コネクタの調整と[接続先の調整](#destination-throttling)を区別できないことがあります。 このような場合は、必要に応じて応答の詳細を確認するか、いくつかの調整間隔の計算を実行してソースを特定します。

グローバルなマルチテナント Azure Logic Apps サービスのロジック アプリの場合、"*接続*" レベルで調整が行われます。 そのため、たとえば、[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) で実行されているロジック アプリの場合、グローバルなマルチテナント Logic Apps サービスで実行されるため、ISE 以外の接続でも調整が行われます。 ただし、ISE コネクタによって作成された ISE 接続は、ISE で実行されるため、調整されません。

このレベルで調整を処理するには、次の選択肢があります。

* 処理のためにロジック アプリでデータを分割するように 1 つのアクションに対して複数の接続を設定します。

  この方法では、同じ資格情報を使用して、同じ接続先への複数の接続にアクションの要求を分割することにより、ワークロードを分散できるかどうかを検討します。

  たとえば、ロジック アプリで SQL Server データベースからテーブルを取得し、各テーブルから行を取得するとします。 処理する必要がある行数に基づいて、複数の接続と複数の **For each** ループを使用して、処理のために行の総数を少数のセットに分割できます。 このシナリオでは、2 つの **For each** ループを使用して、行の総数を半分に分割します。 最初の **For each** ループでは、前半を取得する式を使用します。 もう 1 つの **For each** ループでは、後半を取得する別の式を使用します。次に例を示します。<p>

    * 式 1:`take()` 関数を使用してコレクションの前部を取得します。 詳細については、[ **`take()`** 関数](workflow-definition-language-functions-reference.md#take)に関するページを参照してください。

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * 式 2:`skip()` 関数を使用してコレクションの前部を削除し、他のすべてのアイテムを返します。 詳細については、[ **`skip()`** 関数](workflow-definition-language-functions-reference.md#skip)に関するページを参照してください。

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    これらの式を使用する方法を示す視覚的な例を次に示します。

    ![1 つのアクションに対して複数の接続を作成して使用する](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* アクションごとに異なる接続を設定します。

  この方法では、アクションが同じサービスまたはシステムに接続して同じ資格情報を使用する場合でも、各アクションの要求を独自の接続に分散することでワークロードを分散できるかどうかを検討します。

  たとえば、ロジック アプリで SQL Server データベースからテーブルを取得し、各テーブルの各行を取得するとします。 テーブルの取得に 1 つの接続を使用し、各行の取得に別の接続を使用するように、個別の接続を使用することができます。

  ![アクションごとに異なる接続を作成して使用する](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* ["For each" ループ](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)のコンカレンシーまたは並列処理を変更します。

  既定では、"For each" ループの反復は、[コンカレンシーの制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)を上限として同時に実行されます。 "For each" ループ内に調整されているコネクタがある場合は、並列で実行されるループの反復回数を減らすことができます。 詳細については、以下のトピックを参照してください。
  
  * ["For each" ループ - 反復を変更するか、順番に実行する](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [ワークフロー定義言語スキーマ - For each ループ](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [ワークフロー定義言語スキーマ - "For each" ループのコンカレンシーを変更する](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [ワークフロー定義言語スキーマ - "For each" ループを順番に実行する](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>接続先のサービスまたはシステムの調整

コネクタには独自の調整制限がありますが、コネクタから呼び出される接続先のサービスまたはシステムにも調整制限が存在する場合があります。 たとえば、Microsoft Exchange Server の一部の API には、Office 365 Outlook コネクタよりも厳しい制限があります。

既定では、ロジック アプリのインスタンスと、それらのインスタンス内のすべてのループまたは分岐は *並列して* 実行されます。 この動作は、複数のインスタンスから同時に同じエンドポイントを呼び出すことができることを意味します。 各インスタンスは、他のインスタンスの存在を認識していません。そのため、失敗したアクションを再試行しようとすると、[競合状態](https://en.wikipedia.org/wiki/Race_condition)が発生する可能性があります。この場合、複数の呼び出しの実行が同時に試行されますが、成功するには、調整が開始される前に、これらの呼び出しが接続先のサービスまたはシステムに到達している必要があります。

たとえば、100 個の項目を持つ配列があるとします。 "for each" ループを使用して配列を反復処理し、ループのコンカレンシー制御をオンにして、並列反復の数を 20 個または[現在の既定の制限](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)に制限できます。 このループ内では、アクションによって配列の項目が SQL Server データベースに挿入されます。これにより、1 秒あたり 15 回の呼び出しのみが許可されます。 このシナリオでは、再試行のバックログが蓄積されて実行されないため、調整の問題が発生します。

次の表は、アクションの再試行間隔が 1 秒の場合にループで何が行われるかについてのタイムラインを示しています。

| [特定の時点] | 実行するアクションの数 | 失敗したアクションの数 | 待機の再試行回数 |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 秒 | 20 回の挿入 | 5 回の失敗 (SQL の制限のため) | 5 回の再試行 |
| T + 0.5 秒 | 以前の 5 回の再試行が待機しているため、15 回の挿入 | 以前の SQL の制限がさらに 0.5 秒間有効であるため、15 個すべてが失敗します。 | 20 回の再試行 <br>(以前の 5 回 + 15 回の新規) |
| T + 1 秒 | 20 回の挿入 | 5 回の失敗と以前の 20 回の再試行 (SQL の制限のため) | 25 回の再試行 (以前の 20 回 + 5 回の新規)
|||||

このレベルで調整を処理するには、次の選択肢があります。

* 個々に 1 つの操作を処理するようにロジック アプリを作成します。

  * このセクションの SQL Server シナリオの例を続けて、配列項目を [Azure Service Bus キュー](../connectors/connectors-create-api-servicebus.md)などのキューに格納するロジック アプリを作成できます。 次に、そのキュー内の各項目に対して挿入操作のみを実行する別のロジック アプリを作成します。 このようにすると、特定の時点で実行されるロジック アプリ インスタンスは 1 つのみになります。これにより、挿入操作が完了してキュー内の次の項目に移動するか、インスタンスで 429 エラーを受け取っても無意味な再試行が行われなくなります。

  * アクションごとに子または入れ子になったロジック アプリを呼び出す親ロジック アプリを作成します。 親の結果に基づいて、親から異なる子アプリを呼び出す必要がある場合は、呼び出す子アプリを決定する条件アクションまたは切り替えアクションを使用できます。 このパターンを使うと、呼び出しまたは操作の数を減らすために役立ちます。

    たとえば、2 つのロジック アプリがあり、それぞれに "成功" や "失敗" などの特定の件名についてメール アカウントを毎分確認するポーリング トリガーがあるとします。 この設定の結果、1 時間あたり 120 回の呼び出しが発生します。 代わりに、毎分ポーリングし、件名が "成功" か "失敗" かに基づいて実行される子ロジック アプリを呼び出す 1 つの親ロジック アプリを作成すると、ポーリング確認の数が半分 (この場合は 60) に減ります。

* バッチ処理を設定します。

  接続先サービスがバッチ操作をサポートしている場合は、項目を個別にではなく、グループまたはバッチで処理することで、調整に対処できます。 バッチ処理ソリューションを実装するには、"バッチ レシーバー" ロジック アプリと "バッチ センダー" ロジック アプリを作成します。 バッチ センダーでは、指定された条件が満たされるまでメッセージまたは項目を収集し、それらのメッセージまたは項目を 1 つのグループにまとめて送信します。 バッチ レシーバーでは、そのグループを受け入れ、それらのメッセージや項目を処理します。 詳細については、[メッセージをグループにまとめてバッチ処理する](../logic-apps/logic-apps-batch-process-send-receive-messages.md)方法に関するページを参照してください。

* トリガーとアクションには、ポーリング バージョンではなく、Webhook バージョンを使用します。

  なぜですか? ポーリング トリガーでは、特定の間隔で接続先サービスまたはシステムが引き続き確認されます。 間隔が非常に頻繁な場合は (毎秒など)、調整の問題が生じる可能性があります。 ただし、[HTTP Webhook](../connectors/connectors-native-webhook.md) などの Webhook トリガーまたはアクションでは、接続先サービスまたはシステムに対する 1 回の呼び出しのみを作成します。これはサブスクリプション時に行われ、イベントが発生したときにのみ接続先からトリガーまたはアクションに通知するように要求されます。 このようにすると、トリガーまたはアクションで接続先を継続的に確認する必要がなくなります。
  
  そのため、接続先サービスまたはシステムが Webhook をサポートしている場合、または Webhook バージョンを持つコネクタを用意している場合、この方法はポーリング バージョンを使用するよりも優れています。 Webhook トリガーとアクションを識別するには、その種類が `ApiConnectionWebhook` であること、または繰り返しを指定する必要がないことを確認します。 詳細については、「[APIConnectionWebhook トリガー](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)」と「[APIConnectionWebhook アクション](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action)」を参照してください。

## <a name="next-steps"></a>次のステップ

* 詳細については、[Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページを参照してください
