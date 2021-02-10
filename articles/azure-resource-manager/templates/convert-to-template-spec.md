---
title: ポータルのテンプレートをテンプレート スペックに変換する
description: Azure portal ギャラリー内の既存のテンプレートをテンプレート スペックに変換する方法について説明します。
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 8fe02f55348f2cdcabb43e05bb547819d4b51228
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739063"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>ポータルのテンプレート ギャラリーをテンプレート スペックに変換する

Azure portal には、お使いのアカウントに Azure Resource Manager テンプレート (ARM テンプレート) を格納する方法が用意されています。 **この機能は非推奨となりました。** このギャラリーでテンプレートを引き続き使用するには、[テンプレート スペック](template-specs.md)に変換します。

この記事では、テンプレート ギャラリーの既存のテンプレートをテンプレート スペックに変換する方法について説明します。

ポータルで、非推奨とされる機能は **テンプレート (プレビュー)** と呼ばれています。 変換するテンプレートがあるかどうかを確認するには、[ポータルでテンプレート ギャラリー](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems)を表示します。 これらのテンプレートのリソースの種類は `Microsoft.Gallery/myareas/galleryitems` です。

## <a name="deprecation-of-portal-feature"></a>非推奨のポータル機能

ポータルのテンプレート ギャラリーは、2021 年 1 月 21 日に非推奨となります。 2 月 21 日までは使用を継続できます。 2 月 22 日以降、ポータル ギャラリーで新しいテンプレートを作成できなくなりますが、既存のテンプレートの表示とデプロイは引き続き行うことができます。

6 月 22 日に、この機能はポータルから削除され、すべての API 操作はブロックされます。 ギャラリーからのテンプレートの表示とデプロイの実行はできなくなります。

使用を継続したいテンプレートは、6 月 22 日より前に移行する必要があります。 この記事に記載されているいずれかの方法を使用して、テンプレートを移行できます。 この機能が削除された後は、移行していないテンプレートを取得するには、サポート ケースを開く必要があります。

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
