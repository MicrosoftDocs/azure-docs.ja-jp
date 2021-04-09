---
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
author: linda33wj
ms.author: jingwang
ms.openlocfilehash: 34848b638ff0c7f7b9d1a2f3e5894339f8310ccc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013364"
---
## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

このパイプラインは、adftutorial BLOB コンテナーに対して output フォルダーを自動的に作成します。 そのうえで、input フォルダーから output フォルダーに emp.txt ファイルをコピーします。 

1. Azure portal の **adftutorial** コンテナー ページで、 **[最新の情報に更新]** を選択して出力フォルダーを表示します。 
    
    ![コンテナー ページのスクリーンショット。ここでページを最新の情報に更新することができます。](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)

2. フォルダー一覧で、 **[output]** を選択します。 

3. **emp.txt** が output フォルダーにコピーされていることを確認します。 

    ![output フォルダーの内容を示すスクリーンショット。](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成したリソースは、2 とおりの方法でクリーンアップすることができます。 [Azure リソース グループ](../articles/azure-resource-manager/management/overview.md)を削除した場合、そのリソース グループに含まれたすべてのリソースが対象となります。 他のリソースをそのまま維持する場合は、このチュートリアルで作成したデータ ファクトリだけを削除してください。

リソース グループを削除すると、その中のデータ ファクトリも含めて、すべてのリソースが削除されます。 次のコマンドを実行して、リソース グループ全体を削除します。 

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

> [!Note]
> リソース グループの削除には、しばらく時間がかかることがあります。 処理が完了するまでお待ちください。

リソース グループ全体ではなく、データ ファクトリだけを削除する場合は、次のコマンドを実行します。 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```