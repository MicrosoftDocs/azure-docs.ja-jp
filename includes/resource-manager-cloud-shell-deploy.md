---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 7c081b3bc5f9e6273f680b24897f9aced4999afa
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302344"
---
## <a name="deploy-template-from-cloud-shell"></a>Cloud Shell からのテンプレートのデプロイ

テンプレートは、[Cloud Shell](../articles/cloud-shell/overview.md) を使ってデプロイすることができます。 外部テンプレートをデプロイするには、外部デプロイの場合とまったく同じように、テンプレートの URI を指定します。 ローカル テンプレートをデプロイするには、最初に Cloud Shell のストレージ アカウントにテンプレートを読み込む必要があります。 このセクションでは、Cloud Shell アカウントにテンプレートを読み込み、ローカル ファイルとしてデプロイする方法について説明します。 Cloud Shell の使用経験がない場合は、その設定について、「[Azure Cloud Shell の概要](../articles/cloud-shell/overview.md)」を参照してください。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. Cloud Shell リソース グループを選択します。 名前のパターンは `cloud-shell-storage-<region>` です。

   ![リソース グループの選択](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Cloud Shell のストレージ アカウントを選択します。

   ![ストレージ アカウントを選択する](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. **[BLOB]** を選択します。

   ![BLOB の選択](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. **[+ コンテナー]** を選択します。

   ![コンテナーの追加](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. コンテナーに名前とアクセス レベルを付与します。 この記事のサンプル テンプレートには機密性の高い情報は含まれていないため、匿名の読み取りアクセスを許可します。 **[OK]** を選択します。

   ![コンテナーの値の指定](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. 作成したコンテナーを選択します。

   ![新しいコンテナーの選択](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. **[アップロード]** を選択します。

   ![BLOB のアップロード](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. テンプレートを見つけてアップロードします。

   ![ファイルをアップロードする](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. アップロードが完了したら、テンプレートを選択します。

   ![新しいテンプレートの選択](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. URL をコピーします。

   ![URL のコピー](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. プロンプトを開きます。

   ![Cloud Shell を開く](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
