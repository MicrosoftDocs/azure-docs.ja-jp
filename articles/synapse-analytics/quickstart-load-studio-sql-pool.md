---
title: 'クイックスタート: 専用 SQL プールを使用したデータの一括読み込み'
description: Synapse Studio を使用して、Azure Synapse Analytics の専用 SQL プールにデータを一括読み込みします。
services: synapse-analytics
author: gaursa
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.openlocfilehash: be15a37a9a2965da36f7e8f884a0a3112106b9ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586728"
---
# <a name="quickstart-bulk-loading-with-synapse-studio"></a>クイックスタート: Synapse Studio を使用した一括読み込み

Synapse Studio の一括読み込みウィザードを使用すると、データを簡単に読み込むことができます。 Synapse Studio は Azure Synapse Analytics の機能です。 一括読み込みウィザードの手順に従うと、[COPY ステートメント](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)を使用してデータを一括で専用 SQL プールに読み込むための T-SQL スクリプトを作成できます。 

## <a name="entry-points-to-the-bulk-load-wizard"></a>一括読み込みウィザードへのエントリ ポイント

データを一括読み込みするには、Synapse Studio 内の以下の領域 (ご使用のワークスペースにアタッチされている Azure ストレージ アカウントのファイルまたはフォルダー) を右クリックします。

![ストレージ アカウントのファイルまたはフォルダーを右クリックした画面のスクリーンショット。](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>[前提条件]

- このウィザードでは、Azure Active Directory (Azure AD) パススルーを認証に使用する COPY ステートメントが生成されます。 少なくとも Azure Data Lake Storage Gen2 アカウントに対するストレージ BLOB データ共同作成者 Azure ロールがある状態で、ワークスペースに [Azure AD ユーザーがアクセスできる必要があります](./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication)。 

- [COPY ステートメントを使用するために必要なアクセス許可](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions)が必要です。さらに、読み込み先のテーブルを新しく作成する場合は、テーブルの作成アクセス許可が必要です。

- Data Lake Storage Gen2 アカウントに関連付けられているリンクされたサービスには、読み込む "*ファイルまたはフォルダーへのアクセス権が必要*" です。 たとえば、リンクされたサービスの認証メカニズムがマネージド ID の場合、ワークスペースのマネージド ID には、ストレージ アカウントに対するストレージ BLOB データ閲覧者アクセス許可が少なくとも必要です。

- お使いのワークスペースで仮想ネットワークが有効になっている場合は、ソース データとエラー ファイルの場所に対して Data Lake Storage Gen2 アカウントのリンクされたサービスに関連付けられている統合ランタイムで、インタラクティブな作成が有効になっていることを確認してください。 インタラクティブな作成は、ウィザード内での自動スキーマ検出、ソース ファイルの内容のプレビュー、および Data Lake Storage Gen2 ストレージ アカウントの参照に必要です。

## <a name="steps"></a>手順

1. **[Source storage location]\(ソース ストレージの場所\)** パネルで、ストレージ アカウントと、読み込み元のファイルまたはフォルダーを選択します。 ファイルのソース フィールドをターゲットの適切な SQL データ型にマッピングするなど、Parquet ファイルおよび区切りテキスト (CSV) ファイルの検出がウィザードによって自動的に試行されます。 

   ![ソースの場所を選択する画面のスクリーンショット。](./sql/media/bulk-load/bulk-load-source-location.png)

2. 一括読み込み処理中に拒否された行が存在する場合のエラー設定など、ファイル形式の設定を選択します。 また、 **[データのプレビュー]** を選択すると、COPY ステートメントによってファイルがどのように解析されるかを確認できるため、ファイル形式設定を構成する際の参考にすることができます。 ファイル形式設定を変更するたびに **[データのプレビュー]** を選択して、更新された設定で COPY ステートメントによってファイルがどのように解析されるかを確認します。

   ![データのプレビューを示すスクリーンショット。](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

   > [!NOTE]  
   >
   > - 複数文字のフィールド ターミネータを使用したデータのプレビューは、一括読み込みウィザードではサポートされません。 複数文字のフィールド ターミネータを指定した場合、データが 1 列内でプレビューされます。 
   > - **[列名の推論]** を選択すると、 **[先頭行]** フィールドによって指定された先頭行から、一括読み込みウィザードによって列名が解析されます。 このヘッダー行を無視するために、COPY ステートメントの `FIRSTROW` の値が 1 行分、自動的にインクリメントされます。 
   > - COPY ステートメントでは、複数文字の行ターミネータを指定できます。 ただし一括読み込みウィザードではサポートされません。指定した場合はエラーがスローされます。

3. 読み込みに使用する専用 SQL プールを選択します (既存のテーブルへの読み込みか、新しいテーブルへの読み込みか)。
   ![ターゲットの場所を選択する画面のスクリーンショット。](./sql/media/bulk-load/bulk-load-target-location.png)
4. **[列マッピングの構成]** を選択して、列マッピングが適切であることを確認します。 **[Infer column names]\(列名の推測\)** を有効にした場合は、列名が自動的に検出されることに注意してください。 新しいテーブルの場合、ターゲット列のデータ型を更新するには、列マッピングを構成することが非常に重要です。

   ![列マッピングの構成を示すスクリーンショット。](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. **[スクリプトを開く]** を選択します。 データ レイクからの読み込みを実行する COPY ステートメントを含んだ T-SQL スクリプトが生成されます。
   ![SQL スクリプトが表示されている画面のスクリーンショット。](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>次のステップ

- [COPY ステートメント](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)に関する記事を参照して COPY 機能の詳細を確認する。
- [データ読み込みの概要](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt)に関する記事で、ETL (抽出、変換、読み込み) プロセスの使用について確認する。
