---
title: Data Management Gateway に関する問題をトラブルシューティングする
description: Data Management Gateway に関連する問題のトラブルシューティングを行うためのヒントを示します。
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 0b8aee32869202a8cc24a27dec85c93ffd294c6b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931596"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Data Management Gateway の使用に関する問題のトラブルシューティング
この記事では、Data Management Gateway の使用に関する問題のトラブルシューティングについて説明します。

> [!NOTE]
> この記事は、Azure Data Factory のバージョン 1 に適用されます。 Data Factory サービスの現在のバージョンを使用している場合は、[Data Factory におけるセルフホステッド IR ](../create-self-hosted-integration-runtime.md) に関するページを参照してください。

ゲートウェイの詳細については、「[Data Management Gateway](data-factory-data-management-gateway.md)」を参照してください。 ゲートウェイを使用してオンプレミスの SQL Server データベースから Microsoft Azure Blob Storage にデータを移動する方法のチュートリアルについては、[オンプレミスとクラウドの間のデータの移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事を参照してください。

## <a name="failed-to-install-or-register-gateway"></a>ゲートウェイをインストールまたは登録できない
### <a name="1-problem"></a>1.問題点
ゲートウェイをインストールして登録するときに (具体的には、ゲートウェイのインストール ファイルをダウンロードしているときに)、次のエラー メッセージが表示されます。

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>原因
ネットワーク上の問題により、ゲートウェイをインストールしようとしているコンピューターがダウンロード センターから最新のゲートウェイ インストール ファイルをダウンロードできませんでした。

#### <a name="resolution"></a>解決策
ファイアウォール プロキシ サーバーの設定を調べ、コンピューターから[ダウンロード センター](https://download.microsoft.com/)へのネットワーク接続をブロックしている設定があれば適切に更新します。

または、ダウンロード センターにアクセスできる別のコンピューターで、[ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)から最新のゲートウェイのインストール ファイルをダウンロードすることができます。 次に、インストーラー ファイルをゲートウェイ ホスト コンピューターにコピーし、手動で実行してゲートウェイをインストールおよび更新することができます。

### <a name="2-problem"></a>2.問題点
Azure Portal で **[このコンピューターに直接インストールします]** をクリックしてゲートウェイをインストールしようとすると、次のエラーが表示されます。

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>原因
ゲートウェイが既にコンピューターにインストールされています。

#### <a name="resolution"></a>解決策
コンピューター上の既存のゲートウェイをアンインストールしてから、 **[このコンピューターに直接インストールします]** リンクをもう一度クリックします。

### <a name="3-problem"></a>手順 3.問題点
新しいゲートウェイを登録するときに、次のエラーが表示される場合があります。

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>原因
このメッセージは、次のいずれかの理由で表示される場合があります。

* ゲートウェイ キーの形式が無効である。
* ゲートウェイ キーが無効になっている。
* ゲートウェイ キーがポータルで再生成された。  

#### <a name="resolution"></a>解決策
ポータルから取得した正しいゲートウェイ キーを使用しているかどうかを確認します。 必要に応じて、キーを再生成し、そのキーを使用してゲートウェイを登録します。

### <a name="4-problem"></a>4.問題点
ゲートウェイを登録するときに、次のエラー メッセージが表示される場合があります。

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![キーの内容または形式が無効](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>原因
入力したゲートウェイ キーの内容または形式が正しくありません。 その理由として、ポータルからコピーしたキーの一部が欠落しているか、無効なキーを使用している可能性があります。

#### <a name="resolution"></a>解決策
ポータルでゲートウェイ キーを生成した後、コピー ボタンを使用してキー全体をコピーします。 その後、キーをこのウィンドウに貼り付けてゲートウェイを登録します。

### <a name="5-problem"></a>5.問題点
ゲートウェイを登録するときに、次のエラー メッセージが表示される場合があります。

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![ゲートウェイ キーが無効または空](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>原因
Azure Portal でゲートウェイ キーが再生成されているか、ゲートウェイが削除されています。 Data Management Gateway セットアップが最新でない場合にも発生することがあります。

#### <a name="resolution"></a>解決策
Data Management Gateway セットアップのバージョンが最新かどうかを確認してください。最新バージョンは、Microsoft [ダウンロード センター](https://go.microsoft.com/fwlink/p/?LinkId=271260)から入手できます。

セットアップが最新で、ゲートウェイがまだポータルに存在している場合は、Azure Portal でゲートウェイ キーを再生成した後、コピー ボタンを使用してキー全体をコピーし、キーをこのウィンドウに貼り付けてゲートウェイを登録します。 それ以外の場合は、ゲートウェイを再作成して最初からやり直します。

### <a name="6-problem"></a>6.問題点
ゲートウェイを登録するときに、次のエラー メッセージが表示される場合があります。

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![ゲートウェイ キーが無効または空](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>原因
このエラーは、ゲートウェイが削除されているか、関連付けられているゲートウェイ キーが再生成されている場合に発生する可能性があります。

#### <a name="resolution"></a>解決策
ゲートウェイが削除されている場合は、ポータルでゲートウェイを再作成し、 **[登録]** をクリックします。ポータルからキーをコピーして貼り付け、ゲートウェイを登録します。

ゲートウェイが存在している一方でそのキーが再生成されている場合、新しいキーを使用してゲートウェイを登録します。 キーがない場合は、ポータルでもう一度キーを生成します。

### <a name="7-problem"></a>7.問題点
ゲートウェイを登録するときに、証明書のパスとパスワードの入力が必要になる場合があります。

![証明書の指定](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>原因
以前にこのゲートウェイが別のコンピューターを使用して登録されています。 ゲートウェイの初回登録時に、暗号化証明書がゲートウェイに関連付けられています。 証明書は、ゲートウェイ自体で生成することも、ユーザーが提供することもできます。  この証明書は、データ ストア (リンクされたサービス) の資格情報を暗号化するために使用されます。  

![証明書をエクスポートします。](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

別のホスト コンピューター上のゲートウェイを復元するとき、以前にこの証明書を使用して暗号化された資格情報の暗号化を解除するために、登録ウィザードによってこの証明書が要求されます。  この証明書がない場合、新しいゲートウェイで資格情報の暗号化を解除できず、この新しいゲートウェイに関連付けられている後続のコピー アクティビティの実行に失敗します。  

#### <a name="resolution"></a>解決策
Data Management Gateway Configuration Manager の **[設定]** タブにある **[エクスポート]** を使用して元のゲートウェイ コンピューターの資格情報証明書をエクスポートしている場合は、ここでその証明書を使用します。

ゲートウェイを回復させるうえでこのステージをスキップすることはできません。 証明書がない場合は、ポータルからゲートウェイを削除し、新しいゲートウェイを再作成する必要があります。  さらに、資格情報を再入力して、ゲートウェイに関係するすべてのリンクされたサービスを更新します。

### <a name="8-problem"></a>8.問題点
次のようなエラー メッセージが表示されることがあります。

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>原因
このエラーは、インターネット リソースにアクセスするために HTTP プロキシを必要とする環境にゲートウェイが配置されている場合や、プロキシの認証パスワードが変更されているにもかかわらずゲートウェイでその情報が更新されていない場合に発生します。

#### <a name="resolution"></a>解決策
この記事のプロキシ サーバーに関する考慮事項セクションの手順に従い、Data Management Gateway Configuration Manager でプロキシ設定を構成します。

## <a name="gateway-is-online-with-limited-functionality"></a>ゲートウェイはオンラインだが機能が制限されている
### <a name="1-problem"></a>1.問題点
ゲートウェイの状態が、オンラインではあるものの機能が制限されています。

#### <a name="cause"></a>原因
次のいずれかの理由で、ゲートウェイの状態が、オンラインではあるものの機能が制限されています。

* ゲートウェイから Azure Service Bus 経由でクラウド サービスに接続できない。
* クラウド サービスから Service Bus 経由でゲートウェイに接続できない。

ゲートウェイがオンラインだが機能が制限されている場合、Data Factory コピー ウィザードを使用してオンプレミスのデータ ストアとの間でデータをコピーするためのデータ パイプラインを作成できない可能性があります。 回避策として、ポータル、Visual Studio、または Azure PowerShell で、Data Factory エディターを使用することができます。

#### <a name="resolution"></a>解決策
この問題 (オンラインだが機能が制限されている) の解決策は、ゲートウェイからクラウド サービスに接続できないのか、その逆かによって異なります。 次のセクションでそれぞれの解決策を説明します。

### <a name="2-problem"></a>2.問題点
次のエラーが表示されます。

`Error: Gateway cannot connect to cloud service through service bus`

![ゲートウェイからクラウド サービスに接続できない](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>原因
ゲートウェイから Service Bus 経由でクラウド サービスに接続できない。

#### <a name="resolution"></a>解決策
次の手順に従って、ゲートウェイをオンラインに戻します。

1. ゲートウェイ コンピューターと会社のファイアウォールで IP アドレスの送信規則を許可します。 Windows イベント ログ (ID == 401) の"An attempt was made to access a socket in a way forbidden by its access permissions XX.XX.XX.XX:9350 (アクセス許可 XX.XX.XX.XX:9350 によって禁止されている方法でソケットへのアクセスが試みられました)" というメッセージから、IP アドレスを検出できます。
1. ゲートウェイのプロキシ設定を構成します。 詳細については、プロキシ サーバーに関する考慮事項セクションを参照してください。
1. ゲートウェイ コンピューターの Windows ファイアウォールと会社のファイアウォールの両方で送信ポート 5671 および 9350 ～ 9354 を有効にします。 詳細については、ポートとファイアウォールのセクションを参照してください。 この手順は省略できますが、パフォーマンスの観点から推奨されます。

### <a name="3-problem"></a>手順 3.問題点
次のエラーが表示されます。

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>原因
ネットワーク接続の一時的なエラーです。

#### <a name="resolution"></a>解決策
次の手順に従って、ゲートウェイをオンラインに戻します。

1. 数分待ちます。エラーが解決すると、接続が自動的に回復します。
1. エラーが解決しない場合は、ゲートウェイ サービスを再起動してください。

## <a name="failed-to-author-linked-service"></a>リンクされたサービスを作成できない
### <a name="problem"></a>問題点
ポータルの資格情報マネージャーを使用して新しいリンクされたサービスの資格情報を入力しようとしたときや、既存のリンクされたサービスの資格情報を更新しようとしたときに、このエラーが表示される場合があります。

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

このエラーが表示される場合、Data Management Gateway Configuration Manager の設定ページは、次のスクリーン ショットのようになる可能性があります。

![データ ストアに接続できない](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>原因
ゲートウェイ コンピューター上で SSL 証明書が見つからない可能性があります。 現在 SSL 暗号化に使用されている証明書をゲートウェイ コンピューターで読み込むことができません。 イベント ログに次のようなエラー メッセージが表示される場合もあります。

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>解決策
この問題を解決するには、次の手順に従います。

1. Data Management Gateway Configuration Manager を起動します。
2. **[設定]** タブに切り替えます。  
3. SSL 証明書を変更するために、 **[変更]** をクリックします。

   ![証明書の変更ボタン](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. 新しい証明書を SSL 証明書として選択します。 自分で生成した SSL 証明書も、任意の組織によって生成された SSL 証明書も使用できます。

   ![証明書の指定](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>コピー アクティビティが失敗する
### <a name="problem"></a>問題点
ポータルでパイプラインを設定した後、次の "UserErrorFailedToConnectToSqlserver" エラーが表示される場合があります。

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>原因
このエラーはさまざまな理由で発生するため、対応策もそれに応じて異なります。

#### <a name="resolution"></a>解決策
SQL データベースに接続する前に、Data Management Gateway クライアント側でポート TCP/1433 での送信 TCP 接続を許可します。

ターゲット データベースが Azure SQL データベースの場合は、SQL Server の Azure 用のファイアウォールの設定も確認します。

次のセクションを参照して、オンプレミスのデータ ストアへの接続をテストします。

## <a name="data-store-connection-or-driver-related-errors"></a>データ ストア接続またはドライバーに関連するエラー
データ ストア接続やドライバー関連のエラーが表示された場合は、以下の手順を実行します。

1. ゲートウェイ コンピューターで Data Management Gateway Configuration Manager を起動します。
2. **[診断]** タブに切り替えます。
3. **[接続テスト]** で、ゲートウェイ グループ値を追加します。
4. **[テスト]** をクリックして、その接続情報と資格情報で、ゲートウェイ コンピューターからオンプレミスのデータ ソースに接続できるかどうかを確認します。 ドライバーのインストール後も接続テストが失敗する場合は、最新の変更を認識できるようにゲートウェイを再起動します。

![[診断] タブの [接続のテスト]](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>ゲートウェイ ログ
### <a name="send-gateway-logs-to-microsoft"></a>ゲートウェイ ログを Microsoft に送信する
ゲートウェイに関する問題のトラブルシューティングのために Microsoft サポートに連絡する場合、ゲートウェイ ログの共有を求められる場合があります。 ゲートウェイのリリース版では、Data Management Gateway Configuration Manager でボタンを 2 回クリックすることで、必要なゲートウェイ ログを簡単に共有できます。    

1. Data Management Gateway Configuration Manager で **[診断]** タブに切り替えます。

    ![Data Managemetn Gateway - [診断] タブ](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. **[ログの送信]** をクリックして次のダイアログ ボックスを表示します。

    ![Data Management Gateway - ログの送信](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (省略可能) **[ログの表示]** をクリックしてイベント ビューアーでログを確認します。
4. (省略可能) **[プライバシー]** をクリックして Microsoft Web サービスのプライバシーに関する声明を表示します。
5. アップロードする情報に問題がなければ、 **[ログの送信]** をクリックすると、トラブルシューティングのために過去 7 日間のログが Microsoft に実際に送信されます。 ログ送信操作の状態が、次のスクリーンショットのように表示されます。

    ![Data Management Gateway - ログの送信の状態](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. 操作が完了すると、次のスクリーンショットのようなダイアログ ボックスが表示されます。

    ![Data Management Gateway - ログの送信の状態](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. **レポート ID** を保存し、Microsoft サポートに伝えます。 レポート ID は、トラブルシューティング用にアップロードしたゲートウェイ ログを特定するために使われます。  レポート ID はイベント ビューアーにも保存されます。  レポート ID を探すときは、イベント ID "25" を見つけて日付と時刻を確認します。

    ![Data Management Gateway - ログの送信のレポート ID](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>ゲートウェイ ホスト コンピューターでゲートウェイ ログをアーカイブする
ゲートウェイに問題が発生し、ゲートウェイ ログを直接共有できない場合として、次のようないくつかのシナリオがあります。

* 手動でゲートウェイをインストールしてゲートウェイを登録します。
* Data Management Gateway Configuration Manager で再生成されたキーを使ってゲートウェイを登録しようとしています。
* ログを送信しようとしたときに、ゲートウェイ ホスト サービスに接続できません。

このようなシナリオでは、ゲートウェイ ログを zip ファイルとして保存し、Microsoft サポートに連絡するときにそれを共有できます。 たとえば、ゲートウェイを登録するときに、次のスクリーンショットのようなエラーが表示されたら、以下の操作を行います。   

![Data Management Gateway - 登録エラー](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

**[ゲートウェイ ログのアーカイブ]** リンクをクリックしてログをアーカイブして保存し、zip ファイルを Microsoft サポートと共有します。

![Data Management Gateway - ログのアーカイブ](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>ゲートウェイ ログの検索
詳細なゲートウェイ ログの情報は、Windows イベント ログで確認できます。

1. Windows **イベント ビューアー**を起動します。
2. **[アプリケーションとサービス ログ]**  >  **[Data Management Gateway]** フォルダーでログを見つけます。

   ゲートウェイ関連の問題のトラブルシューティングでは、イベント ビューアーでエラー レベルのイベントを調べます。

![Data Management Gateway - イベント ビューアーに表示されたログ](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
