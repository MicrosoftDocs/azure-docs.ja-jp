---
title: Azure Traffic Manager エンドポイントの監視 | Microsoft Docs
description: この記事では、Azure ユーザーが高可用性アプリケーションをデプロイできるように、Traffic Manager でエンドポイントの監視と自動フェールオーバーの機能がどのように使用されているかを説明します。
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2021
ms.author: duau
ms.openlocfilehash: 455eeb83ef5a9608c077de24b8d3d4722d26a822
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742711"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager エンドポイントの監視

Azure Traffic Manager には、エンドポイントの監視と自動フェールオーバーの機能が組み込まれています。 この機能を使用して、Azure リージョンの障害を含むエンドポイント障害に対する回復性を備えた高可用性アプリケーションを提供できます。

## <a name="configure-endpoint-monitoring"></a>エンドポイント監視の構成

エンドポイント監視を構成するには、Traffic Manager プロファイルで次の設定を指定する必要があります。

* **プロトコル**。 Traffic Manager がエンドポイントをプローブして正常性をチェックするときに使用するプロトコルとして、HTTP、HTTPS、または TCP を選択します。 HTTPS 監視では、TLS/SSL 証明書が有効であるかどうかは検証されず、その証明書が存在することだけが確認されます。
* **Port**。 要求に使用するポートを選択します。
* **Path**。 この構成設定は、パス設定を指定する必要がある HTTP プロトコルと HTTPS プロコルでのみ有効です。 TCP 監視プロトコルにこの設定を指定するとエラーになります。 HTTP および HTTPS プロトコルの場合は、監視でアクセスされる Web ページまたはファイルの相対パスと名前を指定します。 スラッシュ (/) は、相対パスの有効なエントリであり、 ファイルがルート ディレクトリ (既定値) にあることを示します。
* **カスタム ヘッダーの設定**。 この構成設定は、Traffic Manager がプロファイルのエンドポイントに送信する正常性チェックに特定の HTTP ヘッダーを追加するうえで役に立ちます。 カスタム ヘッダーは、プロファイル レベルおよびエンドポイント レベルで指定できます。プロファイル レベルで指定すると、そのプロファイルのすべてのエンドポイントに適用され、エンドポイント レベルで指定すると、エンドポイントにのみ適用されます。 カスタム ヘッダーは、マルチテナント環境でのエンドポイントの正常性チェックに使用できます。 そうすることにより、ホスト ヘッダーを指定して、宛先に正しくルーティングできます。 Traffic Manager からの HTTP (S) 要求を特定し、それを別の方法で処理するために使用できる一意のヘッダーを追加することで、この設定を使用することもできます。 コンマで区切られたヘッダーと値のペアを最大 8 つ指定できます。 たとえば、"header1:value1, header2:value2" です。 
* **予測される状態コードの範囲**。 この設定を使用すると、200-299, 301-301 という形式で複数の成功コードの範囲を指定できます。 正常性チェックが完了したときに、これらの状態コードをエンドポイントからの応答として受信した場合、そのエンドポイントは Traffic Manager によって正常としてマークされます。 指定できる状態コードの範囲は 8 個までです。 この設定は、HTTP と HTTPS プロトコルのみ、およびすべてのエンドポイントに適用できます。 この設定は Traffic Manager プロファイル レベルであり、既定では成功の状態コードとして 200 という値が定義されています。
* **プローブ間隔**。 この値により、Traffic Manager プローブ エージェントによってエンドポイントの正常性がチェックされる頻度を指定します。 ここで、30 秒 (普通のプローブ) と 10 秒 (速いプローブ) という 2 つの値を指定できます。 値が指定されていない場合、プロファイルによって既定値の 30 秒に設定されます。 高速プローブの料金の詳細については、「[Traffic Manager の価格](https://azure.microsoft.com/pricing/details/traffic-manager)」をご覧ください。
* **障害の許容数**。 この値により、Traffic Manager プローブ エージェントがそのエンドポイントを異常としてマークするまでに許容されるエラーの数を指定します。 0 ～ 9 の値を指定できます。 値が 0 の場合、監視エラーが 1 つ発生すると、そのエンドポイントが異常とマークされます。 値が指定されていない場合は、既定値の 3 が使用されます。
* **プローブのタイムアウト**。 このプロパティでは、Traffic Manager プローブ エージェントの待機時間を指定します。これを過ぎると、エンドポイントに対する正常性プローブ チェックが失敗と見なされます。 プローブ間隔が 30 秒に設定されている場合は、5 ～ 10 秒のタイムアウト値を設定できます。 値が指定されていない場合は、既定値の 10 秒が使用されます。 プローブ間隔が 10 秒に設定されている場合は、5 ～ 9 秒のタイムアウト値を設定できます。 タイムアウト値が指定されていない場合は、既定値の 9 秒が使用されます。

    ![Traffic Manager エンドポイントの監視](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **図:Traffic Manager エンドポイントの監視**

## <a name="how-endpoint-monitoring-works"></a>エンドポイント監視のしくみ

監視プロトコルが HTTP または HTTPS に設定されている場合、Traffic Manager プローブ エージェントは、指定されたプロトコル、ポート、相対パスを使用してエンドポイントに対して GET 要求を行います。 プローブ エージェントが 200-OK 応答、または **予測される状態コードの \*範囲** で構成された応答を受信した場合、そのエンドポイントは正常と見なされます。 応答が別の値である場合や、タイムアウト期間内に応答を受信しなかった場合、Traffic Manager プローブ エージェントは [障害の許容数] 設定に従って再試行します。 この設定が 0 である場合、再試行は行われません。 エンドポイントは、連続エラーの数が [障害の許容数] 設定を上回った場合に異常とマークされます。

監視プロトコルが TCP である場合、Traffic Manager プローブ エージェントは、指定されたポートを使用して TCP 接続要求を作成します。 接続を確立するために、エンドポイントが要求に応答すると、その正常性チェックは成功とマークされます。 Traffic Manager プローブ エージェントは TCP 接続をリセットします。 応答が別の値である場合や、タイムアウト期間内に応答を受信しなかった場合、Traffic Manager プローブ エージェントは、[障害の許容数] 設定に従って再試行します。 この設定が 0 である場合、再試行は行われません。 連続エラーの数が [障害の許容数] 設定を上回った場合、そのエンドポイントは異常とマークされます。

どのような場合でも、Traffic Manager は複数の場所からプローブします。 連続エラーによって、各リージョン内で何が発生しているかが判断されます。 そのため、エンドポイントは、[プローブ間隔] に使用されている設定よりも頻繁に Traffic Manager から正常性プローブを受信します。

>[!NOTE]
>HTTP または HTTPS 監視プロトコルの場合、エンドポイント側でアプリケーションにカスタム ページ (例: /health.aspx) を実装するのが一般的です。 このパスを監視に使用すると、パフォーマンス カウンターのチェックやデータベースの可用性チェックなどのアプリケーション固有のチェックを実行できます。 これらのカスタム チェックを基にして、ページで適切な HTTP ステータス コードが返されます。

Traffic Manager プロファイルのすべてのエンドポイントは、監視設定を共有します。 複数のエンドポイントに対して異なる監視設定を使用する場合は、 [入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings)を作成できます。

## <a name="endpoint-and-profile-status"></a>エンドポイントとプロファイルの状態

Traffic Manager のプロファイルとエンドポイントは、ユーザーが有効と無効を切り替えることができます。 ただし、Traffic Manager の設定およびプロセスの自動化により、エンドポイントの状態が変化する場合もあります。

### <a name="endpoint-status"></a>エンドポイントの状態

特定のエンドポイントを有効または無効にすることができます。 正常な状態になっている基になるサービスに影響はありません。 エンドポイントの状態を変更することで、Traffic Manager プロファイルでのエンドポイントの可用性を制御します。 エンドポイントの状態を無効にすると、Traffic Manager でその正常性はチェックされず、DNS 応答にそのエンドポイントは含まれなくなります。

### <a name="profile-status"></a>プロファイルの状態

プロファイルの状態の設定を使用して、特定のプロファイルを有効または無効にすることができます。 エンドポイントの状態が 1 つのエンドポイントに影響を与えるのに対して、プロファイルの状態はすべてのエンドポイントを含むプロファイル全体に作用します。 プロファイルを無効にすると、エンドポイントの正常性はチェックされず、DNS 応答にエンドポイントが含まれなくなります。 DNS クエリに対して、[NXDOMAIN](https://tools.ietf.org/html/rfc2308) 応答コードが返されます。

### <a name="endpoint-monitor-status"></a>エンドポイント監視の状態

エンドポイント監視の状態は Traffic Manager で生成される値で、エンドポイントの状態を示します。 手動でこの設定を変更することはできません。 エンドポイントの監視の状態は、エンドポイントの監視の結果と構成されているエンドポイントの状態の組み合わせです。 エンドポイント監視の状態がとりうる値を、次の表に示します。

| プロファイルの状態 | エンドポイントの状態 | エンドポイント監視の状態 | Notes |
| --- | --- | --- | --- |
| 無効 |Enabled |非アクティブ |プロファイルは無効にされています。 エンドポイントの状態を有効にすることはできますが、プロファイルの状態 (無効) が優先されます。 無効なプロファイルに含まれているエンドポイントは監視されません。 DNS クエリに対して、NXDOMAIN 応答コードが返されます。 |
| &lt;任意&gt; |無効 |無効 |エンドポイントが無効になっています。 無効なエンドポイントは監視されません。 このエンドポイントは DNS 応答に含まれないため、トラフィックを受信しません。 |
| Enabled |Enabled |オンライン |エンドポイントは監視されており、正常です。 これは DNS 応答に含まれるため、トラフィックを受信できます。 |
| Enabled |Enabled |低下しています |エンドポイント監視の正常性チェックが失敗しています。 このエンドポイントは DNS 応答に含まれないため、トラフィックを受信しません。 <br>すべてのエンドポイントが機能低下している場合は例外です。 この場合、それらのすべてがクエリの応答で返されたと見なされます。</br>|
| Enabled |Enabled |エンドポイン トチェック中 |エンドポイントは監視されていますが、最初のプローブの結果はまだ受信されていません。 CheckingEndpoint は、プロファイル内のエンドポイントの追加または有効化の直後に通常発生する一時的な状態です。 この状態のエンドポイントは DNS 応答に含まれるため、トラフィックを受信できます。 |
| Enabled |Enabled |停止済み |このエンドポイントが指している Web アプリが実行されていません。 Web アプリの設定を確認してください。 この状態は、エンドポイントが入れ子になったエンドポイントであり、子プロファイルが無効になっているか非アクティブである場合にも発生する可能性があります。 <br>停止状態のエンドポイントは監視されません。 これは DNS 応答に含まれないため、トラフィックを受信しません。 すべてのエンドポイントが機能低下している場合は例外です。 この場合、それらのすべてがクエリの応答で返されたと見なされます。</br>|

入れ子になったエンドポイントのエンドポイント監視の状態を計算する方法の詳細については、「[入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md)」を参照してください。

>[!NOTE]
> エンドポイント モニターの停止状態は、Web アプリケーションが Standard レベル以上で実行されていない場合に App Service で発生することがあります。 詳細については、[Traffic Manager と App Service の統合](../app-service/web-sites-traffic-manager.md)に関するページを参照してください。

### <a name="profile-monitor-status"></a>プロファイル モニターの状態

プロファイル モニターの状態は、構成済みのプロファイル状態とすべてのエンドポイントのエンドポイント監視状態の値を組み合わせたものです。 次の表に、このオプションで使用可能な値を示します。

| (構成された)プロファイルの状態 | エンドポイント監視の状態 | プロファイル モニターの状態 | Notes |
| --- | --- | --- | --- |
| 無効 |&lt;いずれも&gt; または、プロファイルでエンドポイントが定義されていません。 |無効 |プロファイルは無効にされています。 |
| Enabled |少なくとも 1 つのエンドポイントの状態が低下です。 |低下しています |個々のエンドポイントの状態の値を確認し、さらなる注意が必要なエンドポイントを特定します。 |
| Enabled |少なくとも 1 つのエンドポイントの状態がオンラインです。 低下の状態になっているエンドポイントがありません。 |オンライン |サービスをトラフィックを受信しています。 ユーザーによる対処は不要です。 |
| Enabled |少なくとも 1 つのエンドポイントの状態が、エンドポイント チェック中です。 オンラインまたは低下のエンドポイントはありません。 |エンドポイント チェック中 |このトラフィックの状態は、プロファイルが作成されるか有効になったときに発生します。 エンドポイントの正常性が初めてチェックされます。 |
| Enabled |プロファイルで定義されているすべてのエンドポイントの状態が無効または停止のいずれかであるか、プロファイルにエンドポイントが定義されていません。 |非アクティブ |アクティブなエンドポイントはありませんが、プロファイルは引き続き有効です。 |

## <a name="endpoint-failover-and-recovery"></a>エンドポイントのフェールオーバーと復旧

Traffic Manager は、問題のあるエンドポイントを含むすべてのエンドポイントの正常性を定期的にチェックします。 Traffic Manager は、エンドポイントが正常な状態になり、ローテーションに帰ったときに検出します。

次のいずれかのイベントが発生した場合、エンドポイントは異常です。

- 監視プロトコルが HTTP または HTTPS の場合:
    - 200 以外の応答、または **予測される状態コードの範囲** 設定で指定された状態範囲を含まない応答を受信した (別の 2xx コードや、301 または302 リダイレクトを含む)。
- 監視プロトコルが TCP の場合: 
    - 接続を確立するために Traffic Manager が送信した SYN 要求に対して、ACK または SYN-ACK 以外の応答を受信した。
- タイムアウトになった。 
- その他の接続の問題によってエンドポイントに到達できない。

失敗したチェックのトラブルシューティングについての詳細については、「 [Azure Traffic Managerでの機能低下状態のトラブルシューティング](traffic-manager-troubleshooting-degraded.md)」を参照してください。 

次の図のタイムラインは、以下のように設定されている Traffic Manager エンドポイントの監視プロセスの詳細な説明です。

* 監視プロトコルは HTTP です。
* プローブ間隔は 30 秒です。
* 許容されるエラーの数は 3 件です。
* タイムアウトの値は 10 秒です。
* DNS TTL は 30 秒です。

![Traffic Manager endpoint failover and failback sequence](./media/traffic-manager-monitoring/timeline.png)

**図:Traffic Manager エンドポイントのフェールオーバーと回復のシーケンス**

1. **GET**。 エンドポイントごとに、Traffic Manager の監視システムは、監視設定で指定されたパスで GET 要求を行います。
2. **200 OK または Traffic Manager プロファイル監視設定で指定されたカスタム コード範囲**。 監視システムでは、HTTP 200 OK または監視設定で指定された範囲内の状態コードが 10 秒以内に返されることを想定しています。 この応答を受信すると、サービスが使用可能であると認識されます。
3. **30 秒間隔のチェック**。 このエンドポイント正常性チェックは 30 秒ごとに繰り返されます。
4. **サービス利用不可**。 サービスが使用できなくなります。 次の正常性チェックまで Traffic Manager では認識されません。
5. **監視パスへのアクセス試行**。 監視システムで GET 要求が行われましたが、10 秒のタイムアウト期間内に応答を受信していません。 次に、監視システムは、30 秒間隔で、さらに 3 回試行します。 3 回の試行のいずれかが成功した場合、試行の回数はリセットされます。
6. **機能低下として指定**。 4 回連続して失敗した後に、監視システムは、利用できないエンドポイントの状態を低下として指定します。
7. **他のエンドポイントへのトラフィックの転送**。 Traffic Manager DNS ネーム サーバーが更新され、Traffic Manager による DNS クエリへの応答でこのエンドポイントが返されなくなります。 そのため、新しい接続は他の使用可能なエンドポイントに転送されます。 ただし、以前の DNS 応答にこのエンドポイントが含まれていて、その応答が再帰 DNS サーバーや DNS クライアントにキャッシュされている場合があります。 クライアントは、DNS キャッシュの有効期限が切れるまでこのエンドポイントの使用を継続します。 DNS キャッシュが期限切れになり、クライアントが新たに DNS クエリを実行すると、他のエンドポイントに転送されます。 キャッシュ期間は、Traffic Manager プロファイルの TTL 設定によって制御され、30 秒などに設定されています。
8. **正常性チェックの続行**。 Traffic Manager は、低下状態にある間も、エンドポイントの正常性を継続的にチェックします。 Traffic Manager は、稼働状態に戻ったときにエンドポイントを検出します。
9. **サービスがオンラインに復帰**。 サービスが使用できるようになります。 監視システムが次の正常性チェックを行うまで、Traffic Manager ではそのエンドポイントは低下状態のままです。
10. **サービスへのトラフィックの再開**。 Traffic Manager は、GET 要求を送信し、200 OK ステータス応答を受け取ります。 サービスが正常な状態に戻りました。 Traffic Manager のネーム サーバーがもう一度更新され、DNS 応答でサービスの DNS 名の配信が開始されます。 他のエンドポイントに返すキャッシュ済みの DNS 応答が期限切れになり、他のエンドポイントとの既存の接続が終了すると、トラフィックは元のエンドポイントに戻ります。

    > [!NOTE]
    > Traffic Manager は DNS レベルで動作するため、いずれかのエンドポイントとの既存の接続に影響を与えることはありません。 (プロファイル設定を変更するか、フェールオーバーまたはフェールバックが発生して) エンドポイント間でトラフィックが送信される際、新しい接続は Traffic Manager によって使用可能なエンドポイントに転送されます。 ただし、他のエンドポイントは、セッションが終了するまで既存の接続を介してトラフィックを受信し続ける可能性があります。 トラフィックが既存の接続に転送されないようにするには、各エンドポイントで使用されるセッション期間をアプリケーションで制限する必要があります。

## <a name="traffic-routing-methods"></a>トラフィックのルーティング方法

エンドポイントが低下状態になっているときは、DNS クエリに対する応答で返されなくなります。 代わりに別のエンドポイントが選択されて返されます。 プロファイルで構成されているトラフィック ルーティング方法によって、代替のエンドポイントの選択方法が決定されます。

* **優先順位**。 エンドポイントの優先度リストが使用されます。 常に、リスト内で最初に使用可能なエンドポイントが返されます。 エンドポイントが低下状態になると、次に使用可能なエンドポイントが返されます。
* **重み付け** 使用可能なエンドポイントのいずれもが返される可能性があります。 選択は、割り当てられた重みと、他の使用可能なエンドポイントの重みに基づき、ランダムに行われます。
* **パフォーマンス**。 エンド ユーザーに最も近いエンドポイントが返されます。 そのエンドポイントが使用できない場合、Traffic Manager は、次の最も近い Azure リージョン内のエンドポイントにトラフィックを移動します。 パフォーマンス トラフィック ルーティング方法では、[入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region)を使用してフェールオーバー計画を構成することもできます。
* **地理的**。 クエリ要求の IP に基づいて地理的な場所を提供するためにマップされたエンドポイントが返されます。 地理的な場所はプロファイル内の 1 つのエンドポイントにしかマップできないため、そのエンドポイントが使用できない場合に、フェールオーバー先として別のエンドポイントは選択されません (詳細については、[FAQ](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method) を参照してください)。 ベスト プラクティスとして、地理的なルーティングを使用する場合は、プロファイルのエンドポイントとして複数のエンドポイントが含まれた入れ子になった Traffic Manager プロファイルを使用することをお勧めします。
* **複数値**。IPv4/IPv6 アドレスにマップされている複数のエンドポイントが返されます。 このプロファイルに対するクエリが受信されると、正常なエンドポイントが、指定した **[応答の最大レコード数]** の値に基づいて返されます。 応答の既定の数は 2 つのエンドポイントです。
* **サブネット**。一連の IP アドレス範囲にマップされたエンドポイントが返されます。 その IP アドレスから要求を受信した場合、返されるエンドポイントは、その IP アドレスにマップされているものです。 

詳細については、「 [Traffic Manager のトラフィック ルーティング方法](traffic-manager-routing-methods.md)」を参照してください。

> [!NOTE]
> トラフィック ルーティングの通常の動作の 1 つの例外は、すべての対象となるエンドポイントが低下状態になった場合に発生します。 この場合、Traffic Manager は、"ベスト エフォート" として、*低下状態のすべてのエンドポイントが実際にはオンラインであるかのように応答* します。 この動作は、DNS 応答でどのエンドポイントも返されないよりは望ましい手段です。 無効または停止状態のエンドポイントは監視されないため、トラフィックの対象と見なされません。
>
> このような状況は、一般的に次のようなサービスの誤った構成が原因になっています。
>
> * アクセス制御リスト (ACL) が Traffic Manager の正常性チェックをブロックしている。
> * Traffic Manager プロファイルで監視ポートまたは監視プロトコルが正しく構成されていない。
>
> どのエンドポイントも返されない場合、Traffic Manager の正常性チェックが正しく構成されていなくても、トラフィック ルーティングの観点では Traffic Manager が正常に動作 *している* ように見える場合があります。 ただし、この場合、エンドポイントのフェールオーバーが行われず、アプリケーション全体の可用性に影響を与えます。 プロファイルに低下状態ではなくオンライン状態を表示することが重要になります。 オンライン状態は、Traffic Manager の正常性チェックが正常に動作していることを示しています。

失敗した正常性チェックのトラブルシューティングについての詳細については、「 [Azure Traffic Managerでの機能低下状態のトラブルシューティング](traffic-manager-troubleshooting-degraded.md)」を参照してください。

## <a name="faqs"></a>FAQ

* [Traffic Manager には、Azure リージョンの障害に対する回復性がありますか。](./traffic-manager-faqs.md#is-traffic-manager-resilient-to-azure-region-failures)

* [リソース グループの場所の選択は Traffic Manager にどのような影響を与えますか。](./traffic-manager-faqs.md#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [各エンドポイントの現在の正常性を確認するには、どうすればよいですか。](./traffic-manager-faqs.md#how-do-i-determine-the-current-health-of-each-endpoint)

* [HTTPS エンドポイントを監視できますか。](./traffic-manager-faqs.md#can-i-monitor-https-endpoints)

* [エンドポイントを追加する際には、IP アドレスと DNS 名のどちらを使用しますか。](./traffic-manager-faqs.md#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [エンドポイントを追加するときに、どの種類の IP アドレスを使用できますか。](./traffic-manager-faqs.md#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [1 つのプロファイル内で異なる種類のエンドポイント アドレス指定方法を使用することはできますか。](./traffic-manager-faqs.md#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [受信クエリのレコード タイプが、エンドポイントのアドレス指定方法に関連付けられているレコード タイプと異なる場合はどうなりますか。](./traffic-manager-faqs.md#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [入れ子になったプロファイル内で、IPv4/IPv6 アドレスでエンドポイントが指定されているプロファイルを使用することはできますか。](./traffic-manager-faqs.md#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Web アプリケーションのエンドポイントを Traffic Manager プロファイルで停止しましたが、それを再起動した後でもトラフィックを受信していません。どうしたらいいですか。](./traffic-manager-faqs.md#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [アプリケーションが HTTP または HTTPS をサポートしていなくても Traffic Manager を使用できますか。](./traffic-manager-faqs.md#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [TCP 監視を使用する場合、エンドポイントからどのような応答が必要ですか。](./traffic-manager-faqs.md#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Traffic Manager は、どの程度迅速に異常なエンドポイントからユーザーを移動させますか。](./traffic-manager-faqs.md#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [プロファイル内のエンドポイントごとに異なる監視設定を指定するにはどうすればよいですか。](./traffic-manager-faqs.md#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [エンドポイントに対する Traffic Manager の正常性チェックに HTTP ヘッダーを割り当てるにはどうすればよいですか。](./traffic-manager-faqs.md#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [エンドポイントの正常性チェックには、どのようなホストヘッダーが使用されますか。](./traffic-manager-faqs.md#what-host-header-do-endpoint-health-checks-use)

* [正常性チェックはどの IP アドレスから発信されますか。](./traffic-manager-faqs.md#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Traffic Manager では、エンドポイントに対して正常性チェックが何回実行されるのですか。](./traffic-manager-faqs.md#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [いずれかのエンドポイントがダウンした場合に通知を受ける方法を教えてください。](./traffic-manager-faqs.md#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>次のステップ

[Traffic Manager のしくみ](traffic-manager-how-it-works.md)

Traffic Manager でサポートされている [トラフィック ルーティング方法](traffic-manager-routing-methods.md) の詳細を確認する。

[Traffic Manager プロファイルの作成](traffic-manager-manage-profiles.md)

[低下状態に関するトラブルシューティング](traffic-manager-troubleshooting-degraded.md) を行う。