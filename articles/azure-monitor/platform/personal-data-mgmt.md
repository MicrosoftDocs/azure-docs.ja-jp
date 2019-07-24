---
title: Azure Log Analytics に格納された個人データに関するガイダンス | Microsoft Docs
description: この記事では、Azure Log Analytics に格納された個人データを管理する方法と、それを特定して削除する方法について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.openlocfilehash: 0cf5a80e3eedbe7efb8463162b5b3ed489ac08c8
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59577900"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Log Analytics と Application Insights に格納される個人データに関するガイダンス

Log Analytics は、個人データが存在する可能性が高いデータ ストアです。 Application Insights では、そのデータが Log Analytics パーティションに格納されます。 この記事では、そのようなデータがよく存在する Log Analytics および Application Insights の場所と、そのようなデータを処理するために使用できる機能について説明します。

> [!NOTE]
> この記事では、"_ログ データ_" とは Log Analytics ワークスペースに送信されるデータのことで、"_アプリケーション データ_" とは Application Insights によって収集されるデータのことです。

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>個人データの処理に関する戦略

プライベート データを処理する際の戦略を策定するとしたら、それを最終的に行うのはお客様とその会社になりますが、考えられるアプローチをいくつか以下に紹介します。 これらは、技術的な観点に基づいて最も望ましいものから順に記載されています。

* 可能であれば、データの収集をやめるか、収集されるデータの難読化または匿名化を行う。そうでない場合は、"プライベート" と見なされるデータがなくなるよう、収集されるデータを調整する。 "_圧倒的_" に望ましいのはこのアプローチです。このアプローチでは、非常にコストがかかり影響も大きいデータ処理戦略を策定する必要がありません。
* 不可能な場合、データ プラットフォームとパフォーマンスへの影響を抑えるためにデータの正規化を試みる。 たとえば、明示的なユーザー ID を記録するのではなく、ユーザー名と詳細が内部 ID に関連付けられる参照データを作成し、別の場所から記録できるようにします。 そうすることで、あるユーザーから個人情報の削除を依頼された場合に、そのユーザーに対応するルックアップ テーブルの行を削除するだけで済ませることができます。 
* 最後に、プライベート データを収集する必要がある場合は、消去 API パスと既存のクエリ API パスに関するプロセスを構築する。これにより、ユーザーに関連付けられたプライベート データのエクスポートと削除に関して発生し得る義務を満たします。 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>プライベート データは Log Analytics のどこで見つかるか

Log Analytics は柔軟なストアであり、データのスキーマを指定しながら、カスタム値で各フィールドを上書きできます。 さらに、カスタム スキーマが取り込まれる場合があります。 そのため、特定のワークスペースのどこにプライベート データがあるかを正確に断言することはできません。 しかし、インベントリ内の以下の場所から始めるのが適切です。

### <a name="log-data"></a>ログ データ

* *IP アドレス*:Log Analytics では、多数の異なるテーブルからさまざまな IP 情報が収集されます。 たとえば次のクエリは、過去 24 時間で IPv4 アドレスが収集されたすべてのテーブルを示します。
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *ユーザー ID*:ユーザー ID は、さまざまなソリューションとテーブルで見つかります。 検索コマンドを使用して、データセット全体で特定のユーザー名を検索することができます。
    ```
    search "[username goes here]"
    ```
  人間が判読できるユーザー名だけでなく、特定のユーザーまで直接追跡できる GUID も忘れずに検索してください。
* *デバイス ID*:ユーザー ID と同様、デバイス ID も "プライベート" と見なされる場合があります。 上に記載されているユーザー ID の場合と同じ方法を使用して、これが問題となるかもしれないテーブルを特定します。 
* *カスタム データ*:Log Analytics では、カスタム ログとカスタム フィールド、[HTTP データ コレクター API](../../azure-monitor/platform/data-collector-api.md)、システムのイベント ログの一部として収集されるカスタム データなど、さまざまな方法による収集が可能です。 これらはすべてプライベート データを含んでいる可能性があり、そのようなデータが存在するかどうかを確認するために調べる必要があります。
* *ソリューションによって収集されたデータ*: ソリューションのメカニズムは変更可能です。そのため、コンプライアンスを確保するために、ソリューションによって生成されたすべてのテーブルを確認することをお勧めします。

### <a name="application-data"></a>アプリケーション データ

* *IP アドレス*:Application Insights は既定で、すべての IP アドレス フィールドを "0.0.0.0" に難読化しますが、セッション情報を保持しておくために、この値を実際のユーザー IP で上書きするのがかなり一般的なパターンです。 Analytics の次のクエリを使用すると、過去 24 時間にわたって、IP アドレス以外の列に "0.0.0.0" 以外の値が含まれるすべてのテーブルを検索できます。
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *ユーザー ID*:既定では、Application Insights は、ユーザーとセッションの追跡のために、ランダムに生成された ID を使用します。 しかし、これらのフィールドは、アプリケーションにとってより関連性の高い ID を格納するために上書きされるのが一般的です。 たとえば、ユーザー名や AAD GUID などです。これらの ID は多くの場合、個人データの範囲にあると見なされます。そのため、適切に処理する必要があります。 これらの ID については、常に難読化または匿名化を試みることをお勧めします。 これらの値がよく見つかるフィールドには、session_Id、user_Id、user_AuthenticatedId、user_AccountId、customDimensions などがあります。
* *カスタム データ*:Application Insights では、どのデータ型にもカスタム ディメンションのセットを追加できます。 これらのディメンションは、*どのような*データでもありえます。 過去 24 時間にわたって収集されたすべてのカスタム ディメンションを識別するには、次のクエリを使用します。
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *メモリ内および転送中のデータ*:Application Insights では、例外、要求、依存関係の呼び出し、およびトレースが追跡されます。 プライベート データは、多くの場合、コードや HTTP 呼び出しのレベルで収集できます。 例外、要求、依存関係、およびトレース テーブルを確認して、このようなデータをすべて識別します。 可能な場所では[テレメトリ初期化子](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling)を使用してこのデータを難読化します。
* *スナップショット デバッガーのキャプチャ*:Application Insights の[スナップショット デバッガー](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)機能では、アプリケーションの実稼働インスタンスで例外がキャッチされるたびにデバッグのスナップショットを収集できます。 スナップショットでは、例外と、スタックに含まれるすべてのステップのローカル変数の値にたどり着く完全なスタック トレースが公開されます。 残念ながら、この機能では、スナップ ポイントを選択的に削除したり、スナップショット内のデータにプログラムからアクセスしたりすることができません。 そのため、既定のスナップショット保有期間のペースではお客様のコンプライアンス要件が満たされない場合は、この機能を無効にすることをお勧めします。

