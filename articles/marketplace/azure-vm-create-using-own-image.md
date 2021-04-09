---
title: 独自のイメージを使用して Azure Marketplace に Azure 仮想マシン オファーを作成する
description: 独自のイメージを使用して Azure Marketplace に Azure 仮想マシン オファーを公開する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 4711ea76af83594ec529cfda13a308fbe6646398
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200457"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>独自のイメージを使用して仮想マシンを作成する方法

この記事では、ユーザー指定の仮想マシン (VM) イメージを作成してデプロイする方法について説明します。

> [!NOTE]
> この手順を開始する前に、Azure VM オファーの仮想ハード ディスク (VHD) などに関する[技術要件](marketplace-virtual-machines.md#technical-requirements)を確認してください。

代わりに、承認済みのベース イメージを使用する場合は、[承認済みのベースからの VM イメージの作成](azure-vm-create-using-approved-base.md)に関する手順に従います。

## <a name="configure-the-vm"></a>VM を構成する

このセクションでは、Azure VM のサイズ変更、更新、一般化を行う方法について説明します。 これらの手順は、Azure Marketplace にデプロイする VM を準備するために必要です。

### <a name="size-the-vhds"></a>VHD のサイズを調整する

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>最新の更新プログラムをインストールする

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>より多くのセキュリティ チェックを実行する

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>カスタム構成とスケジュールされたタスクを実行する

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>イメージを汎用化する

Azure Marketplace のすべてのイメージは汎用的な方法で再利用できる必要があります。 これを実現するには、オペレーティング システム VHD を一般化する必要があります。これは、インスタンス固有の識別子とソフトウェア ドライバーを VM からすべて削除する操作です。

## <a name="bring-your-image-into-azure"></a>イメージを Azure に取り込む

Azure にイメージを取り込むには、次の 3 つの方法があります。

1. VHD を Shared Image Gallery (SIG) にアップロードする。
1. VHD を Azure ストレージ アカウントにアップロードする。
1. マネージド イメージから VHD を抽出する (イメージ作成サービスを使用している場合)。

次の 3 つのセクションで、これらのオプションについて説明します。

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>オプション 1: VHD を Shared Image Gallery としてアップロードする

1. VHD をストレージ アカウントにアップロードします。
2. Azure portal で、 **[カスタム テンプレートのデプロイ]** を検索します。
3. **[Build your own template in the editor] \(エディターで独自のテンプレートをビルド\)** を選択します。
4. 次の Azure Resource Manager (ARM) テンプレートをコピーします。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sourceStorageAccountResourceId": {
          "type": "string",
          "metadata": {
            "description": "Resource ID of the source storage account that the blob vhd resides in."
          }
        },
        "sourceBlobUri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk0Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk1Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "galleryName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Shared Image Gallery."
          }
        },
        "galleryImageDefinitionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Definition."
          }
        },
        "galleryImageVersionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Version - should follow <MajorVersion>.<MinorVersion>.<Patch>."
          }
        }
      },
      "resources": [
        {
          "type": "Microsoft.Compute/galleries/images/versions",
          "name": "[concat(parameters('galleryName'), '/', parameters('galleryImageDefinitionName'), '/', parameters('galleryImageVersionName'))]",
          "apiVersion": "2020-09-30",
          "location": "[resourceGroup().location]",
          "properties": {
            "storageProfile": {
              "osDiskImage": {
                "source": {
                  "id": "[parameters('sourceStorageAccountResourceId')]",
                  "uri": "[parameters('sourceBlobUri')]"
                }
              },
    
              "dataDiskImages": [
                {
                  "lun": 0,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk0Uri')]"
                  }
                },
                {
                  "lun": 1,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk1Uri')]"
                  }
                }
              ]
            }
          }
        }
      ]
    }
    
    ```

5. テンプレートをエディターに貼り付けます。

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="VM のサンプル コード画面。":::

1. **[保存]** を選択します。
1. この表のパラメーターを使用して、次の画面のフィールドに入力します。

| パラメーター | 説明 |
| --- | --- |
| sourceStorageAccountResourceId | BLOB VHD が存在するソース ストレージ アカウントのリソース ID。<br><br>リソース ID を取得するには、 **Azure portal** で自分の **ストレージ アカウント** に移動し、 **[プロパティ]** に移動して、**ResourceID** の値をコピーします。 |
| sourceBlobUri | OS ディスク VHD BLOB の BLOB URI (指定されたストレージ アカウントに存在する必要があります)。<br><br>BLOB URL を取得するには、**Azure portal** で自分の **ストレージ アカウント** に移動し、自分の **BLOB** に移動して、**URL** の値をコピーします。 |
| sourceBlobDataDisk0Uri | データ ディスク VHD BLOB の BLOB URI (指定されたストレージ アカウントに存在する必要があります)。 データ ディスクがない場合は、このパラメーターをテンプレートから削除します。<br><br>BLOB URL を取得するには、**Azure portal** で自分の **ストレージ アカウント** に移動し、自分の **BLOB** に移動して、**URL** の値をコピーします。 |
| sourceBlobDataDisk1Uri | 追加データ ディスク VHD BLOB の BLOB URI (指定されたストレージ アカウントに存在する必要があります)。 追加データ ディスクがない場合は、このパラメーターをテンプレートから削除します。<br><br>BLOB URL を取得するには、**Azure portal** で自分の **ストレージ アカウント** に移動し、自分の **BLOB** に移動して、**URL** の値をコピーします。 |
| galleryName | Shared Image Gallery の名前 |
| galleryImageDefinitionName | イメージ定義の名前 |
| galleryImageVersionName | 作成されるイメージ バージョンの名前。`<MajorVersion>.<MinorVersion>.<Patch>` の形式で指定します。 |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="カスタム デプロイ ウィンドウを示します。":::

8. **[Review + create]\(レビュー + 作成\)** を選択します。 検証が終了したら、 **[作成]** を選択します。

> [!TIP]
> SIG イメージを公開するには、公開元アカウントに "所有者" アクセス権が必要です。 必要に応じて、下の手順に従ってアクセス権を付与します。
>
> 1. Azure Shared Image Gallery (SIG) にアクセスします。
> 2. 左側のパネルで **[アクセス制御 (IAM)]** を選択します。
> 3. **[追加]** 、 **[ロールの割り当ての追加]** の順に選択します。
> 4. **[ロール]** で、 **[所有者]** を選択します。
> 5. **[アクセスの割り当て先]** で、 **[User, group, or service principle]\(ユーザー、グループ、またはサービス プリンシパル\)** を選択します。
> 6. イメージを公開するユーザーの Azure メールアドレスを入力します。
> 7. **[保存]** を選択します。<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="[ロールの割り当ての追加] ウィンドウが表示されます。":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>オプション 2: ストレージ アカウントに VHD をアップロードする

「[Azure にアップロードする Windows VHD または VHDX を準備する](../virtual-machines/windows/prepare-for-upload-vhd-image.md)」または [Linux VHD の作成とアップロード](../virtual-machines/linux/create-upload-generic.md)に関するページで説明されている方法で、VM をアップロードするための構成と準備を行います。

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>オプション 3: マネージド イメージから VHD を抽出する (イメージ作成サービスを使用している場合)

[Packer](https://www.packer.io/) などのイメージ作成サービスを使用している場合は、イメージから VHD を抽出する必要がある場合があります。 これは直接的には実行できません。 VM を作成し、VM ディスクから VHD を抽出する必要があります。

## <a name="create-the-vm-on-the-azure-portal"></a>Azure portal で VM を作成する

[Azure portal](https://ms.portal.azure.com/) でベース VM イメージを作成するには、次の手順に従います。

1. [Azure portal](https://ms.portal.azure.com/) にサインインします。
2. **[Virtual Machines]** を選択します。
3. **[+ 追加]** を選択して、 **[仮想マシンの作成]** 画面を開きます。
4. ドロップダウン リストからイメージを選択するか、または **[すべてのパブリックおよびプライベート イメージを参照する]** を選択して、使用可能なすべての仮想マシン イメージを検索または参照します。
5. **Gen 2** VM を作成するには、 **[詳細設定]** タブに移動し、 **[Gen 2]** オプションを選択します。

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Gen 1 または Gen 2 を選択します。":::

6. デプロイする VM のサイズを選択します。

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="選択したイメージで推奨される VM サイズを選択します。":::

7. VM の作成に必要なその他の詳細を指定します。
8. **[確認と作成]** を選択して、選択内容を確認します。 **検証に成功** のメッセージが表示されたら、 **[作成]** を選択します。

Azure で、指定した仮想マシンのプロビジョニングが開始されます。 左側のメニューで **[Virtual Machines]** タブを選択して、進捗状況を追跡します。 仮想マシンが作成されると、状態が **[実行中]** に変更されます。

## <a name="connect-to-your-vm"></a>VM に接続する

お使いの [Windows](../virtual-machines/windows/connect-logon.md) または [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM に接続する方法については、次のドキュメントを参照してください。

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>次の手順

- [お使いの VM イメージをテストし](azure-vm-image-test.md)、それが Azure Marketplace の公開要件を満たしていることを確認します。 これは省略可能です。
- VM イメージをテストしない場合は、 [パートナー センター](https://partner.microsoft.com/)にサインインし、SIG イメージを公開します (オプション #1)。
- オプション #2 または #3 に従った場合は、 [SAS URI を生成](azure-vm-get-sas-uri.md)します。
- お使いの Azure ベースの新しい VHD の作成で問題が発生した場合は、[Azure Marketplace の VM に関する FAQ](azure-vm-create-faq.md) を参照してください。
