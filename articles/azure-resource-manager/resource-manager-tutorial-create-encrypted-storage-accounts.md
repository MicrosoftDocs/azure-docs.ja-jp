---
title: Azure Resource Manager テンプレートのリファレンスを活用する | Microsoft Docs
description: Azure Resource Manager テンプレートのリファレンスを活用して、暗号化されたストレージ アカウントをデプロイするためのテンプレートを作成します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 68199083369018cab1c9f83dd6781653a8cae37c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57849297"
---
# <a name="tutorial-utilize-the-azure-resource-manager-template-reference"></a>チュートリアル: Azure Resource Manager テンプレートのリファレンスを活用する

テンプレート スキーマ情報を見つける方法のほか、その情報を使用して Azure Resource Manager テンプレートを作成する方法について説明します。

このチュートリアルでは、Azure クイック スタート テンプレートから入手したベース テンプレートを使用します。 テンプレート リファレンス ドキュメントを使用し、テンプレートをカスタマイズして、暗号化されたストレージ アカウントを作成します。

![Resource Manager テンプレートのリファレンスによる暗号化されたストレージ アカウントのデプロイ](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-tutorial-deploy-encrypted-storage-account.png)

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートの理解
> * テンプレート リファレンスの検索
> * テンプレートの編集
> * テンプレートのデプロイ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Visual Studio Code](https://code.visualstudio.com/) と [Resource Manager ツール拡張機能](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/)は、Resource Manager テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このクイック スタートで使用されるテンプレートは、[Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) と呼ばれます。 テンプレートにより、Azure ストレージ アカウント リソースが定義されます。

1. Visual Studio Code から、**[ファイル]**>**[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。
4. **[ファイル]**>**[名前を付けて保存]** を選択し、ファイルを **azuredeploy.json** としてご自身のローカル コンピューターに保存します。

## <a name="understand-the-schema"></a>スキーマの概要

1. VS Code から、テンプレートをルート レベルに折りたたみます。 次の要素を含む最も単純な構造があります。

    ![Resource Manager テンプレートの最も単純な構造](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * **$schema**: テンプレート言語のバージョンが記述されている JSON スキーマ ファイルの場所を指定します。
    * **contentVersion**: この要素に値を指定して、テンプレートの大きな変更を記述します。
    * **parameters**: リソースのデプロイをカスタマイズするためにデプロイを実行するときに提供される値を指定します。
    * **variables**: テンプレート言語式を簡略化するためにテンプレート内で JSON フラグメントとして使用される値を指定します。
    * **resources**: リソース グループ内でデプロイまたは更新されるリソースの種類を指定します。
    * **outputs**: デプロイ後に返される値を指定します。

2. **resources** を展開します。 `Microsoft.Storage/storageAccounts` リソースが定義されています。 テンプレートによって、暗号化されていないストレージ アカウントが作成されます。

    ![Resource Manager テンプレートのストレージ アカウント定義](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>テンプレート リファレンスの検索

1. [Azure テンプレート リファレンス](https://docs.microsoft.com/azure/templates/)を参照します。
2. **[タイトルでフィルター処理します]** ボックスで、**ストレージ アカウント**を入力します。
3. 次のスクリーンショットに示すように、**Reference/Template reference/Storage/&lt;Version>/Storage Accounts** を選択します。

    ![Resource Manager テンプレート参照ストレージ アカウント](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    選択するべきバージョンがわからない場合は、最新バージョンを使用します。

4. 暗号化関連の定義情報を見つけます。  

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    同じ Web ページの次の説明によって、暗号化されたストレージ アカウントを作成するために `encryption` オブジェクトが使用されることが確認されます。

    ![Resource Manager テンプレート リファレンスのストレージ アカウントの暗号化](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    暗号化キーを管理するには 2 つの方法があります。 Storage Service Encryption には Microsoft が管理する暗号化キーのほか、ユーザー独自の暗号化キーを使うことができます。 このチュートリアルの簡潔さを維持するため、`Microsoft.Storage` オプションを使用します。そのため、Azure キー コンテナーを作成する必要がありません。

    ![Resource Manager テンプレート リファレンスのストレージ アカウントの暗号化オブジェクト](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    暗号化オブジェクトは次のようになります。

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>テンプレートの編集

Visual Studio code で、リソース要素が次のようになるようにテンプレートを変更します。

![Resource Manager テンプレートの暗号化されたストレージ アカウントの resources](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

デプロイ手順については、Visual Studio Code のクイック スタートの「[テンプレートのデプロイ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template)」セクションを参照してください。

次のスクリーンショットは、新しく作成されたストレージ アカウントを一覧表示するための CLI コマンドを示しています。これは、BLOB ストレージに対して暗号化が有効になっていることを示しています。

![Azure Resource Manager の暗号化されたストレージ アカウント](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、テンプレート参照を使用して既存のテンプレートをカスタマイズする方法について説明しました。 複数のストレージ アカウント インスタンスを作成する方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [複数のインスタンスの作成](./resource-manager-tutorial-create-multiple-instances.md)