## <a name="how-to-export-and-delete-private-data"></a>プライベート データをエクスポートして削除する方法

「[個人データの処理に関する戦略](#strategy-for-personal-data-handling)」セクションで先ほど述べたとおり、可能な場合はデータ収集ポリシーを再構築することが __強く__ 推奨されます。プライベート データの収集を無効にするか、難読化または匿名化を行ってください。そうでなければ、"プライベート" と見なされるデータがなくなるよう修正してください。 データの処理を行う場合、戦略を定義して自動化するコスト、顧客が問題なくデータを操作できるインターフェイスを作成するコスト、継続的なメンテナンス コストがまず、お客様とそのチームにかかります。 そのうえ、Log Analytics と Application Insights で多額の計算コストがかかります。また、クエリ API または消去 API の同時呼び出しが大量に発生して、Log Analytics 機能に対するすべての他の操作に悪影響が及ぶ可能性があります。 とは言え、プライベート データを収集する必要があるシナリオが有効な場合が確かにあります。 このような場合、このセクションで説明されているとおりデータを処理する必要があります。

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>表示とエクスポート

データ要求の表示とエクスポートの両方に、[Log Analytics クエリ API](https://dev.loganalytics.io/) または [Application Insights クエリ API](https://dev.applicationinsights.io/quickstart) を使用する必要があります。 必要な場合、ユーザーに提供するためにデータの形式を適切なものに変換するロジックを実装できます。 そのようなロジックをホストするには、[Azure Functions](https://azure.microsoft.com/services/functions/) が適しています。

> [!IMPORTANT]
>  消去操作の大部分は SLA よりもずっと短期間に完了する場合がありますが、使用されるデータ プラットフォームへの影響が大きいため、**消去操作の完了の正式な SLA は 30 日に設定されています**。 これは自動化されたプロセスです。操作処理の高速化を要求する方法はありません。

### <a name="delete"></a>削除

> [!WARNING]
> Log Analytics の削除は破壊的であり、元に戻せません。 実行には細心の注意を払ってください。

プライバシー処理の一環として、Microsoft は "*消去*" API パスを提供しています。 実行に関するリスク、パフォーマンスへの潜在的な影響、Log Analytics データの総集計や測定などにおけるスキューの発生の可能性があり、このパスは慎重に使用する必要があります。 プライベート データを処理する別のアプローチについては、「[個人データの処理に関する戦略](#strategy-for-personal-data-handling)」セクションを参照してください。

消去は高度な特権が必要な操作であり、Azure Resource Manager でロールが明示的に付与されない限り、Azure のアプリまたはユーザーは (リソース所有者でさえも) 実行のアクセス許可を得られません。 このロールは "_データ消去者_" であり、データ損失の可能性があるため慎重に委任する必要があります。 

Azure Resource Manager ロールが割り当てられると、2 つの新しい API パスが利用できるようになります。 

#### <a name="log-data"></a>ログ データ

* [POST purge](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - 削除するデータのパラメーターを指定するオブジェクトを受け取り、参照 GUID を返します 
* GET purge status - POST purge 呼び出しでは、実行した消去 API の状態を確認するために呼び出せる URL が含まれる、'x-ms-status-location' ヘッダーが返されます。 例: 

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  ほとんどの消去操作は Microsoft の SLA よりもはるかに早く完了すると考えられます。しかし、Log Analytics によって使用されるデータ プラットフォームへの重大な影響があるため、**消去操作の完了の正式な SLA は 30 日に設定されています**。 

#### <a name="application-data"></a>アプリケーション データ

* [POST purge](https://docs.microsoft.com/rest/api/application-insights/components/purge) - 削除するデータのパラメーターを指定するオブジェクトを受け取り、参照 GUID を返します
* GET purge status - POST purge 呼び出しでは、実行した消去 API の状態を確認するために呼び出せる URL が含まれる、'x-ms-status-location' ヘッダーが返されます。 例: 

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  消去操作の大部分は、Application Insights で使用されるデータ プラットフォームへの影響が大きいため、SLA よりもずっと短期間に完了する場合があります。**消去操作の完了についての SLA は、30 日に設定されています**。

## <a name="next-steps"></a>次の手順
- Log Analytics のデータの収集方法、処理方法、保護方法については、「[Log Analytics データのセキュリティ](../../azure-monitor/platform/data-security.md)」をご覧ください。
- Application Insights のデータが収集、処理、セキュリティ保護される方法について詳しくは、[Application Insights データのセキュリティ](../../azure-monitor/app/data-retention-privacy.md)に関するページをご覧ください。
