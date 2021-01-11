---
title: Synapse ワークスペース (プレビュー) のセキュリティ保護
description: この記事では、ロールとアクセス制御を使用して、Synapse ワークスペースでのアクティビティとデータへのアクセスを制御する方法について説明します。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 6b1fe78ac00b57d7627a64970e84ae8b717ae5c3
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501918"
---
# <a name="secure-your-synapse-workspace-preview"></a>Synapse ワークスペース (プレビュー) のセキュリティ保護

この記事では、ロールとアクセス制御を使用して、アクティビティとデータへのアクセスを制御する方法について説明します。 次の手順に従うと、Azure Synapse Analytics でのアクセス制御が簡単になります。 3 つのセキュリティ グループのいずれかでユーザーを追加するか、削除する必要があるだけです。

## <a name="overview"></a>概要

Synapse ワークスペース (プレビュー) をセキュリティ保護するには、次の項目を構成するパターンに従います。

- Azure ロール (所有者、共同作成者などの組み込みのロールなど)
- Synapse ロール - これらのロールは Synapse に固有であり、Azure ロールに基づいていません。 次の 3 つのロールがあります。
  - Synapse ワークスペース管理者
  - Synapse SQL 管理者
  - Apache Spark for Azure Synapse Analytics 管理者
- Azure Data Lake Storage Gen 2 (ADLSGEN2) でのデータのアクセス制御。
- Synapse SQL および Spark データベースのアクセス制御

## <a name="steps-to-secure-a-synapse-workspace"></a>Synapse ワークスペースをセキュリティ保護する手順

このドキュメントでは、手順を簡素化するために、標準名を使用しています。 それらを任意の名前に置き換えてください。

|設定 | 値の例 | 説明 |
| :------ | :-------------- | :---------- |
| **Synapse ワークスペース** | WS1 |  Synapse ワークスペースに使用される名前。 |
| **ADLSGEN2 アカウント** | STG1 | ワークスペースで使用する ADLS アカウント。 |
| **コンテナー** | CNT1 | ワークスペースで既定で使用される STG1 内のコンテナー。 |
| **Active Directory テナント** | contoso | Active Directory テナント名。|
||||

## <a name="step-1-set-up-security-groups"></a>手順 1: セキュリティ グループを設定する

ワークスペースに 3 つのセキュリティ グループを作成して設定します。

- **WS1\_WSAdmins** - ワークスペースを完全に制御する必要があるユーザー用
- **WS1\_SparkAdmins** - ワークスペースの Spark の側面を完全に制御する必要があるユーザー用
- **WS1\_SQLAdmins** - ワークスペースの SQL の側面を完全に制御する必要があるユーザー用
- **WS1\_WSAdmins** を **WS1\_SQLAdmins** に追加する
- **WS1\_WSAdmins** を **WS1\_SparkAdmins** に追加する

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>手順 2: Data Lake Storage Gen2 アカウントを準備する

ストレージに関する次の情報を特定します。

- ワークスペースに使用する ADLSGEN2 アカウント。 このドキュメントでは、それを STG1 と呼びます。  STG1 は、ワークスペースの "プライマリ" ストレージ アカウントと見なされます。
- Synapse ワークスペースで既定で使用される WS1 内のコンテナー。 このドキュメントでは、それを CNT1 と呼びます。  このコンテナーは、次のために使用されます。
  - Spark テーブル用のバッキング データ ファイルの格納
  - Spark ジョブの実行ログ

- Azure portal を使用して、セキュリティ グループに、CNT1 に対する次のロールを割り当てます

  - **WS1\_WSAdmins** に **Storage BLOB データ共同作成者**ロールを割り当てます
  - **WS1\_SparkAdmins** に **Storage BLOB データ共同作成者**ロールを割り当てます。
  - **WS1\_SQLAdmins** に **Storage BLOB データ共同作成者**ロールを割り当てます

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>手順 3:Synapse ワークスペースを作成して構成する

Azure portal で、Synapse ワークスペースを作成します。

- ワークスペースに WS1 という名前を指定します
- ストレージ アカウントとして STG1 を選択します
- "Filesystem" として使用されているコンテナーに CNT1 を選択します。
- Synapse Studio で WS1 を開きます
- **[管理]**  >  **[アクセス制御]** を選択して、セキュリティ グループに次の Synapse ロールを割り当てます。
  - **WS1\_WSAdmins** に Synapse ワークスペース管理者を割り当てます
  - **WS1\_SparkAdmins** に Synapse Spark 管理者を割り当てます
  - **WS1\_SQLAdmins** に Synapse SQL 管理者を割り当てます

## <a name="step-4-configure-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>手順 4:Synapse ワークスペースで使用するための Data Lake Storage Gen2 を構成する

Synapse ワークスペースでは、パイプラインを実行してシステム タスクを実行できるように、STG1 と CNT1 にアクセスする必要があります。

- Azure ポータルを開きます
- STG1 を見つけます
- CNT1 に移動します
- 確実に、WS1 の MSI (マネージド サービス ID) に、CNT1 に対する**ストレージ BLOB データ共同作成者**ロールが割り当てられるようにします
  - 割り当てられていることが確認できない場合は、割り当てます。
  - MSI には、ワークスペースと同じ名前が付けられます。 このケースでは、&quot;WS1&quot; になります。

## <a name="step-5-configure-admin-access-for-sql-pools"></a>手順 5:SQL プールの管理者アクセスを構成する

- Azure ポータルを開きます
- WS1 に移動します
- **[設定]** で、 **[SQL Active Directory 管理者]** をクリックします
- **[管理者の設定]** をクリックし、WS1\_SQLAdmins を選択します

## <a name="step-6-maintain-access-control"></a>手順 6:アクセス制御を維持する

