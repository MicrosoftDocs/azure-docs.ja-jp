---
title: ポータルのテンプレートをテンプレート スペックに変換する
description: Azure portal ギャラリー内の既存のテンプレートをテンプレート スペックに変換する方法について説明します。
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: c59275fca1eb3037b48b7293fc9e507df46b7fcb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99555942"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>ポータルのテンプレート ギャラリーをテンプレート スペックに変換する

Azure portal には、お使いのアカウントに Azure Resource Manager テンプレート (ARM テンプレート) を格納する方法が用意されています。 ただし、[テンプレート スペック](template-specs.md)を使用すると、より簡単な方法で、テンプレートを組織内のユーザーと共有でき、他のテンプレートとリンクできます。 この記事では、テンプレート ギャラリーの既存のテンプレートをテンプレート スペックに変換する方法について説明します。

変換するテンプレートがあるかどうかを確認するには、[ポータルでテンプレート ギャラリー](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems)を表示します。 これらのテンプレートのリソースの種類は `Microsoft.Gallery/myareas/galleryitems` です。

## <a name="convert-with-powershell-script"></a>PowerShell スクリプトを使用して変換する

テンプレート ギャラリー内のテンプレートの変換を簡略化するには、Azure クイックスタート テンプレート リポジトリから PowerShell スクリプトを使用します。 スクリプトを実行するときは、テンプレートごとに新しいテンプレート スペックを作成するか、テンプレート スペックを作成するテンプレートをダウンロードすることができます。このスクリプトでは、テンプレート ギャラリーからテンプレートは削除されません。

1. [移行スクリプト](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1)をコピーします。 *Migrate-GalleryItems.ps1* という名前でローカル コピーを保存します。
1. 新しいテンプレート スペックを作成するには、`-ResourceGroupName` および `-Location` の各パラメーターの値を指定します。 

   テンプレートをエクスポートするには、`ItemsToExport` を `MyGalleryItems` に設定します。 アクセスできるすべてのテンプレートをエクスポートするには、`AllGalleryItems` に設定します。

   次の例では、**migratedRG** という名前のリソース グループ内のテンプレートごとに新しいテンプレート スペックを作成します。 リソース グループが存在しない場合は、スクリプトによって作成されます。

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. テンプレート スペックの作成に使用できるテンプレートをダウンロードするには、リソース グループまたは場所の値を指定しないでください。 代わりに、`-ExportToFile` を指定します。 このテンプレートは、ギャラリーのテンプレートと同じではありません。 代わりに、テンプレートのテンプレート スペックを作成するテンプレート スペッ ク リソースが含まれています。

   次の例では、テンプレート スペックを作成せずにテンプレートをダウンロードします。

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   テンプレート スペックを作成するテンプレートをデプロイする方法については、「[クイック スタート:テンプレート スペックの作成とデプロイ (プレビュー)](quickstart-create-template-specs.md)」を参照してください。

スクリプトとそのパラメーターの詳細については、「[テンプレート ギャラリーのテンプレートから TemplateSpecs を作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create)」を参照してください。

## <a name="manually-convert-through-portal"></a>ポータルを使用して手動で変換する

ギャラリーから新しいテンプレート スペックにテンプレートを手動でコピーできます。

1. ポータルで、[[テンプレート (プレビュー)]](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) を開きます。
1. 移行するテンプレートを選択します。
1. **[テンプレートの表示]** を選択します。
1. テンプレートの内容をコピーします。
1. ポータルの検索バーで、**テンプレート スペック** を検索します。 そのオプションを選択します。
1. **[Create template spec]\(テンプレート スペックの作成\)** を選択します。
1. 名前、サブスクリプション、リソース グループ、場所、およびバージョンの値を入力します。
1. **Next:テンプレートの編集** を選択します。
1. テンプレートの内容には、テンプレート ギャラリーからコピーしたテンプレートを貼り付けます。
1. **[確認および作成]** を選択します。
1. 検証が正常に完了したら、 **[作成]** を選択します。

テンプレート スペックを組織内の他のユーザーと共有する必要がある場合は、アクセス権が必要なグループやユーザーに[ロールベースのアクセス制御を設定します](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)。

## <a name="next-steps"></a>次のステップ

テンプレート スペックの詳細については、[テンプレート スペックの作成とデプロイ](template-specs.md)に関する記事を参照してください。
