---
title: Synapse Studio のトラブルシューティング
description: Azure Synapse Studio のトラブルシューティング
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 7d91001024ad547e52fe48ee30749fee9a4fb4a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98116179"
---
# <a name="azure-synapse-studio-troubleshooting"></a>Azure Synapse Studio のトラブルシューティング

このトラブルシューティング ガイドでは、ネットワーク接続の問題についてサポート チケットを開くときに入力する情報について説明します。 適切な情報があれば、問題をより迅速に解決できる可能性があります。

## <a name="serverless-sql-pool-service-connectivity-issue"></a>サーバーレス SQL プール サービスの接続の問題

### <a name="symptom-1"></a>現象 1

[接続先] ドロップダウンで、[Serverless SQL pool]\(サーバーレス SQL プール\) オプションがグレー表示される。

![現象 1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>現象 2

[Serverless SQL pool]\(サーバーレス SQL プール\) を使用してクエリを実行すると、[Failed to establish connection to server]\(サーバーへの接続を確立できませんでした\) というエラー メッセージが表示される。

![現象 2](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順

> [!NOTE] 
>    次のトラブルシューティング手順は、Chromium Edge と Chrome を対象としています。 同じトラブルシューティング手順を他のブラウザー (FireFox など) に使用することもできますが、[開発者ツール] ウィンドウは、この TSG のスクリーンショットとは異なるレイアウトになる場合があります。 可能であれば、トラブルシューティングにクラシック Edge は使用しないでください (特定の状況で不正確な情報が表示される可能性があるため)。

[診断情報] パネルを開き、[診断のダウンロード] ボタンを選択します。 ダウンロードした情報をエラー報告用に保管します。 または、"セッション ID" をコピーして、サポート チケットを開くときに添付することができます。

![診断情報](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

トラブルシューティングを開始するには、実行した操作を Azure Synapse Studio で再試行してください。

- 現象 1 については、[SQL スクリプト] タブの [データベースの使用] ドロップダウンの右側にある [更新] ボタンを選択し、[Serverless SQL pool]\(サーバーレス SQL プール\) が表示されるかどうかを確認します。
- 現象 2 については、クエリをもう一度実行して、正常に実行されているかどうかを確認してください。

問題がまだ発生する場合は、ブラウザーで F12 キーを押して、"開発者ツール" (DevTools) を開きます。

[開発者ツール] ウィンドウで、[ネットワーク] パネルに切り替えます。 必要に応じて、[ネットワーク] パネルでツール バーの [クリア] ボタンを選択します。
[ネットワーク] パネルで [Disable cache]\(キャッシュを無効化\) がオンになっていることを確認します。

実行した操作を Azure Synapse Studio で再試行します。 "開発者ツール" の [ネットワーク] の一覧に新しい項目が表示される場合があります。 現在のシステム時刻をメモして、サポート チケットに記載してください。

![ネットワーク パネル 1](media/troubleshooting-synapse-studio/network-panel.png)

URL 列が次のパターンに一致する項目を探します。

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

ここで、[*A*] はワークスペース名で、"-ondemand" は "-sqlod" などになります。[*B*] はデータベース名 ("master" など) である必要があります。 URL の値が同じでメソッドの値が異なる (OPTIONS と POST) 項目は、最大 2 つである必要があります。 この 2 つの項目の状態列に "200" または "20x" が表示されているかどうかを確認します。"x" は任意の 1 桁の数字です。

いずれか一方に "20x" 以外のものが含まれている場合は、次のようになります。

- 状態が "(失敗)" で始まる場合は、[状態] 列を拡大するか、状態テキストの上にポインターを置き、テキスト全体を表示します。 サポート チケットを開くときに、テキストまたはスクリーンショットあるいはその両方を含めます。

    ![状態テキスト](media/troubleshooting-synapse-studio/status-text.png)

    - ERR_NAME_NOT_RESOLVED が表示され、10 分以内にワークスペースを作成した場合は、10 分間待機してから、問題がまだ存在するかどうかを確認してください。
    - ERR_INTERNET_DISCONNECTED または ERR_NETWORK_CHANGED が表示された場合は、PC ネットワーク接続に問題があることを示している可能性があります。 ネットワーク接続を確認して、操作をやり直してください。
    - ERR_CONNECTION_RESET、ERR_SSL_PROTOCOL_ERROR、その他 "SSL" を含むエラー コードが表示された場合は、ローカルの SSL 構成に問題が発生しているか、サーバーレス SQL プール サーバーへのアクセスがネットワーク管理者によってブロックされていることを示している可能性があります。 サポート チケットを開き、説明にエラー コードを添付します。
    - ERR_NETWORK_ACCESS_DENIED が表示された場合は、ローカル ファイアウォール ポリシーで *.database.windows.net ドメインまたはリモート ポート 1443 へのアクセスがブロックされているかどうかを管理者に確認する必要があります。
    - 必要に応じて、別のコンピューターまたはネットワーク環境で同じ操作を直ちに実行して、PC 上のネットワーク構成の問題を除外します。

- 状態が "40x"、"50x"、またはその他の数値である場合は、項目を選択して詳細を表示します。 右側に項目の詳細が表示されます。 "応答ヘッダー" セクションを見つけます。次に、"access-control-allow-origin" という名前の項目が存在するかどうかを確認します。 該当する場合は、次のいずれかの値が示されます。

    - `*` (1 個のアスタリスク)
    - https://web.azuresynapse.net/ (またはブラウザーのアドレス バーのテキストの先頭のその他の値)

応答ヘッダーに上記のいずれかの値が含まれている場合は、エラー情報が既に収集されたことを意味します。 必要に応じてサポート チケットを開き、オプションで項目の詳細のスクリーンショットを添付できます。

ヘッダーが表示されない場合、またはヘッダーに上記の値のいずれかが示されていない場合は、チケットを開いたときに項目の詳細のスクリーンショットを添付します。

 
![項目の詳細](media/troubleshooting-synapse-studio/item-details.png)
 
上記の手順で問題が解決しない場合は、サポート チケットを開くことが必要な可能性があります。 サポート チケットを送信するときは、このガイドの冒頭でダウンロードした "セッション ID" または "診断情報" を含めてください。

問題を報告するときに、必要に応じて、"開発者ツール" の [コンソール] タブのスクリーンショットを取得し、サポート チケットに添付することもできます。 コンテンツをスクロールし、必要に応じて複数のスクリーンショットを取得して、メッセージ全体をキャプチャします。

![開発者ツールのコンソール](media/troubleshooting-synapse-studio/developer-tool-console.png)

スクリーンショットを添付する場合は、スクリーンショットを取得した時刻 (または推定時間の範囲) を記述します。 それは問題を調査するときに役立ちます。

一部のブラウザーでは、[コンソール] タブでのタイムスタンプの表示がサポートされています。Chromium Edge または Chrome の場合は、"開発者ツール" の [設定] ダイアログを開き、[基本設定] タブの [タイムスタンプを表示] をオンにします。

![開発者ツールのコンソール設定](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![タイムスタンプの表示](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>次のステップ
前の手順で問題が解決しない場合は、[サポート チケットを作成](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)してください