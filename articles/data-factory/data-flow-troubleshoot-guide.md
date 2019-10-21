---
title: Azure Data Factory データ フローのトラブルシューティング | Microsoft Docs
description: Azure Data Factory でのデータ フローに関する問題のトラブルシューティングを行う方法について説明します。
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: b5895b061426066d265d3ff68dc948014e641322
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242269"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Azure Data Factory データ フローのトラブルシューティング

この記事では、Azure Data Factory のデータ フローの一般的なトラブルシューティング方法について説明します。

## <a name="common-errors-and-messages"></a>一般的なエラーとメッセージ

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>エラー メッセージ:DF-SYS-01: shaded.databricks.org.apache.hadoop.fs.azure.AzureException: com.microsoft.azure.storage.StorageException:指定されたコンテナーが存在しません。

- **現象**:コンテナーが存在しないため、データ プレビュー、デバッグ、パイプライン データ フローの実行が失敗する

- **原因**:ストレージに存在しないコンテナーがデータセットに含まれている場合

- **解決方法**:データセットで参照しているコンテナーが存在することを確認する

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>エラー メッセージ:DF-SYS-01: java.lang.AssertionError: アサーションに失敗しました:ディレクトリ構造の競合が検出されました。 疑わしいパス

- **現象**:Parquet ファイルでソースを変換するときにワイルドカードを使用した場合

- **原因**:ワイルドカード構文が正しくないか、無効になっている

- **解決方法**:ソース変換オプションで使用しているワイルドカード構文を確認する

### <a name="error-message-df-src-002-container-container-name-is-required"></a>エラー メッセージ:DF-SRC-002: 'コンテナー' (コンテナー名) が必須です

- **現象**:コンテナーが存在しないため、データ プレビュー、デバッグ、パイプライン データ フローの実行が失敗する

- **原因**:ストレージに存在しないコンテナーがデータセットに含まれている場合

- **解決方法**:データセットで参照しているコンテナーが存在することを確認する

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>エラー メッセージ:DF-UNI-001:PrimaryKeyValue の IntegerType 型と StringType 型に互換性がありません

- **現象**:コンテナーが存在しないため、データ プレビュー、デバッグ、パイプライン データ フローの実行が失敗する

- **原因**:データベース シンクに間違った主キー型を挿入すると発生する

- **解決方法**:データ フローの主キーに使用している列を派生列でキャストし、ターゲット データベースのデータ型に合わせる

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>エラー メッセージ:DF-SYS-01: com.microsoft.sqlserver.jdbc.SQLServerException:ホスト xxxxx.database.windows.net ポート 1433 に TCP/IP 接続できませんでした。 エラー: "xxxx.database.windows.net。 接続プロパティを確認してください。 SQL Server のインスタンスがホストで実行されており、ポートで TCP/IP 接続を受け入れることを確認してください。 ポートへの TCP 接続がファイアウォールでブロックされていないことを確認してください。"

- **現象**:データベース ソースまたはシンクでデータをプレビューできず、パイプラインを実行できない

- **原因**:データベースがファイアウォールで保護されている

- **解決方法**:データベースに対してファイアウォール アクセスを開く

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>エラー メッセージ:DF-SYS-01: com.microsoft.sqlserver.jdbc.SQLServerException:データベースに 'xxxxxx' という名前のオブジェクトが既に存在します。

- **現象**:シンクでテーブルを作成できない

- **原因**:ソースまたはデータセットで定義されているものと同じ名前を持つテーブル名がターゲット データベースに既に存在する

- **解決方法**:これから作成するテーブルの名前を変更する

## <a name="general-troubleshooting-guidance"></a>一般的なトラブルシューティング ガイダンス

1. データセット接続の状態を確認します。 ソース/シンクの変換ごとに、使用している各データベースのリンクされたサービスにアクセスし、接続をテストします。
2. データ フロー デザイナーからファイルとテーブルの接続の状態を確認します。 デバッグをオンに切り替え、ソース変換のデータ プレビューをクリックし、データにアクセスできることを確認します。
3. データ プレビューに問題がなければ、パイプライン デザイナーに進み、パイプライン アクティビティにデータ フローを配置します。 エンドツーエンド テストとしてパイプラインをデバッグします。

## <a name="next-steps"></a>次の手順

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
*  [ADF マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)
