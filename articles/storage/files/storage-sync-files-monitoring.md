---
title: Azure File Sync 監視する | Microsoft Docs
description: Azure File Sync の監視方法。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 86c4bf328430bbc623d8e493eec5db520d50ef82
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485974"
---
# <a name="monitor-azure-file-sync"></a>Azure File Sync の監視

Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま Azure Files で組織のファイル共有を一元化できます。 Azure File Sync により、ご利用の Windows Server が Azure ファイル共有の高速キャッシュに変わります。 SMB、NFS、FTPS など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルにアクセスできます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

この記事では、Azure Monitor、ストレージ同期サービス、Windows Server を使用して、Azure File Sync デプロイを監視する方法について説明します。

現在、以下の監視オプションを利用できます。

## <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) を使用してメトリックを表示し、同期、クラウドを使った階層化、およびサーバー接続に関するアラートを構成します。  

### <a name="metrics"></a>メトリック

Azure File Sync のメトリックは既定で有効になっており、15 分ごとに Azure Monitor に送信されます。

Azure Monitor 内で Azure File Sync のメトリックを表示するには、**ストレージ同期サービス** リソースの種類を選択します。

Azure Monitor では、Azure File Sync の次のメトリックを使用できます。

| メトリックの名前 | 説明 |
|-|-|
| 同期したバイト数 | データ転送 (アップロードとダウンロード) のサイズ。<br><br>単位:Bytes<br>集計の種類:合計<br>適用可能なディメンション:サーバー エンドポイント名、同期の方向、同期グループ名 |
| クラウドを使った階層化の呼び戻し | 呼び戻されたデータのサイズ。<br><br>**メモ**:このメトリックは今後削除される予定です。 クラウドを使った階層化の呼び戻しサイズ メトリックを使用して、呼び戻されたデータのサイズを監視します。<br><br>単位:Bytes<br>集計の種類:合計<br>適用可能なディメンション:サーバー名 |
| クラウドを使った階層化の呼び戻しサイズ | 呼び戻されたデータのサイズ。<br><br>単位:Bytes<br>集計の種類:合計<br>適用可能なディメンション:サーバー名、同期グループ名 |
| アプリケーションによるクラウドを使った階層化の呼び戻しサイズ | アプリケーションによって呼び戻されたデータのサイズ。<br><br>単位:Bytes<br>集計の種類:合計<br>適用可能なディメンション:アプリケーション名、サーバー名、同期グループ名 |
| クラウドを使った階層化の呼び戻しスループット | データの呼び戻しスループットのサイズ。<br><br>単位:Bytes<br>集計の種類:合計<br>適用可能なディメンション:サーバー名、同期グループ名 |
| ファイルが同期していない | 同期に失敗しているファイルの数。<br><br>単位:Count<br>集計の種類:合計<br>適用可能なディメンション:サーバー エンドポイント名、同期の方向、同期グループ名 |
| 同期されたファイル数 | 転送された (アップロードとダウンロード) ファイルの数。<br><br>単位:Count<br>集計の種類:合計<br>適用可能なディメンション:サーバー エンドポイント名、同期の方向、同期グループ名 |
| サーバーのオンライン状態 | サーバーから受信したハートビートの数。<br><br>単位:Count<br>集計の種類:最大値<br>適用可能なディメンション:サーバー名 |
| 同期セッションの結果 | 同期セッションの結果 (1=成功した同期セッション; 0=失敗した同期セッション)<br><br>単位:Count<br>集計の種類:最大値<br>適用可能なディメンション:サーバー エンドポイント名、同期の方向、同期グループ名 |

### <a name="alerts"></a>アラート

Azure Monitor でアラートを構成するには、ストレージ同期サービスを選択し、アラートに使用する [Azure File Sync のメトリック](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics)を選択します。  

次の表に、監視するシナリオの例とアラートに使用する適切なメトリックを示します。

| シナリオ | アラートに使用するメトリック |
|-|-|
| ポータルのサーバー エンドポイントの正常性 = エラー | 同期セッションの結果 |
| ファイルがサーバーまたはクラウド エンドポイントへの同期に失敗する | ファイルが同期していない |
| 登録済みのサーバーがストレージ同期サービスと通信できない | サーバーのオンライン状態 |
| クラウドを使った階層化の呼び戻しサイズが 1 日で 500 GiB を超えた  | クラウドを使った階層化の呼び戻しサイズ |

