---
title: Azure HDInsight での Apache Oozie のトラブルシューティング
description: Azure HDInsight での特定の Apache Oozie エラーを解決します。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 2cdd20a5d639f74916657edc3f73183a403204a5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944667"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Azure HDInsight での Apache Oozie のトラブルシューティング

Apache Oozie UI では、Oozie のログを見ることができます。 また、Oozie UI には、ワークフローによって開始された MapReduce タスクの JobTracker ログへのリンクも含まれます。 トラブルシューティングのパターンは次のようになります。

1. Oozie Web UI でジョブを表示します。

2. 特定のアクションでエラーまたは障害が発生した場合は、そのアクションを選択して、 **[Error Message]** フィールドにエラーの詳細情報が示されているかどうかを確認します。

3. 可能な場合は、アクションから URL を使って、アクションの詳細 (JobTracker ログなど) を表示します。

発生する可能性のある具体的なエラーとその解決方法を次に示します。

## <a name="ja009-cant-initialize-cluster"></a>JA009:クラスターを初期化できません

### <a name="issue"></a>問題

ジョブの状態が **SUSPENDED** に変更されます。 ジョブの詳細に、`RunHiveScript` の状態が **START_MANUAL** と示されます。 アクションを選択すると、次のエラー メッセージが表示されます。

```output
JA009: Cannot initialize Cluster. Please check your configuration for map
```

### <a name="cause"></a>原因

**job.xml** ファイルで使われている Azure Blob Storage アドレスに、ストレージ コンテナー名またはストレージ アカウント名が含まれていません。 Blob Storage アドレスは、`wasbs://containername@storageaccountname.blob.core.windows.net` という形式にする必要があります。

### <a name="resolution"></a>解像度

ジョブが使う Blob Storage アドレスを変更します。

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002:Oozie では &lt;USER&gt; の偽装が許可されていません

### <a name="issue"></a>問題

ジョブの状態が **SUSPENDED** に変更されます。 ジョブの詳細に、`RunHiveScript` の状態が **START_MANUAL** と示されます。 アクションを選ぶと、次のエラー メッセージが表示されます。

```output
JA002: User: oozie is not allowed to impersonate <USER>
```

### <a name="cause"></a>原因

現在のアクセス許可設定で、Oozie が指定されたユーザー アカウントを偽装することを許可していません。

### <a name="resolution"></a>解像度

Oozie では、 **`users`** グループ内のユーザーを偽装できます。 `groups USERNAME` を使用して、ユーザー アカウントがメンバーとして属するグループを確認します。 ユーザーが **`users`** グループのメンバーでない場合は、次のコマンドを使用して、ユーザーをグループに追加します。

```bash
sudo adduser USERNAME users
```

> [!NOTE]  
> ユーザーがグループに追加されたことを HDInsight が認識するまで数分かかる場合があります。

---

## <a name="launcher-error-sqoop"></a>Launcher ERROR (ランチャー エラー) (Sqoop)

### <a name="issue"></a>問題

ジョブの状態が **KILLED** に変更されます。 ジョブの詳細に、`RunSqoopExport` の状態が **ERROR** と示されます。 アクションを選ぶと、次のエラー メッセージが表示されます。

```output
Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]
```

### <a name="cause"></a>原因

Sqoop が、データベースにアクセスするために必要なデータベース ドライバーを読み込むことができません。

### <a name="resolution"></a>解像度

Oozie ジョブから Sqoop を使うときは、ジョブが使う他のリソース (workflow.xml など) とともにデータベース ドライバーを含める必要があります。 また、workflow.xml の `<sqoop>...</sqoop>` セクションから、データベース ドライバーが格納されたアーカイブを参照します。

たとえば、[Hadoop Oozie ワークフローの使用](hdinsight-use-oozie-linux-mac.md)に関するページのジョブ例では、次の手順を使用します。

1. `mssql-jdbc-7.0.0.jre8.jar` ファイルを **/tutorials/useoozie** ディレクトリにコピーします。

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. `workflow.xml` を変更して、`</sqoop>` の上の新しい行に次の XML を追加します。

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]