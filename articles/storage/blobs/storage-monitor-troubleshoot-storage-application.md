---
title: "Azure でのクラウド ストレージ アプリケーションの監視およびトラブルシューティング | Microsoft Docs"
description: "クラウド アプリケーションをトラブルシューティングおよび監視するには、診断ツール、メトリック、およびアラートを使用します。"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: db88c331f79d83e0124519f8b6dbb34514b456dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>クラウド ストレージ アプリケーションの監視およびトラブルシューティング

このチュートリアルはシリーズの第 4 部であり、かつ最後の部分です。 クラウド ストレージ アプリケーションを監視およびトラブルシューティングする方法を学習します。

シリーズの第 4 部では、次の方法を学習します。

> [!div class="checklist"]
> * ログ記録とメトリックを有効にする
> * 承認エラーのアラートを有効にする
> * 正しくない SAS トークンでテスト トラフィックを実行する
> * ログをダウンロードして分析する

[Azure Storage Analytics](../common/storage-analytics.md) では、ストレージ アカウントのログ記録およびメトリック データが提供されます。 このデータにより、ストレージ アカウントの正常性を確認できます。 ストレージ アカウントの状態を確認するには、その前にデータ収集を設定する必要があります。 このプロセスには、ログ記録の有効化、メトリックの構成、およびアラートの有効化が含まれます。

ストレージ アカウントのログ記録とメトリックは、Azure Portal の **[診断]** タブで有効にします。 メトリックには、次の 2 種類があります。 **集計**メトリックは、受信/送信、可用性、待機時間、および成功の割合を収集します。 このメトリックは、BLOB、キュー、テーブル、ファイルのサービスごとに集計されます。 **API ごと**は、Azure Storage サービス API でのストレージ操作ごとに同じ一連のメトリックを収集します。 ストレージのログ記録では、ストレージ アカウント内の成功した要求と失敗した要求の両方の詳細を記録できます。 これらのログにより、Azure テーブル、キュー、および BLOB に対する読み取り、書き込み、および削除操作の詳細を確認できます。 また、タイムアウト、調整、承認エラーなど、失敗した要求の理由を確認することもできます。

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする

[Azure ポータル](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>ログ記録とメトリックを有効にする

左側のメニューから、**[リソース グループ]** を選択し、**[myResourceGroup]** を選択してから、リソース一覧内のストレージ アカウントを選択します。

**[診断]** で、**[状態]** を **[On] \(オン)** に設定します。 **[Blob aggregate metrics] \(BLOB 集計メトリック)**、**[Blob per API metrics] \(BLOB API ごとのメトリック)**、および **[Blob logs] \(BLOB ログ)** が有効になっていることを確認してください。

完了したら、**[保存]** をクリックします。

![[診断] ウィンドウ](media/storage-monitor-troubleshoot-storage-application/figure1.png)

## <a name="enable-alerts"></a>アラートを有効にする

アラートは、管理者に電子メールを送信したり、しきい値を超えているメトリックに基づいて webhook をトリガーしたりする方法を提供します。 この例では、`SASClientOtherError` メトリックのアラートを有効にします。

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Azure Portal でストレージ アカウントに移動します。

左側のメニューから、**[リソース グループ]** を選択し、**[myResourceGroup]** を選択してから、リソース一覧内のストレージ アカウントを選択します。

**[監視]** セクションで、**[アラート ルール]** を選択します。

**[+ Add alert] \(+ アラートの追加)** を選択し、**[アラート ルールの追加]** で、必要な情報を入力します。 **[メトリック]** ドロップダウンから `SASClientOtherError` を選択します。

![[診断] ウィンドウ](media/storage-monitor-troubleshoot-storage-application/figure2.png)

## <a name="simulate-an-error"></a>エラーをシミュレートする

有効なアラートをシミュレートするには、ストレージ アカウントに対して存在しない BLOB の要求を試みることができます。 これを行うには、`<incorrect-blob-name>` 値を存在しない値に置き換えます。 失敗した BLOB 要求をシミュレートするには、次のサンプル コードを数回実行します。

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <incorrect-blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<incorrect-blob-name>?$sasToken
```

次の図は、前の例で実行したシミュレートされた失敗に基づくアラートの例です。

 ![アラートの例](media/storage-monitor-troubleshoot-storage-application/alert.png)

## <a name="download-and-view-logs"></a>ログをダウンロードして表示する

ストレージ ログは、ストレージ アカウント内の **$logs** という名前の BLOB コンテナーにある一連の BLOB 内にデータを格納します。 このコンテナーは、アカウント内のすべての BLOB コンテナーを一覧表示しても表示されませんが、直接それにアクセスすればその内容を見ることができます。

このシナリオでは、[Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx) を使用して Azure ストレージ アカウントを操作します。

### <a name="download-microsoft-message-analyzer"></a>Microsoft Message Analyzer をダウンロードする

[Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) をダウンロードし、アプリケーションをインストールします。

アプリケーションを起動し、**[ファイル]** > **[開く]** > **[From Other File Sources] \(他のファイル ソースから)** を選択します。

**[File Selector] \(ファイル セレクター)** ダイアログで、**[+ Add Azure Connection] \(+ Azure 接続の追加)** を選択します。 **ストレージ アカウント名**と**アカウント キー**を入力し、**[OK]** をクリックします。

![Microsoft Message Analyzer - Azure Storage 接続ダイアログの追加](media/storage-monitor-troubleshoot-storage-application/figure3.png)

接続されたら、ストレージ ツリー ビューのコンテナーを展開してログ BLOB を表示します。 最新のログを選択し、**[OK]** をクリックします。

![Microsoft Message Analyzer - Azure Storage 接続ダイアログの追加](media/storage-monitor-troubleshoot-storage-application/figure4.png)

**[New Session] \(新しいセッション)** ダイアログで、**[開始]** をクリックしてログを表示します。

ログが開いたら、ストレージ イベントを表示できます。 次の図からわかるように、ストレージ アカウントでトリガーされた `SASClientOtherError` が存在しました。 ストレージのログ記録の詳細については、「[Storage Analytics](../common/storage-analytics.md)」を参照してください。

![Microsoft Message Analyzer - イベントの表示](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[ストレージ エクスプローラー](https://azure.microsoft.com/features/storage-explorer/)は、**$logs** コンテナーやそこに含まれているログなど、ストレージ アカウントを操作するために使用できるもう 1 つのツールです。

## <a name="next-steps"></a>次のステップ

シリーズの第 4 部であり、かつ最後の部分では、次に示すような、ストレージ アカウントを監視およびトラブルシューティングする方法を学習しました。

> [!div class="checklist"]
> * ログ記録とメトリックを有効にする
> * 承認エラーのアラートを有効にする
> * 正しくない SAS トークンでテスト トラフィックを実行する
> * ログをダウンロードして分析する

事前に作成されたストレージ サンプルを確認するには、次のリンクに従ってください。

> [!div class="nextstepaction"]
> [Azure Storage のサンプル スクリプト](storage-samples-blobs-cli.md)