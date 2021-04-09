---
title: Azure SQL Edge の使用状況と診断データの構成
description: Azure SQL Edge で使用状況と診断データを構成する方法について説明します。
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 08/04/2020
ms.openlocfilehash: 81b2aabbae148faec06c9ab420bdfecde475b4bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97605004"
---
# <a name="azure-sql-edge-usage-and-diagnostics-data-configuration"></a>Azure SQL Edge の使用状況と診断データの構成

Azure SQL Edge では既定で、お客様のアプリケーションの使用状態に関する情報が収集されます。 具体的には、Azure SQL Edge により、展開エクスペリエンス、使用状況、パフォーマンスに関する情報が収集されます。 この情報は、Microsoft が製品の向上を図り、お客様のニーズをさらに満たすのに役立ちます。 たとえば Microsoft では、お客様が受け取るエラー コードの種類に関する情報を収集して、関連するバグの修正、Azure SQL Edge の使用方法に関するドキュメントの改善、より良いサービスのために製品に機能を追加すべきかどうかの判断を行います。

具体的には、Microsoft はこのメカニズムでは次の種類の情報は送信しません。

- ユーザー テーブル内からのすべての値。
- すべてのログオン資格情報またはその他の認証情報。
- 個人データまたは顧客データ。

次のサンプル シナリオには、製品の向上に役立つ、機能の利用状況の情報が含まれます。

使用状況と診断データの収集に使用されるクエリの例を以下に示します。 このクエリでは、Azure SQL Edge で使用されているさまざまなストリーミング データ ソースの数と種類が明らかになります。 このデータは、Microsoft がこれらのデータ ソースに関連するパフォーマンスとユーザー エクスペリエンスを向上させることができるように、よく使用されているストリーミング データ ソースを特定するのに役立ちます。 

```sql
select 
count(*) as [count], sum(inputs) as inputs, sum(outputs) as outputs, sum(linked_to_job) 
as linked_to_job, data_source_type
from ( 
select isnull(value,'unknown') as data_source_type, inputs, outputs, linked_to_job
from 
    ( 
        select 
        convert(sysname, lower(substring(ds.location, 0, charindex('://', ds.location))), 1) as data_source_type, 
        isnull(inputs, 0) as inputs, isnull(outputs, 0) as outputs, isnull(js.stream_id/js.stream_id, 0) as linked_to_job 
        from sys.external_streams es
        join sys.external_data_sources ds 
             on es.data_source_id = ds.data_source_id
        left join 
            ( 
            select stream_id, max(cast(is_input as int)) inputs, max(cast(is_output as int)) outputs 
            from sys.external_job_streams group by stream_id 
            ) js
             on js.stream_id = es.object_id 
    ) ds
left join 
    (
        select value from string_split('edgehub,sqlserver,kafka', ',')) as known_ep on data_source_type = value 
    ) known_ds
group by data_source_type
```

## <a name="disable-usage-and-diagnostic-data-collection"></a>使用状況と診断データの収集を無効にする

Azure SQL Edge での使用状況と診断データの収集は、次のいずれかの方法を使用して無効にできます。

> [!NOTE]
> 使用状況と診断データは、Developer バージョンでは無効にできません。

### <a name="disable-usage-and-diagnostics-using-environment-variables"></a>環境変数を使用して使用状況と診断を無効にする

Azure SQL Edge での使用状況と診断データの収集を無効にするには、次の環境変数を追加し、その値を `*False*` に設定します。 環境変数を使用した Azure SQL Edge の構成の詳細については、[環境変数を使用した構成](configure.md#configure-by-using-environment-variables)に関するページを参照してください。

`MSSQL_TELEMETRY_ENABLED = TRUE | FALSE`

- TRUE - 使用状況と診断データの収集を有効にします。 これは既定の構成です。
- FALSE - 使用状況と診断データの収集を無効にします。

### <a name="disable-usage-and-diagnostics-using-mssqlconf-file"></a>mssql.conf ファイルを使用して使用状況と診断を無効にする

Azure SQL Edge での使用状況と診断データの収集を無効にするには、SQL Edge モジュールの /var/opt/mssql/ フォルダーにマップされる永続ストレージ ドライブの mssql.conf ファイルに次の行を追加します。 mssql.conf ファイルを使用した Azure SQL Edge の構成の詳細については、「[mssql.conf ファイルを使用して構成する](configure.md#configure-by-using-an-mssqlconf-file)」を参照してください。

```ini
[telemetry]
customerfeedback = false
```

## <a name="local-audit-of-usage-and-diagnostic-data-collection"></a>使用状況と診断データの収集のローカル監査

Azure SQL Edge の使用状況と診断データの収集のローカル監査コンポーネントでは、サービスによって収集されたデータを指定したフォルダーに書き込むことができます。このデータは、Microsoft に送信されるデータ (ログ) です。 Local Audit の目的は、Microsoft がこの機能で収集するすべてのデータをユーザーがコンプライアンス、法規制、またはプライバシーの検証目的で確認できるようにすることです。

### <a name="enable-local-audit-of-usage-and-diagnostics-data"></a>使用状況と診断データのローカル監査を有効にする

Azure SQL Edge での使用状況と診断データのローカル監査を有効にするには

1. 新しいローカル監査ログ ストレージ用のターゲット ディレクトリを作成します。 このターゲット ディレクトリは、ホスト上またはコンテナー内のどちらにあってもかまいません。 次の例では、SQL Edge の /var/opt/mssql/ パスにマップされるのと同じマウント ボリューム内にターゲット ディレクトリが作成されます。

   ```bash
   sudo mkdir <host mount path>/audit
   ```

2. 環境変数または mssql.conf ファイルを使用して、使用状況と診断データの監査を構成します。

   - 環境変数の使用 - 次の環境変数を SQL Edge の展開に追加し、監査ファイルのターゲット ディレクトリを指定します。
   
     `*MSSQL_TELEMETRY_DIR = <host mount path>/audit*`
   
   - mssql.conf ファイルの使用 - mssql.conf ファイルに次の行を追加し、監査ファイルのターゲット ディレクトリを指定します。
       ```ini
       [telemetry]
       userrequestedlocalauditdirectory  = <host mount path>/audit
       ```  

## <a name="next-steps"></a>次のステップ

- [Azure SQL Edge に接続する](connect.md)
- [SQL Edge を使用してエンドツーエンドの IoT ソリューションを構築する](tutorial-deploy-azure-resources.md)