構成が完了しました。

これで、ユーザーのアクセスを管理するために、3 つのセキュリティ グループに対してユーザーを追加および削除できるようになります。

ユーザーに Synapse ロールを手動で割り当てることもできますが、そうすると、構成の整合性が取れなくなります。 代わりに、セキュリティ グループに対してユーザーを追加または削除するだけにしてください。

## <a name="step-7-verify-access-for-users-in-the-roles"></a>手順 7:ロールでのユーザーのアクセスを確認する

各ロールのユーザーは、次の手順を実行する必要があります。

| Number | 手順 | ワークスペース管理者 | Spark 管理者 | SQL 管理者 |
| --- | --- | --- | --- | --- |
| 1 | Parquet ファイルを CNT1 にアップロードする | YES | YES | YES |
| 2 | 必要に応じて SQL を使用して Parquet ファイルを読み取る | YES | NO | YES |
| 3 | Spark プールを作成する | YES [1] | YES [1] | NO  |
| 4 | Notebook を使用して Parquet ファイルを読み取る | YES | YES | NO |
| 5 | Notebook からパイプラインを作成し、すぐに実行するパイプラインをトリガーする | YES | NO | NO |
| 6 | SQL プールを作成し、&quot;SELECT 1&quot; などの SQL スクリプトを実行する | YES [1] | NO | YES[1] |

> [!NOTE]
> [1] SQL または Spark プールを作成するために、ユーザーは Synapse ワークスペースに対して少なくとも共同作成者ロールを持っている必要があります。
> [!TIP]
>
> - ロールによっては、一部の手順が意図的に許可されません。
> - セキュリティが完全に構成されていない場合、一部のタスクが失敗する可能性があることに注意してください。 これらのタスクについては、表に記載されています。

## <a name="step-8-network-security"></a>手順 8:ネットワークのセキュリティ

ワークスペース ファイアウォール、仮想ネットワーク、および [Private Link](../../azure-sql/database/private-endpoint-overview.md) を構成します。

## <a name="step-9-completion"></a>手順 9:Completion

これで、ワークスペースが完全に構成され、セキュリティ保護されました。

## <a name="how-roles-interact-with-synapse-studio"></a>ロールと Synapse Studio の相互作用

Synapse Studio は、ユーザー ロールに基づいて動作が異なります。 適切なアクセス権を付与するロールにユーザーが割り当てられていない場合、一部の項目が非表示または無効にされることがあります。 次の表に、Synapse Studio が受ける影響をまとめています。

| タスク | ワークスペース管理者 | Spark 管理者 | SQL 管理者 |
| --- | --- | --- | --- |
| Synapse Studio を開く | YES | YES | YES |
| ホーム ハブの表示 | YES | YES | YES |
| データ ハブの表示 | YES | YES | YES |
| データ ハブ/リンクされた ADLS Gen2 アカウントとコンテナーを参照 | YES [1] | YES[1] | YES[1] |
| データ ハブ/データベースを参照 | YES | YES | YES |
| データ ハブ/データベース内のオブジェクトを参照 | YES | YES | YES |
| データ ハブ/SQL プール内のデータを参照 | YES   | NO   | YES   |
| データ ハブ/SQL オンデマンド データベース内のデータへのアクセス | YES [2]  | NO  | YES [2]  |
| データ ハブ/Spark データベース内のデータへのアクセス | YES [2] | YES [2] | YES [2] |
| 開発ハブの使用 | YES | YES | YES |
| 開発ハブ/SQL スクリプトの作成 | YES | NO | YES |
| 開発ハブ/Spark ジョブ定義の作成 | YES | YES | NO |
| 開発ハブ/ノートブックの作成 | YES | YES | NO |
| 開発ハブ/データフローの作成 | YES | NO | NO |
| 調整ハブの使用 | YES | YES | YES |
| 調整ハブ/パイプラインの使用 | YES | NO | NO |
| 管理ハブの使用 | YES | YES | YES |
| 管理ハブ/SQL プール | YES | NO | YES |
| 管理ハブ/Spark プール | YES | YES | NO |
| 管理ハブ/トリガー | YES | NO | NO |
| 管理ハブ/リンクされたサービス | YES | YES | YES |
| 管理ハブ/アクセス制御 (ユーザーに Synapse ワークスペース ロールを割り当てる) | YES | NO | NO |
| 管理ハブ/統合ランタイム | YES | YES | YES |
| 監視ハブを使用する | YES | YES | YES |
| 監視ハブ/オーケストレーション/パイプラインの実行  | YES | NO | NO |
| 監視ハブ/オーケストレーション/トリガーの実行  | YES | NO | NO |
| 監視ハブ/オーケストレーション/統合ランタイム  | YES | YES | YES |
| 監視ハブ/アクティビティ/Spark アプリケーション | YES | YES | NO  |
| 監視ハブ/アクティビティ/SQL 要求 | YES | NO | YES |
| 監視ハブ/アクティビティ/Spark プール | YES | YES | NO  |
| 監視ハブ/トリガー | YES | NO | NO |
| 管理ハブ/リンクされたサービス | YES | YES | YES |
| 管理ハブ/アクセス制御 (ユーザーに Synapse ワークスペース ロールを割り当てる) | YES | NO | NO |
| 管理ハブ/統合ランタイム | YES | YES | YES |


> [!NOTE]
> [1] コンテナー内のデータへのアクセスは ADLS Gen2 のアクセス制御に依存します。 </br>
> [2] SQL OD テーブルと Spark テーブルでは ADLS Gen2 にデータが格納され、アクセスには ADLS Gen2 に対する適切なアクセス許可が必要です。

## <a name="next-steps"></a>次のステップ

[Synapse ワークスペース](../quickstart-create-workspace.md)を作成する
