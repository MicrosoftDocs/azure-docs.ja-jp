---
title: Azure Application Insights 可用性テストのトラブルシューティング | Microsoft Docs
description: Azure Application Insights の Web テストのトラブルシューティングを行います。 Web サイトが使用できなくなったり、応答速度が低下したりした場合に、アラートを受け取ります。
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 87bc87d7d105d581f0143e87044fb0337c0fd7f6
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304748"
---
# <a name="troubleshooting"></a>トラブルシューティング

この記事は、可用性の監視機能を使用しているときに遭遇する可能性のある一般的な問題のトラブルシューティングに役立ちます。

## <a name="ssltls-errors"></a>SSL/TLS エラー

|症状/エラー メッセージ| 考えられる原因|
|--------|------|
|SSL/TLS のセキュリティで保護されているチャネルを作成できませんでした  | SSL のバージョン。 サポートされるのは、TLS 1.0、1.1、1.2 のみです。 **SSLv3 はサポートされません。**
|TLSv1.2 Record レイヤー: アラート (レベル: 致命的、説明: Record MAC が無効です)| [詳細](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake)については、StackExchange スレッドを参照してください。
|エラーの発生する URL が CDN (Content Delivery Network) の URL | CDN の不適切な構成が原因である可能性があります。 |  

### <a name="possible-workaround"></a>考えられる回避策

* 問題の発生する URL が常に依存リソースの URL である場合、Web テストの **[従属要求の解析]** は無効にすることをお勧めします。

## <a name="test-fails-only-from-certain-locations"></a>特定の場所からのみテストが失敗する

|症状/エラー メッセージ| 考えられる原因|
|----|---------|
|接続済みの呼び出し先が一定の時間を過ぎても正しく応答しなかったため、接続できませんでした  | 特定の場所のテスト エージェントがファイアウォールによってブロックされています。|
|    |Load Balancer、Geo Traffic Manager、Azure Express Route を介して特定の IP アドレスの再ルーティングが生じています。 
|    |Azure ExpressRoute を使用している場合、[非対称ルーティングが発生](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing)してパケットがドロップしている状況が考えられます。|

## <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>プロトコル違反エラーでテストが断続的に失敗します

|症状/エラー メッセージ| 考えられる原因|
|----|---------|
プロトコル違反 CR の後には LF を指定しなければなりません | これは、形式に誤りのあるヘッダーが検出されたときに発生します。 具体的には、行末の指定で CRLF を使用していないヘッダーがあります。これは HTTP 仕様に違反しているため、.NET WebRequest レベルで失敗します。
 || ロード バランサーまたは CDN が原因である可能性もあります。

> [!NOTE]
> HTTP ヘッダーの緩和された検証を行うブラウザーでは、URL は 失敗しない場合があります。 この問題の詳細については、こちらのブログ記事を参照してください: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>一般的なトラブルシューティングの質問

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>サイトには問題がないように見えますがテストが失敗します。 Application Insights からアラートが通知されるのはどうしてですか。

   * ご利用のテストでは **[依存する要求の解析]** が有効になっていますか。 それが有効になっていると、スクリプトやイメージなどのリソースに対して厳密なチェックが行われます。ブラウザー上で、この種のエラーはわかりにくい場合があります。 イメージ、スクリプト、スタイル シート、およびページによって読み込まれるその他のファイルすべてを確認してください。 これらのいずれかにエラーがある場合、メインの HTML ページの読み込みに問題がない場合でも、テストはエラーとしてレポートされます。 このようなリソース エラーをテストで検出しないようにするには、テスト構成で [依存する要求の解析] をオフにするだけです。

   * ネットワークの一時的な問題によるノイズの可能性を減らすには、[Enable retries for test failures]\(テストが失敗した場合に再試行を有効にする\) 構成がオンになっていることを確認します。 複数の場所からテストを行い、アラート ルールのしきい値を適宜管理して、過度のアラートの原因となっている場所固有の問題を防ぐこともできます。

   * 可用性エクスペリエンスで赤い点のいずれかをクリックするか、または Search エクスプローラーで任意の可用性エラーをクリックすると、エラーが報告された理由の詳細が表示されます。 テスト結果に加えて、サーバー側の相関関係を持つテレメトリ (有効になっている場合) は、テストが失敗した理由を理解するのに役立つはずです。 一時的な問題が発生する原因は、一般にネットワークまたは接続の問題にあります。

   * テストがタイムアウトになりましたか。 2 分後にテストを中止します。 ping または複数手順のテストが 2 分を経過しても完了しない場合は、エラーとして報告されます。 より短い時間で完了するようにテストを複数のテストに分割することを検討してください。

   * すべての場所でエラーが報告されましたか、それとも一部の場所でのみエラーが報告されましたか。 一部の場所でのみエラーが報告された場合は、ネットワーク/CDN の問題が原因です。 繰り返しになりますが、赤色のドットをクリックすると、その場所でエラーが報告された理由を理解するのに役立つ情報が表示されます。

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>アラートがトリガーされたとき、もしくは解決されたとき、あるいはその両方で、電子メールが届きませんでした。

クラシック アラート構成を調べて、目的の電子メールが直接リストされること、または表示している配布リストが通知を受信するように構成されていることを確認します。 そのようになっている場合は、配布リストの構成を調べて、外部の電子メールを受信できるように構成されていることを確認します。 また、メール管理者が所有するポリシーがこの問題を引き起こす可能性のある構成になっていないか確認します。

### <a name="i-did-not-receive-the-webhook-notification"></a>Webhook 通知が届きませんでした。

