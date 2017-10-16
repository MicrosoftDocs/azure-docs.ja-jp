---
title: "Azure Stack の Marketplace アイテムの作成と発行 | Microsoft Docs"
description: "Azure Stack にある Marketplace アイテムを作成および発行します。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 77e5f60c-a86e-4d54-aa8d-288e9a889386
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: erikje
ms.openlocfilehash: 64203ce186665aada98fbe8daed971164a650399
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-publish-a-marketplace-item"></a>Marketplace アイテムを作成および発行する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

## <a name="create-a-marketplace-item"></a>Marketplace アイテムの作成
1. Azure Gallery Packager ツールおよびサンプルの Azure Stack Marketplace アイテムを[ダウンロード](http://www.aka.ms/azurestackmarketplaceitem)します。
2. サンプルの Marketplace アイテムを開き、**SimpleVMTemplate** フォルダーの名前を変更します。 (自分の Marketplace アイテムと同じ名前を使用します。例: **Contoso.TodoList**)このフォルダーには、以下が格納されています。
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [Azure Resource Manager テンプレートを作成する](../azure-resource-manager/resource-group-authoring-templates.md)か、GitHub からテンプレートを選択します。 Marketplace アイテムでは、このテンプレートを使用して新しいリソースを作成します。
4. リソースを正常にデプロイできるか確認するには、Microsoft Azure Stack API を使ってテンプレートをテストします。
5. テンプレートが仮想マシン イメージに依存する場合、指示に従って [Azure Stack に仮想マシン イメージを追加](azure-stack-add-vm-image.md)します。
6. Azure Resource Manager テンプレートを **/Contoso.TodoList/DeploymentTemplates/** フォルダーに保存します。
7. Marketplace アイテムのアイコンとテキストを選択します。 **Icons** フォルダーにアイコンを追加し、**Strings** フォルダー内の **resources** ファイルにテキストを追加します。 アイコンの命名規則 Small、Medium、Large、Wide を使用します。 詳細については、「[Marketplace アイテムの UI リファレンス](#reference-marketplace-item-ui)」をご覧ください。
   
   > [!NOTE]
   > Marketplace アイテムを正しく構築するには、4 つすべてのアイコン サイズ (小、中、大、ワイド) が必要です。
   > 
   > 
8. **manifest.json** ファイルで、**名前**を Marketplace アイテムの名前に変更します。 さらに**発行者**を自分の名前または会社に変更します。
9. **artifacts** の下で、**名前**と**パス**を、含めた Azure Resource Manager テンプレートの正しい情報に変更します。
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. **[My Marketplace Items]** を Marketplace アイテムを表示するカテゴリの一覧に置き換えます。
    
             "categories":[
                 "My Marketplace Items"
              ],
11. Manifest.json に対するその他の編集については、「[リファレンス: Marketplace アイテム manifest.json](#reference-marketplace-item-manifestjson)」をご覧ください。
12. フォルダーを .azpkg ファイルにパッケージ化するには、コマンド プロンプトを開き、次のコマンドを実行します。
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > 出力パッケージへの完全パスが存在している必要があります。 たとえば、出力パスが C:\MarketPlaceItem\yourpackage.azpkg の場合は、C:\MarketPlaceItem フォルダーが存在する必要があります。
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Marketplace アイテムの発行
1. PowerShell または Azure Storage Explorer を使用して、Marketplace アイテム (.azpkg) を Azure Blob Storage にアップロードします。 ローカルの Azure Stack ストレージへのアップロード、または Azure Storage へのアップロードができます。 (パッケージの一時的な場所です。)BLOB がパブリックにアクセスできることを確認します。
2. Microsoft Azure Stack 環境のクライアント仮想マシンで、PowerShell セッションがサービス管理者の資格情報を使用して設定されていることを確認します。 Azure Stack で PowerShell を認証する方法についての説明は、[PowerShell を使用したテンプレートのデプロイ](user/azure-stack-deploy-template-powershell.md)に関するページにあります。
3. **Add-AzureRMGalleryItem** PowerShell コマンドレットを使用して Marketplace アイテムを Azure Stack に発行します。 For example:
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | パラメーター | 説明 |
   | --- | --- |
   | サブスクリプション ID |管理者のサブスクリプション ID。 PowerShell を使用して取得できます。 ポータルで取得する場合は、プロバイダーのサブスクリプションに移動し、サブスクリプション ID をコピーします。 |
   | GalleryItemUri |ストレージに既にアップロードされた、ギャラリー パッケージの BLOB URI。 |
   | ApiVersion |**2015-04-01** と設定します。 |
4. ポータルに移動します。 これで、オペレーターまたはユーザーとして、ポータルで Marketplace アイテムを確認できます。
   
   > [!NOTE]
   > パッケージの表示には数分かかる場合があります。
   > 
   > 
5. これで Marketplace アイテムが Azure Stack Marketplace に保存されました。 Blob Storage の場所から削除することができます。
6. Marketplace アイテムを削除するには、**Remove-AzureRMGalleryItem** コマンドレットを使用します。 例:
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > アイテムを削除した後に Marketplace UI でエラーが表示される可能性があります。 このエラーを解決するには、ポータルで **[設定]** をクリックします。 次に、**[ポータルのカスタマイズ]** で **[変更を破棄する]** を選択します。
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>リファレンス: Marketplace アイテム manifest.json
### <a name="identity-information"></a>ID 情報
| 名前 | 必須 | 型 | 制約 | Description |
| --- | --- | --- | --- | --- |
| 名前 |○ |String |[A-Za-z0-9]+ | |
| 発行元 |○ |String |[A-Za-z0-9]+ | |
| バージョン |○ |String |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Metadata
| 名前 | 必須 | 型 | 制約 | Description |
| --- | --- | --- | --- | --- |
| displayName |○ |String |推奨 80 文字 |80 文字より長い場合、ポータルでアイテム名が適切に表示されないことがあります。 |
| PublisherDisplayName |○ |String |推奨 30 文字 |30 文字より長い場合、ポータルで発行者名が適切に表示されないことがあります。 |
| PublisherLegalName |○ |String |最大 256 文字 | |
| 概要 |○ |String |60 ～ 100 文字 | |
| LongSummary |○ |String |140 ～ 256 文字 |Azure Stack ではまだ適用なし。 |
| Description |○ |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 ～ 5,000 文字 | |

### <a name="images"></a>イメージ
Marketplace では、次のアイコンを使用します。

| 名前 | 幅 | 高さ | メモ |
| --- | --- | --- | --- |
| ワイド |255 px |115 px |常に必要 |
| Large |115 px |115 px |常に必要 |
| 中 |90 px |90 px |常に必要 |
| Small |40 px |40 px |常に必要 |
| スクリーンショット |533 px |32 px |省略可能 |

### <a name="categories"></a>カテゴリ
Marketplace の各アイテムは、そのアイテムのポータル UI における表示場所を識別するカテゴリでタグ付けする必要があります。 Azure Stack 内の既存のカテゴリのいずれか (コンピューティング、データ + ストレージなど) を選択するか、新しいものを選択します。

### <a name="links"></a>リンク
各 Marketplace アイテムには、追加コンテンツへのさまざまなリンクを含めることができます。 リンクは、名前と URI の一覧として指定されます。

| 名前 | 必須 | 型 | 制約 | Description |
| --- | --- | --- | --- | --- |
| displayName |○ |String |最大 64 文字 | |
| Uri |○ |URI | | |

### <a name="additional-properties"></a>追加のプロパティ
前述のメタデータに加えて、Marketplace 作成者は次の形式でカスタムのキー/値のペアでデータを指定することができます。

| 名前 | 必須 | 型 | 制約 | Description |
| --- | --- | --- | --- | --- |
| displayName |○ |String |最大 25 文字 | |
| 値 |○ |String |最大 30 文字 | |

### <a name="html-sanitization"></a>HTML のサニタイズ
HTML を許可するフィールドでは、次の要素と属性が許可されます。

h1、h2、h3、h4、h5、p、ol、ul、li、a[target|href]、br、strong、em、b、i

## <a name="reference-marketplace-item-ui"></a>リファレンス: Marketplace アイテムの UI
Azure Stack ポータルで表示される Marketplace アイテムのアイコンとテキストは、次のとおりです。

### <a name="create-blade"></a>[作成] ブレード
![[作成] ブレード](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>[Marketplace item details (Marketplace アイテムの詳細)] ブレード
![[Marketplace item details (Marketplace アイテムの詳細)] ブレード](media/azure-stack-marketplace-item-ui-reference/image3.png)

