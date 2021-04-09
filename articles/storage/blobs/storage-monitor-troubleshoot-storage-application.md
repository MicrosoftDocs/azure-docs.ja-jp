---
title: Azure でのクラウド ストレージ アプリケーションの監視およびトラブルシューティング | Microsoft Docs
description: クラウド アプリケーションをトラブルシューティングおよび監視するには、診断ツール、メトリック、およびアラートを使用します。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 6c1d9b813db15900521968865c1036d53ad53f41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544787"
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>クラウド ストレージ アプリケーションの監視およびトラブルシューティング

このチュートリアルはシリーズの第 4 部であり、かつ最後の部分です。 クラウド ストレージ アプリケーションを監視およびトラブルシューティングする方法を学習します。

シリーズの第 4 部では、次の方法を学習します。

> [!div class="checklist"]
> * ログ記録とメトリックを有効にする
> * 承認エラーのアラートを有効にする
> * 正しくない SAS トークンでテスト トラフィックを実行する
> * ログをダウンロードして分析する

[Azure Storage Analytics](../common/storage-analytics.md) では、ストレージ アカウントのログ記録およびメトリック データが提供されます。 このデータにより、ストレージ アカウントの正常性を確認できます。 Azure Storage Analytics からデータを収集するには、ログ記録、メトリック、およびアラートを構成します。 このプロセスには、ログ記録の有効化、メトリックの構成、およびアラートの有効化が含まれます。

ストレージ アカウントのログ記録とメトリックは、Azure Portal の **[診断]** タブで有効にします。 ストレージのログ記録では、ストレージ アカウント内の成功した要求と失敗した要求の両方の詳細を記録できます。 これらのログにより、Azure テーブル、キュー、および BLOB に対する読み取り、書き込み、および削除操作の詳細を確認できます。 また、タイムアウト、調整、承認エラーなど、失敗した要求の理由を確認することもできます。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure ポータル](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>ログ記録とメトリックを有効にする

左側のメニューから、 **[リソース グループ]** を選択し、 **[myResourceGroup]** を選択してから、リソース一覧内のストレージ アカウントを選択します。

**[診断設定 (クラシック)]** で、 **[状態]** を **[オン]** に設定します。 **[BLOB のプロパティ]** のすべてのオプションが有効になっていることを確認します。

完了したら、 **[保存]** をクリックします。

![ログとメトリックを有効にするための構成設定が含まれているセクションが強調表示されているスクリーンショット。](media/storage-monitor-troubleshoot-storage-application/enable-diagnostics.png)

## <a name="enable-alerts"></a>アラートを有効にする

アラートは、管理者に電子メールを送信したり、しきい値を超えているメトリックに基づいて webhook をトリガーしたりする方法を提供します。 この例では、`SASClientOtherError` メトリックのアラートを有効にします。

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Azure Portal でストレージ アカウントに移動します。

**[監視]** セクションで、 **[アラート (クラシック)]** を選択します。

**[メトリック アラートの追加 (クラシック)]** を選択し、必要な情報を入力して **[ルールの追加]** フォームを完成させます。 **[メトリック]** ドロップダウンで、`SASClientOtherError` を選択します。 最初のエラー発生時にアラートのトリガーを許可するには、 **[条件]** ドロップダウンで **[Greater than or equal to]\(この値以上\)** を選択します。

![[診断] ウィンドウ](media/storage-monitor-troubleshoot-storage-application/add-alert-rule.png)

## <a name="simulate-an-error"></a>エラーをシミュレートする

有効なアラートをシミュレートするには、ストレージ アカウントに対して存在しない BLOB の要求を試みることができます。 次のコマンドにはストレージ コンテナー名が必要です。 既存のコンテナーの名前を使用するか、この例で使用する目的で新しいものを作成します。

プレースホルダーを実際の値に置き換え (`<INCORRECT_BLOB_NAME>` が存在しない値に設定されていることを確認して)、コマンドを実行します。

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <STORAGE_ACCOUNT_NAME> \
    --account-key <STORAGE_ACCOUNT_KEY> \
    --container-name <CONTAINER_NAME> \
    --name <INCORRECT_BLOB_NAME> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d`)

curl https://<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/<CONTAINER_NAME>/<INCORRECT_BLOB_NAME>?$sasToken
```

次の図は、前の例で実行したシミュレートされた失敗に基づくアラートの例です。

 ![アラートの例](media/storage-monitor-troubleshoot-storage-application/email-alert.png)

## <a name="download-and-view-logs"></a>ログをダウンロードして表示する

ストレージ ログは、ストレージ アカウント内の **$logs** という名前の BLOB コンテナーにある一連の BLOB 内にデータを格納します。 このコンテナーは、アカウント内のすべての BLOB コンテナーを一覧表示しても表示されませんが、直接それにアクセスすればその内容を見ることができます。

このシナリオでは、[Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide) を使用して Azure ストレージ アカウントを操作します。

### <a name="download-microsoft-message-analyzer"></a>Microsoft Message Analyzer をダウンロードする

[Microsoft Message Analyzer](/message-analyzer/installing-and-upgrading-message-analyzer) をダウンロードし、アプリケーションをインストールします。

アプリケーションを起動し、 **[ファイル]**  >  **[開く]**  >  **[From Other File Sources] \(他のファイル ソースから)** を選択します。

**[File Selector] \(ファイル セレクター)** ダイアログで、 **[+ Add Azure Connection] \(+ Azure 接続の追加)** を選択します。 **ストレージ アカウント名** と **アカウント キー** を入力し、 **[OK]** をクリックします。

![Microsoft Message Analyzer - Azure Storage 接続ダイアログの追加](media/storage-monitor-troubleshoot-storage-application/figure3.png)

接続されたら、ストレージ ツリー ビューのコンテナーを展開してログ BLOB を表示します。 最新のログを選択し、 **[OK]** をクリックします。

![Microsoft Message Analyzer を示すスクリーンショット。選択したログ ファイルが強調表示されています。](media/storage-monitor-troubleshoot-storage-application/figure4.png)

**[New Session] \(新しいセッション)** ダイアログで、 **[開始]** をクリックしてログを表示します。

ログが開いたら、ストレージ イベントを表示できます。 次の図からわかるように、ストレージ アカウントでトリガーされた `SASClientOtherError` が存在しました。 ストレージのログ記録の詳細については、「[Storage Analytics](../common/storage-analytics.md)」を参照してください。

![Microsoft Message Analyzer - イベントの表示](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[ストレージ エクスプローラー](https://azure.microsoft.com/features/storage-explorer/)は、 **$logs** コンテナーやそこに含まれているログなど、ストレージ アカウントを操作するために使用できるもう 1 つのツールです。

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