Azure Monitor でのアラートの構成の詳細については、「[Microsoft Azure のアラートの概要]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)」をご覧ください。

## <a name="storage-sync-service"></a>ストレージ同期サービス

登録済みサーバーの正常性、サーバー エンドポイントの正常性、およびメトリックを表示するには、Azure portal 内でストレージ同期サービスに移動します。 **[登録済みサーバー]** ブレードに登録済みサーバーの正常性が表示され、 **[同期グループ]** ブレードにサーバー エンドポイントの正常性が表示されます。

### <a name="registered-server-health"></a>登録済みサーバーの正常性

- **登録済みサーバー**の状態が **[オンライン]** である場合、サーバーは正常にサービスと通信しています。
- **登録済みサーバー**の状態が **[オフラインのようです]** である場合は、ストレージ同期モニター (AzureStorageSyncMonitor.exe) プロセスがサーバー上で実行されているか確認します。 サーバーがファイアウォールまたはプロキシの内側にある場合は、[こちらの記事](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy)を参照して、ファイアウォールとプロキシを構成してください。

### <a name="server-endpoint-health"></a>サーバー エンドポイントの正常性

- ポータルのサーバー エンドポイントの正常性は、サーバーのテレメトリ イベント ログに記録されている同期イベント (ID 9102 および 9302) に基づきます。 一時的なエラーが原因で同期セッションが失敗した場合 (エラーで取り消された場合など)、現在の同期セッションが進行中である限り、ポータルには同期の正常性が引き続き表示される可能性があります。 ファイルが適用されているかどうかを判断するために、イベント ID 9302 が使用されます。 詳細については、[同期の正常性](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)と[同期の進行状況](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)に関するドキュメントを参照してください。
- 同期が進行していないことが原因でポータルに同期エラーが表示された場合は、[トラブルシューティングに関するドキュメント](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)を参考にしてください。

### <a name="metric-charts"></a>メトリック グラフ

- ストレージ同期サービス ポータルでは、次のメトリック グラフを表示できます。

  | メトリックの名前 | 説明 | ブレードの名前 |
  |-|-|-|
  | 同期したバイト数 | 転送された (アップロードとダウンロード) データのサイズ | 同期グループ、サーバー エンドポイント |
  | クラウドを使った階層化の呼び戻し | 呼び戻されたデータのサイズ | 登録済みサーバー |
  | ファイルが同期していない | 同期に失敗しているファイルの数 | サーバー エンドポイント |
  | 同期されたファイル数 | 転送された (アップロードとダウンロード) ファイルの数 | 同期グループ、サーバー エンドポイント |
  | サーバーのオンライン状態 | サーバーから受信したハートビートの数 | 登録済みサーバー |

- 詳細については、「[Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)」セクションを参照してください。

  > [!Note]  
  > ストレージ同期サービス ポータルのグラフには、24 時間の時間範囲があります。 異なる時間範囲やディメンションを表示するには、Azure Monitor を使用します。

## <a name="windows-server"></a>Windows Server

Windows Server では、クラウドを使った階層化、登録済みのサーバー、および同期の正常性を表示できます。

### <a name="event-logs"></a>イベント ログ

サーバーのテレメトリ イベント ログを使用して、登録済みサーバー、同期、およびクラウドを使った階層化の正常性を監視します。 テレメトリ イベント ログは、イベント ビューアーの *Applications and Services\Microsoft\FileSync\Agent* にあります。

同期の正常性:

- 同期セッションが終了すると、イベント ID 9102 がログに記録されます。 同期セッションが正常に終了している (**HResult = 0**) かどうか、および項目単位の同期エラーがあるかどうかを確認する場合は、このイベントを使用します。 詳細については、[同期の正常性](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)および[項目単位のエラー](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)に関するドキュメントを参照してください。

  > [!Note]  
  > 同期セッション全体が失敗する場合や、PerItemErrorCount が 0 以外の場合があります。 しかし、それでも同期セッションは進行し、一部のファイルは正常に同期します。 このことは、Applied フィールド (AppliedFileCount、AppliedDirCount、AppliedTombstoneCount、AppliedSizeBytes) で確認できます。 このフィールドでは、成功したセッションの数が示されます。 行内に失敗した同期セッションが複数あるのに、Applied の数が増えている場合は、サポート チケットを開く前に、時間をとって同期の再試行を待ちます。

