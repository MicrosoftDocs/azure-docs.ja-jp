---
title: クイック スタート:Synapse Studio を使用する
description: このクイックスタートでは、Synapse Studio を使用してさまざまな種類のファイルに対するクエリを簡単に実行する方法について説明します。
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8b35b281f6a70d5485004dc61563271132734a87
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374152"
---
# <a name="quickstart-use-synapse-studio-preview"></a>クイック スタート:Synapse Studio (プレビュー) を使用する

このクイックスタートでは、Synapse Studio を使用してファイルに対するクエリを実行する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="prerequisites"></a>前提条件

[Azure Synapse ワークスペースと、関連するストレージ アカウントを作成します](quickstart-create-workspace.md)。

## <a name="launch-synapse-studio"></a>Synapse Studio を起動する

Azure portal の Azure Synapse ワークスペースで、 **[Launch Synapse Studio]\(Synapse Studio の起動\)** をクリックします。

![Synapse Studio を起動する](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

または、[[Azure Synapse Analytics]](https://web.azuresynapse.net) をクリックし、適切なテナント、サブスクリプション、およびワークスペースの値を指定して Synapse Studio を起動することもできます。

## <a name="browse-storage-accounts"></a>ストレージ アカウントを参照する

Synapse Studio を開いたら、 **[データ]** を参照し、 **[ストレージ アカウント]** を展開して、ワークスペース内のストレージ アカウントを表示します。

![ストレージ上のファイルを参照する](./media/quickstart-synapse-studio/browse-files-on-storage.png)

新しいフォルダーを作成し、ツールバーのリンクを使用してファイルをアップロードして、ファイルを整理することができます。

## <a name="query-files-on-storage-account"></a>ストレージ アカウントのファイルに対してクエリを実行する

> [!IMPORTANT]
> ファイルに対してクエリを実行できるようにするには、基になるストレージの `Storage Blob Reader` ロールのメンバーである必要があります。 [Azure Storage で**ストレージ BLOB データ閲覧者**または**ストレージ BLOB データ共同作成者**の RBAC アクセス許可を割り当てる](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-an-azure-built-in-role)方法について確認してください。

1. いくつかの `PARQUET` ファイルをアップロードします。
2. 1 つ以上のファイルを選択し、新しい SQL スクリプトまたは Spark ノートブックを作成して、ファイルの内容を確認します。 ノートブックを作成する場合は、[Synapse ワークスペースに Apache Spark プール](quickstart-create-apache-spark-pool-studio.md)を作成する必要があります。

   ![ストレージ上のファイルに対してクエリを実行する](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. 生成されたクエリまたはノートブックを実行して、ファイルの内容を確認します。

   ![ファイルの内容を確認する](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. クエリを変更して、結果のフィルター処理と並べ替えを行うことができます。 SQL オンデマンドで利用可能な言語機能については、[SQL 機能の概要](sql/overview-features.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Storage で**ストレージ BLOB データ閲覧者**または**ストレージ BLOB データ共同作成者**の RBAC アクセス許可を割り当てる](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-an-azure-built-in-role)ことにより、Azure AD ユーザーがファイルに対してクエリを実行できるようにする
- [SQL オンデマンドを使用して、Azure Storage 上のファイルに対してクエリを実行する](sql/on-demand-workspace-overview.md)
- [Azure portal を使用して Apache Spark プールを作成する](quickstart-create-apache-spark-pool-portal.md)
- [Azure Storage に格納されているファイルについて Power BI レポートを作成する](sql/tutorial-connect-power-bi-desktop.md)
