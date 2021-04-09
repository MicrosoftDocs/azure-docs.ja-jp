---
title: Data Factory を Azure Purview に接続する
description: Data Factory を Azure Purview に接続する方法について説明します
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 44f093f96d0f4653a6fcca94aaa97264c93e3c7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727942"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Data Factory を Azure Purview に接続する (プレビュー)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Data Factory を Azure Purview に接続する方法と、Azure Data Factory アクティビティ (データのコピー、データ フロー、SSIS パッケージの実行) のデータ系列を報告する方法について説明します。


## <a name="connect-data-factory-to-azure-purview"></a>Data Factory を Azure Purview に接続する
Azure Purview は、データ ユーザーがクラウド環境とオンプレミス環境にまたがるデータ資産全体のデータ ガバナンスを一元管理するために使用する新しいクラウド サービスです。 Data Factory を Azure Purview に接続できます。また、この接続により、Azure Purview を使用して、コピー、データ フロー、SSIS パッケージの実行の系列データをキャプチャできます。 Data Factory を Azure Purview に接続するには、次の 2 つの方法があります。
### <a name="register-azure-purview-account-to-data-factory"></a>Azure Purview アカウントを Data Factory に登録する
1. ADF ポータルで、 **[管理]**  ->  **[Azure Purview]** にアクセスします。 **[Connect to a Purview account]\(Purview アカウントに接続する\)** を選択します。 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Purview アカウントを登録するためのスクリーンショット。":::
2. **[Azure サブスクリプションから]** または **[手動で入力]** を選択できます。 **Azure サブスクリプションから**、自分がアクセスできるアカウントを選択できます。 
3. 接続すると、 **[Purview アカウント]** タブで Purview アカウントの名前を確認できます。 
4. Azure Data Factory ポータルの上部中央にある検索バーを使用して、データを検索することができます。 

Azure Purview アカウントを Data Factory に登録した後に、Azure Data Factory ポータルで警告が表示される場合は、次の手順に従って問題を解決してください。

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Purview アカウント登録の警告のスクリーンショット。":::

1. Azure portal にアクセスして、データ ファクトリを検索します。 [タグ] セクションを選択し、**catalogUri** という名前のタグがあるかどうかを確認します。 ない場合は、ADF ポータルで Azure Purview アカウントを切断し、再接続してください。

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="Purview アカウント登録のタグのスクリーンショット。":::

2. Azure Purview アカウントを Data Factory に登録するためのアクセス許可が付与されているかどうかを確認します。 「[Azure Data Factory と Azure Purview を接続する方法](../purview/how-to-link-azure-data-factory.md#create-new-data-factory-connection)」を参照してください。

### <a name="register-data-factory-in-azure-purview"></a>Azure Purview に Data Factory を登録する
Azure Purview に Data Factory を登録する方法については、「[Azure Data Factory と Azure Purview を接続する方法](../purview/how-to-link-azure-data-factory.md)」を参照してください。 

## <a name="report-lineage-data-to-azure-purview"></a>系列データを Azure Purview に報告する
お客様が Azure Data Factory でコピー、データ フロー、または SSIS パッケージの実行アクティビティを実行すると、お客様は依存関係を取得し、データ ソースと宛先の間のワークフロー プロセス全体の概要を把握できます。
Azure Data Factory からデータ系列を収集する方法については、[Data Factory のデータ系列](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
[カタログ系列ユーザー ガイド](../purview/catalog-lineage-user-guide.md)

[チュートリアル: Data Factory 系列データを Azure Purview にプッシュする](turorial-push-lineage-to-purview.md)