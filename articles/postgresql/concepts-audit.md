---
title: Azure Database for PostgreSQL - Single Server で pgAudit を使用した監査ログ
description: Azure Database for PostgreSQL - Single Server での pgAudit 監査ログの概念。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 8b4cbe309e310ef1fc384224c952a6f04385b1dd
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092468"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server の監査ログ

Azure Database for PostgreSQL - Single Server でのデータベース アクティビティの監査ログは、PostgreSQL の監査拡張機能 [pgAudit](https://www.pgaudit.org/) を使用して入手できます。 pgAudit は、セッションまたはオブジェクトの詳細な監査ログを提供します。

> [!NOTE]
> pgAudit は、汎用サーバーとメモリ最適化サーバー上でのみ有効にすることができます。

## <a name="usage-considerations"></a>使用に関する考慮事項
既定では、pgAudit ログ ステートメントは、Postgres の標準ログ記録機能を使用して、通常のログ ステートメントと共に出力されます。 Azure Database for PostgreSQL では、これらの .log ファイルを Azure portal または CLI を介してダウンロードできます。 ファイルのコレクション用の最大ストレージ容量は 1 GB で、各ファイルは最大 7 日間 (既定では 3 日間) 使用できます。 このサービスは、短期的なストレージ オプションです。

または、すべてのログが Azure Monitor の診断ログ サービスに出力されるよう構成することもできます。 Azure Monitor の診断ログを有効にすると、選択した内容に応じて、ログが (JSON 形式で) Azure Storage、Event Hubs、または Azure Monitor のログに自動的に送信されます。

pgAudit を有効にすると、サーバー上に大量のログ記録が生成され、パフォーマンスとログ ストレージに影響を与えます。 長期的なストレージ オプションのほか、分析やアラートの機能も提供する、Azure 診断ログ サービスを使用することをお勧めします。 追加のログ記録によるパフォーマンスへの影響を軽減するために、標準のログ記録を無効にすることをお勧めします。

   1. パラメーター `logging_collector` を OFF に設定します。 
   2. この変更を適用するには、サーバーを再起動します。

Azure Storage、Event Hubs、または Azure Monitor のログへのログ記録を設定する方法については、[サーバー ログに関する記事](concepts-server-logs.md)の診断ログのセクションを参照してください。

## <a name="installing-pgaudit"></a>pgAudit のインストール

pgAudit をインストールするには、それをサーバーの共有プリロード ライブラリに含める必要があります。 Postgres の `shared_preload_libraries` パラメーターへの変更を有効にするには、サーバーの再起動が必要です。 パラメーターを変更するには、[Azure portal](howto-configure-server-parameters-using-portal.md)、[Azure CLI](howto-configure-server-parameters-using-cli.md)、または [REST API](/rest/api/postgresql/configurations/createorupdate) を使用できます。

[Azure portal](https://portal.azure.com) を使用して以下を実行します。

   1. Azure Database for PostgreSQL サーバーを選択します。
   2. サイドバーから、 **[サーバー パラメーター]** を選択します。
   3. `shared_preload_libraries` パラメーターを検索します。
   4. **[pgaudit]** を選択します。
   5. サーバーを再起動して変更を適用します。

   6. クライアント (psql など) を使用してサーバーに接続し、pgAudit 拡張機能を有効にします。
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> エラーが表示される場合は、`shared_preload_libraries` を保存した後にサーバーを再起動したことを確認してください。

## <a name="pgaudit-settings"></a>pgAudit の設定

pgAudit では、セッションまたはオブジェクトの監査ログを構成できます。 [セッション監査ログ](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)では、実行されたステートメントの詳細なログが出力されます。 [オブジェクト監査ログ](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)は、特定の関係だけを対象にした監査です。 設定するログ記録の種類を 1 つにするか両方にするか選択できます。 

> [!NOTE]
> pgAudit 設定はグローバルに指定され、データベース レベルまたはロール レベルでは指定できません。

[pgAudit のインストール](#installing-pgaudit)が完了したら、ログ記録を開始するようそのパラメーターを構成できます。 [pgAudit のドキュメント](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)には、各パラメーターの定義が記載されています。 まずパラメーターをテストし、期待どおりに動作することを確認します。

> [!NOTE]
> `pgaudit.log_client` を ON に設定すると、ログはファイルに書き込まれるのではなく、クライアント プロセス (psql など) にリダイレクトされます。 通常、この設定は無効のままにしておく必要があります。

> [!NOTE]
> `pgaudit.log_level` が有効になるのは、`pgaudit.log_client` がオンになっている場合のみです。 また、Azure portal では現在、`pgaudit.log_level` にバグがあります。コンボ ボックスが表示され、複数のレベルを選択できることが示されます。 ただし、選択するレベルは 1 つだけにしてください。 

> [!NOTE]
> Azure Database for PostgreSQL では、pgAudit のドキュメントで説明されているように、`pgaudit.log` を `-` (マイナス) 記号のショートカットを使用して設定することはできません。 必要なステートメント クラス (READ、WRITE など) はすべて、個別に指定する必要があります。

### <a name="audit-log-format"></a>監査ログの形式
各監査エントリは、ログ行の先頭付近の `AUDIT:` によって示されます。 エントリの残りの部分の形式については、[pgAudit のドキュメント](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)で詳しく説明されています。

実際の監査要件を満たすためにその他のフィールドが必要な場合は、Postgres パラメーター `log_line_prefix` を使用します。 `log_line_prefix` は、各 Postgres ログ行の先頭に出力される文字列です。 たとえば、次の `log_line_prefix` 設定では、タイムスタンプ、ユーザー名、データベース名、およびプロセス ID が提供されます。

```
t=%m u=%u db=%d pid=[%p]:
```

`log_line_prefix` の詳細を確認するには、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)にアクセスしてください。

### <a name="getting-started"></a>使用の開始
すぐに作業を開始するには、`pgaudit.log` を `WRITE` に設定し、ログを開いて出力を確認します。 


## <a name="next-steps"></a>次の手順
- [Azure Database for PostgreSQL でのログ記録について学習する](concepts-server-logs.md)
- [Azure portal](howto-configure-server-parameters-using-portal.md)、[Azure CLI](howto-configure-server-parameters-using-cli.md)、または [REST API](/rest/api/postgresql/configurations/createorupdate) を使用してパラメーターを設定する方法を学習する
