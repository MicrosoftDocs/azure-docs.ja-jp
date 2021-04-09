---
title: Azure portal を使用した Azure Data Box Gateway のトラブルシューティング | Microsoft Docs
description: Azure Data Box Gateway に関する問題のトラブルシューティング方法について説明します。 診断を実行し、サポートに関する情報を収集し、ログを使用してトラブルシューティングを行うことができます。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: troubleshooting
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e5280805090a83245e287c0b15a1735ee41e934e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581352"
---
# <a name="troubleshoot-your-azure-data-box-gateway-issues"></a>Azure Data Box Gateway に関する問題のトラブルシューティング

この記事では、Azure Data Box Gateway に関する問題のトラブルシューティングを行う方法について説明します。
この記事では、次のことについて説明します。

> [!div class="checklist"]
>
> * 診断の実行
> * サポート パッケージの収集
> * ログを使用したトラブルシューティング

## <a name="run-diagnostics"></a>診断の実行

デバイスのエラーの診断とトラブルシューティングを行うには、診断テストを実行します。 診断テストを実行するには、お客様のデバイスのローカル Web UI で、次の手順を実行します。

1. ローカル Web UI で、 **[トラブルシューティング]、[診断テスト]** の順に移動します。 実行したいテストを選択し、 **[テストの実行]** をクリックします。 これにより、お客様のネットワーク、デバイス、Web プロキシ、時刻、またはクラウドの設定で発生する可能性のある問題を診断するためのテストが実行されます。 デバイスでテストを実行中であることが通知されます。

    ![[ユーザーの追加] をクリックする](media/data-box-gateway-troubleshoot/run-diag-1.png)
 
2. テストが完了すると、結果が表示されます。 テストが失敗すると、推奨される操作の URL が表示されます。 URL をクリックすると、推奨される操作を表示することができます。 
 
    ![[ユーザーの追加] をクリックする 2](media/data-box-gateway-troubleshoot/run-diag-2.png)


## <a name="collect-support-package"></a>サポート パッケージの収集

ログ パッケージは、Microsoft サポートがデバイスの問題のトラブルシューティングを行う際に役立つすべての関連ログで構成されます。 ログ パッケージは、ローカル Web UI を使用して生成することができます。 

サポート パッケージを収集するには、次の手順を実行します。 

1. ローカル Web UI で、 **[トラブルシューティング]、[サポート]** の順に移動します。 **[サポート パッケージの作成]** をクリックします。 システムでサポート パッケージの収集が開始されます。 パッケージの収集には数分かかる場合があります。

    ![[ユーザーの追加] をクリックする 3](media/data-box-gateway-troubleshoot/collect-logs-1.png)
 