Web hook 通知を受信するアプリケーションが利用可能であること、さらにそのアプリケーションによって Web hook 要求が適切に処理されることを確認します。 詳細については、[こちら](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)をご覧ください。

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>プロトコル違反エラーでテストが断続的に失敗します。

エラー ("プロトコル違反..CR の後には LF を指定しなければなりません") は、サーバー (または依存関係) に問題があることを示しています。 これは、応答に形式が正しくないヘッダーが設定されている場合に発生します。 ロード バランサーまたは CDN が原因である可能性があります。 具体的には、行末の指定で CRLF を使用していないヘッダーがあります。これは HTTP 仕様に違反しているため、.NET WebRequest レベルで失敗します。 応答を検査して、違反の可能性のあるヘッダーを見つけます。

> [!NOTE]
> HTTP ヘッダーの緩和された検証を行うブラウザーでは、URL は 失敗しない場合があります。 この問題の詳細については、こちらのブログ記事を参照してください: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>テストの失敗を診断するためのサーバー側の関連するテレメトリが表示されません。*

サーバー側のアプリケーションに対して Application Insights を設定している場合は、[サンプリング](../../azure-monitor/app/sampling.md)操作中のためである可能性があります。 別の可用性の結果を選択します。

### <a name="can-i-call-code-from-my-web-test"></a>Web テストからコードを呼び出すことはできますか。

いいえ。 テストの手順は、.webtest ファイルに含まれている必要があります。 他の Web テストの呼び出しまたはループの使用は許可されていません。 ただし、役に立つさまざまなプラグインがあります。


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>"Web テスト" と "可用性テスト" に違いはありますか。

この 2 つの用語は、同じ意味で参照されることがあります。 可用性テストは、複数ステップから成る Web テストに加えて単一の URL Ping テストも含む、より一般的な用語です。

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>ファイアウォールの内側で稼働している内部サーバーに対して可用性テストを使用したいと考えています。

   解決策として、次の 2 つが考えられます。

   * [Web テスト エージェントの IP アドレス](../../azure-monitor/app/ip-addresses.md)からの受信要求を許可するようにファイアウォールを構成します。
   * 内部サーバーを定期的にテストする独自のコードを作成します。 このコードを、バック グラウンド プロセスとして、ファイアウォールの内側のテスト サーバーで実行します。 テスト プロセスは、コア SDK パッケージ内の [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API を使用して、その結果を Application Insights に送信できます。 この方法では、テスト サーバーに Application Insights のインジェスト エンドポイントへの送信アクセスが必要となりますが、受信要求を許可する代替方法と比べて、セキュリティのリスクは大幅に小さくなります。 結果は可用性 Web テストのブレードには表示されませんが、分析、検索、メトリックス エクスプローラーでの可用性の結果として表示されます。

### <a name="uploading-a-multi-step-web-test-fails"></a>複数ステップの Web テストのアップロードが失敗します。

これを引き起こす可能性があるいくつかの原因:
   * 300 K のサイズ制限があります。
   * ループはサポートされていません。
   * 他の Web テストへの参照はサポートされていません。
   * データ ソースはサポートされていません。

### <a name="my-multi-step-test-doesnt-complete"></a>複数ステップのテストが完了しません。

1 テストあたりの要求は 100 個に制限されています。 また、実行時間が 2 分を超えると、テストは停止します。

### <a name="how-can-i-run-a-test-with-client-certificates"></a>クライアント証明書でテストを実行するにはどうすればよいですか。

現在これはサポートされていません。

## <a name="who-receives-the-classic-alert-notifications"></a>(クラシック) アラート通知は誰が受け取りますか。

このセクションは、クラシック アラートにのみ適用され、目的の受信者だけが通知を受け取るように、アラート通知を最適化するために役立ちます。 [クラシック アラート](../platform/alerts-classic.overview.md)と新しいアラート エクスペリエンスの違いの詳細については、[アラートの概要の記事](../platform/alerts-overview.md)を参照してください。 新しいアラート エクスペリエンスのアラート通知を制御するには、[アクション グループ](../platform/action-groups.md)を使用します。

* クラシック アラート通知には、特定の受信者の使用をお勧めします。

* Y のうち X の場所からのエラーに関するアラートの場合、**一括/グループ** チェックボックス オプションが有効にされていれば、管理者/共同管理者ロールを持つユーザーに送信されます。  本質的に、_サブスクリプション_の_すべて_の管理者が通知を受け取ります。

* 可用性のメトリックに関するアラートの場合、**一括/グループ** チェックボックス オプションが有効にされていれば、サブスクリプション内の所有者、共同作成者、または閲覧者ロールを持つユーザーに送信されます。 実際には、サブスクリプションの Application Insights リソースにアクセスできる _すべて_ のユーザーが範囲内になり、通知を受け取ります。 

> [!NOTE]
> 現在、**一括/グループ** チェックボックス オプションを使用しており、それを無効にすると、変更を元に戻すことはできません。

ロールに基づいてユーザーに通知する必要がある場合は、新しいアラート エクスペリエンス/準リアルタイム アラートを使用します。 [アクション グループ](../platform/action-groups.md)を使用して、共同作成者/所有者/閲覧者のいずれかのロール (1 つのオプションとして組み合わされていない) を持つユーザーに対し、電子メール通知を構成することができます。

## <a name="next-steps"></a>次の手順

* [複数ステップ Web テスト](availability-multistep.md)
* [URL の ping テスト](monitor-web-app-availability.md)