- アクティブな同期セッションがある場合は、5 分から 10 分ごとにイベント ID 9302 がログに記録されます。 現在の同期セッションが進行中 (**AppliedItemCount が 0 より大きい**) かどうかを確認するには、このイベントを使用します。 同期が進行していない場合、同期セッションは最終的に失敗するはずです。イベント ID 9102 はエラーと共にログに記録されます。 詳細については、[同期の進行に関するドキュメント](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)をご覧ください。

登録済みサーバーの正常性:

- サーバーでサービスのジョブについてクエリが実行されている場合、30 秒ごとにイベント ID 9301 がログに記録されます。 GetNextJob が **0 の状態**で終了した場合、サーバーはサービスと通信できます。 GetNextJob がエラーで終了した場合は、[トラブルシューティングに関するドキュメント](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)を参考にしてください。

クラウドを使った階層化の正常性:

- サーバー上の階層化アクティビティを監視するには、テレメトリ イベント ログ (イベント ビューアーの *[アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent]* の下) にあるイベント ID 9003、9016、9029 を使用します。

  - イベント ID 9003 は、サーバー エンドポイントのエラー分布を示します。 例: Total Error Count や ErrorCode。 エラー コードごとに 1 つのイベントがログ記録されます。
  - イベント ID 9016 は、ボリュームの非実体化の結果を示します。 例: 空き領域の割合、セッション内の非実体化されたファイルの数、非実体化が失敗したファイルの数。
  - イベント ID 9029 では、サーバー エンドポイントの非実体化セッション情報が提供されます。 例: セッション内の試行されたファイルの数、セッション内の階層化されたファイルの数、既に階層化されているファイルの数。
  
- サーバー上の呼び戻しアクティビティを監視するには、テレメトリ イベント ログ (イベント ビューアーの *[アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent]* の下) にあるイベント ID 9005、9006、9009、および 9059 を使用します。

  - イベント ID 9005 は、サーバー エンドポイントの呼び戻しの信頼性を示します。 例: アクセスされた一意のファイルの合計数、アクセスが失敗した一意のファイルの合計数。
  - イベント ID 9006 は、サーバー エンドポイントの呼び戻しエラーの分布を示します。 例: 失敗した要求の合計数、ErrorCode。 エラー コードごとに 1 つのイベントがログ記録されます。
  - イベント ID 9009 では、サーバー エンドポイントの呼び戻しセッション情報が提供されます。 例: DurationSeconds、CountFilesRecallSucceeded、CountFilesRecallFailed。
  - イベント ID 9059 では、サーバー エンドポイントのアプリケーション呼び戻し分布が提供されます。 例: ShareId、Application Name、TotalEgressNetworkBytes。

### <a name="performance-counters"></a>パフォーマンス カウンター

サーバーで Azure File Sync のパフォーマンス カウンターを使用して、同期アクティビティを監視します。

サーバー上で Azure File Sync のパフォーマンス カウンターを表示するには、パフォーマンス モニター (Perfmon.exe) を開きます。 カウンターは、**AFS Bytes Transferred** オブジェクトおよび **AFS Sync Operations** オブジェクトの下にあります。

パフォーマンス モニターでは、Azure File Sync の以下のパフォーマンス カウンターを使用できます。

| パフォーマンス オブジェクト\カウンター名 | 説明 |
|-|-|
| AFS Bytes Transferred\Downloaded Bytes/sec | 1 秒あたりのダウンロードされたバイト数。 |
| AFS Bytes Transferred\Uploaded Bytes/sec | 1 秒あたりのアップロードされたバイト数。 |
| AFS Bytes Transferred\Total Bytes/sec | 1 秒あたりの合計バイト数 (アップロードとダウンロード)。 |
| AFS Sync Operations\Downloaded Sync Files/sec | 1 秒あたりのダウンロードされたファイル数。 |
| AFS Sync Operations\Uploaded Sync Files/sec | 1 秒あたりのアップロードされたファイル数。 |
| AFS Sync Operations\Total Sync File Operations/sec | 同期されたファイルの合計数 (アップロードとダウンロード)。 |

## <a name="next-steps"></a>次の手順
- [Azure File Sync のデプロイの計画](storage-sync-files-planning.md)
- [ファイアウォールとプロキシの設定の考慮事項](storage-sync-files-firewall-and-proxy.md)
- [Azure File Sync をデプロイする](storage-sync-files-deployment-guide.md)
- [Azure File Sync のトラブルシューティング](storage-sync-files-troubleshoot.md)
- [Azure Files についてよく寄せられる質問 (FAQ)](storage-files-faq.md)