2. サポート パッケージが作成されたら、 **[サポート パッケージのダウンロード]** をクリックします。 圧縮されたパッケージが、お客様が選択したパスにダウンロードされます。 そのパッケージを解凍してください。

    ![[ユーザーの追加] をクリックする 4](media/data-box-gateway-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>ログを使用したトラブルシューティング

アップロード プロセス中および更新プロセス中に発生したすべてのエラーが、それぞれのエラー ファイルに含まれています。

1. エラー ファイルを表示するには、共有に移動し、内容を表示する共有をクリックします。 

      ![[ユーザーの追加] をクリックする 5](media/data-box-gateway-troubleshoot/troubleshoot-logs-1.png)

2. "_Microsoft Data Box Gateway フォルダー_" をクリックします。 このフォルダーには、次の 2 つのサブフォルダーがあります。

   - アップロードのエラー用のログ ファイルがある Upload フォルダー。
   - 更新中のエラー用の Refresh フォルダー。

     以下に更新用のログ ファイルのサンプルを示します。

     ```
     <root container="brownbag1" machine="VM15BS020663" timestamp="07/18/2018 00:11:10" />
     <file item="test.txt" local="False" remote="True" error="16001" />
     <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
     ``` 

3. このファイル内にエラー (サンプルの強調表示されている部分) がある場合は、エラー コードを書き留めておきます。この例では 16001 です。 以下のエラー リファレンスで、このエラー コードの説明を参照します。

    |     エラー コード     |     例外の名前                                         |     エラーの説明                                                                                                                                                                                                                     |
    |--------------------|------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    |    100             |    ERROR_CONTAINER_OR_SHARE_NAME_LENGTH                    |    コンテナーまたは共有の名前は、3 文字から 63 文字で指定する必要があります。                                                                                                                                                                     |
    |    101             |    ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH        |    コンテナーまたは共有の名前に使用できるのは、英字、数字、またはハイフンだけです。                                                                                                                                                       |
    |    102             |    ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH             |    コンテナーまたは共有の名前に使用できるのは、英字、数字、またはハイフンだけです。                                                                                                                                                       |
    |    103             |    ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL               |    BLOB またはファイルの名前に、サポートされていない制御文字が含まれています。                                                                                                                                                                       |
    |    104             |    ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL               |    BLOB またはファイルの名前に、無効な文字が含まれています。                                                                                                                                                                                   |
    |    105             |    ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT                   |    BLOB またはファイルの名前に含まれているセグメントが多すぎます (各セグメントはスラッシュ (/) で区切られています)。                                                                                                                                              |
    |    106             |    ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH                |    BLOB またはファイルの名前が長すぎます。                                                                                                                                                                                                     |
    |    107             |    ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH                |    BLOB またはファイルの名前のセグメントの 1 つが長すぎます。                                                                                                                                                                            |
    |    108             |    ERROR_BLOB_OR_FILE_SIZE_LIMIT                           |    ファイル サイズがアップロードできる最大ファイル サイズを超えています。                                                                                                                                                                              |
    |    109             |    ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT                       |    BLOB またはファイルが正しくアラインされていません。                                                                                                                                                                                               |
    |    110             |    ERROR_NAME_NOT_VALID_UNICODE                            |    Unicode でエンコードされたファイル名または BLOB が無効です。                                                                                                                                                                                  |
    |    111             |    ERROR_RESERVED_NAME_NOT_ALLOWED                         |    ファイルの名前またはプレフィックス、あるいは BLOB の名前またはプレフィックスが、サポートされていない予約名です (たとえば、COM1)。                                                                                                                             |
    |    2000            |    ERROR_ETAG_MISMATCH                                     |    Etag の不一致は、クラウドとデバイスのブロック BLOB の間に競合があることを示します。 この競合を解決するには、これらのファイルの一方 (クラウド内のバージョンか、デバイス上のバージョン) を削除します。    |
    |    2001            |    ERROR_UNEXPECTED_FINALIZE_FAILURE                       |    ファイルがアップロードされた後、ファイルの処理中に予期しない問題が発生しました。    このエラーが表示され、エラーが 24 時間を超えて続く場合は、サポートにお問い合わせください。                                                      |
    |    2002            |    ERROR_ALREADY_OPEN                                      |    ファイルが別のプロセスで既に開かれており、ハンドルが閉じられるまで、アップロードすることはできません。                                                                                                                                       |
    |    2003            |    ERROR_UNABLE_TO_OPEN                                    |    アップロードするファイルを開けませんでした。 このエラーが表示される場合は、Microsoft サポートにお問い合わせください。                                                                                                                                                |
    |    2004            |    ERROR_UNABLE_TO_CONNECT                                 |    データのアップロード先のコンテナーに接続できませんでした。                                                                                                                                                                             |
    |    2005            |    ERROR_INVALID_CLOUD_CREDENTIALS                         |    アカウントのアクセス許可が正しくないか期限切れであるために、コンテナーに接続できませんでした。 アクセスを確認してください。                                                                                                               |
    |    2006            |    ERROR_CLOUD_ACCOUNT_DISABLED                            |    アカウントまたは共有が無効になっているため、アカウントにデータをアップロードできませんでした。                                                                                                                                                            |
    |    2007            |    ERROR_CLOUD_ACCOUNT_PERMISSIONS                         |    アカウントのアクセス許可が正しくないか期限切れであるために、コンテナーに接続できませんでした。 アクセスを確認してください。                                                                                                               |
    |    2008            |    ERROR_CLOUD_CONTAINER_SIZE_LIMIT_REACHED                |    コンテナーがいっぱいであるため、新しいデータを追加できませんでした。 コンテナーの種類ごとのサポートされているサイズについては、Azure の仕様を確認してください。 たとえば、Azure Files では、5 TB の最大ファイル サイズのみがサポートされています。                                     |
    |    2009            |    ERROR_CLOUD_CONTAINER_MISSING                |     共有に関連付けられているコンテナーが存在しないため、データをアップロードできませんでした。                                     |    
    |    2997            |    ERROR_ITEM_CANCELED                                     |    予期しないエラーが発生しました。 これは、自然に解決する一時的なエラーです。                                                                           |
    |    2998            |    ERROR_UNMAPPED_FAILURE                                  |    予期しないエラーが発生しました。 エラーは自動的に解決される可能性がありますが、24 時間たっても解決しない場合は、Microsoft サポートにお問い合わせください。                                                                                                     |
    |    16000           |    RefreshException                                        |    このファイルを停止できませんでした。                                                                                                                                                                                                        |
    |    16001           |    RefreshAlreadyExistsException                           |    このファイルは既にローカル システムに存在するため、停止できませんでした。                                                                                                                                                         |
    |    16002           |    RefreshWorkNeededException                              |    このファイルは完全にアップロードされていないため、更新できませんでした。                                                                                                                                                                          | 


## <a name="next-steps"></a>次のステップ

- [このリリースの既知の問題](data-box-gateway-release-notes.md)について学習します。
