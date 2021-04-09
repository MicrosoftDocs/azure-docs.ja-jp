---
title: Azure Cosmos DB Emulator 使用時の問題をトラブルシューティングする
description: Azure Cosmos DB Emulator 使用時のサービス利用不可、証明書、暗号化、バージョン管理に関する問題をトラブルシューティングする方法について説明します。
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: bb07adafbc68ff1e9b7d3ee49bb7631dc4395d77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033461"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-db-emulator"></a>Azure Cosmos DB Emulator 使用時の問題をトラブルシューティングする
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB Emulator では、Azure Cosmos DB サービスを開発目的でエミュレートするローカル環境を利用できます。 この記事のヒントを使用すると、Azure Cosmos DB Emulator をインストールまたは使用するときに発生する問題のトラブルシューティングに役立ちます。 

新しいバージョンのエミュレーターをインストールしてエラーが発生している場合は、データをリセットしていることを確認します。 システム トレイの Azure Cosmos DB Emulator アイコンを右クリックし、[Reset Data….]\(データのリセット….\) をクリックすると、データをリセットできます。 それでエラーが解決されない場合は、そのエミュレーターとエミュレーターの以前のすべてのバージョン (見つかった場合) をアンインストールし、*C:\Program files\Azure Cosmos DB Emulator* ディレクトリを削除してから、エミュレーターを再インストールすることができます。 手順については、「[ローカル エミュレーターのアンインストール](local-emulator.md#uninstall)」をご覧ください。 あるいは、データのリセットが機能しない場合は、`%LOCALAPPDATA%\CosmosDBEmulator` の場所に移動してそのフォルダーを削除します。

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>破損している Windows パフォーマンス カウンターをトラブルシューティングする

* Azure Cosmos DB Emulator がクラッシュした場合は、`%LOCALAPPDATA%\CrashDumps` フォルダーからダンプ ファイルを収集して圧縮し、[Azure portal](https://portal.azure.com) からサポート チケットを開きます。

* `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` にクラッシュが発生した場合、パフォーマンス カウンターが破損していることを示す兆候である可能性が考えられます。 この問題は通常、管理者のコマンド プロンプトから次のコマンドを実行すると解消します。

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>接続の問題のトラブルシューティング

* 接続の問題が発生した場合は、[トレース ファイルを収集](#trace-files)し、それらを圧縮して、[Azure portal](https://portal.azure.com) からサポート チケットを開いてください。

* "**サービス利用不可**" というメッセージが表示された場合、エミュレーターがネットワーク スタックの初期化に失敗している可能性があります。 Pulse Secure クライアントまたは Juniper Networks クライアントのネットワーク フィルター ドライバーが問題の原因である可能性があるため、これらのクライアントがインストールされているかどうかを確認してください。 通常、サード パーティのネットワーク フィルター ドライバーをアンインストールすると、問題が解決されます。 このほか、/DisableRIO オプションを指定してエミュレーターを起動する方法もあります。このオプションを使うと、エミュレーターのネットワーク通信が通常の Winsock に切り替わります。 

* **"禁止"、"メッセージ": "転送プロトコルまたは暗号で禁止された暗号化を使用して要求が行われています。アカウントの SSL/TLS の許可されている最小プロトコル設定を確認してください..."** の接続の問題が発生した場合、これは OS のグローバルな変更 (Insider Preview ビルド 20170 など) または既定として TLS 1.3 を有効にするブラウザーの設定が原因である可能性があります。 SDK を使用して Cosmos エミュレーターに対して要求を実行すると、同様のエラーが発生する場合があります。例: **Microsoft.Azure.Documents.DocumentClientException: 転送プロトコルまたは暗号で禁止された暗号化を使用して要求が行われています。アカウントの SSL/TLS の許可されている最小プロトコル設定を確認してください**。 Cosmos エミュレーターは TLS 1.2 プロトコルのみを受け入れて動作するため、この時点ではこれが予想されます。 推奨される回避策として、設定を変更し、TLS 1.2 を既定の設定にします。たとえば、IIS マネージャーで [サイト]、[既定の Web サイト] の順に移動し、ポート 8081 の [サイト バインド] を見つけて TLS 1.3 を無効にするように編集します。 [設定] オプションを使用して、Web ブラウザーに対して同様の操作を実行できます。

* エミュレーターの実行中に、ご利用のコンピューターがスリープ モードになった場合や、そのコンピューターで OS を更新した場合、"**サービスは現在使用できません**" というメッセージが表示される可能性があります。 Windows 通知トレイに表示されているアイコンを右クリックし、 **[Reset Data]\(データのリセット\)** を選択して、エミュレーターのデータをリセットします。

## <a name="collect-trace-files"></a><a id="trace-files"></a>トレース ファイルの収集

デバッグ トレースを収集するには、管理コマンド プロンプトから次のコマンドを実行します。

1. エミュレーターがインストールされているパスに移動します。

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. エミュレーターをシャットダウンし、システム トレイを監視してプログラムがシャットダウンしたことを確認します。 これは、完了するまでに 1 分かかることがあります。 また、Azure Cosmos DB Emulator のユーザー インターフェイスで **[終了]** を選択することもできます。

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. 次のコマンドでログ記録を開始します。

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. エミュレーターを起動します。

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. 問題を再現します。 データ エクスプローラーが動作していない場合は、エラーをキャッチするために、ブラウザーが開くまで数秒待つだけで済みます。

1. 次のコマンドでログ記録を停止します。

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. `%ProgramFiles%\Azure Cosmos DB Emulator` のパスに移動し、*docdbemulator_000001.etl* ファイルを見つけます。

1. [Azure portal](https://portal.azure.com) でサポート チケットを開き、再現手順と共に .etl ファイルを追加します。

## <a name="next-steps"></a>次のステップ

この記事では、ローカル エミュレーターに関する問題をデバッグする方法について学習しました。 これで、次の記事に進むことができます。

* [Java、Python、および Node.js アプリで使用する Azure Cosmos DB エミュレーター証明書のエクスポート](local-emulator-export-ssl-certificates.md)
* [コマンド ライン パラメーターと PowerShell コマンドを使用してエミュレーターを制御する](emulator-command-line-parameters.md)
