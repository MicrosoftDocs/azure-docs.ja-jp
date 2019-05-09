---
title: Azure Portal のダッシュボードの作成と共有 | Microsoft Docs
description: この記事では、Azure Portal でダッシュボードを作成して編集する方法について説明します。
services: azure-portal
documentationcenter: ''
author: sewatson
manager: doubeby
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: kfollis
ms.openlocfilehash: 693e973fb988a57c15b4ea2fae47f16b4ff39011
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818580"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Azure Portal でのダッシュボードの作成と共有
ダッシュボードを複数作成し、ご自分の Azure サブスクリプションにアクセスできる他のユーザーと共有することができます。  この記事では、ダッシュボードの作成、編集、発行のほか、ダッシュボードへのアクセスの管理に関する基本について説明します。

## <a name="create-a-dashboard"></a>ダッシュボードを作成する
ダッシュボードを作成するには、現在のダッシュボードの名前の隣にある **[新しいダッシュボード]** ボタンを選択します。  

![create dashboard](./media/azure-portal-dashboards/new-dashboard.png)

この操作を実行すると、新しい空のプライベート ダッシュボードが作成され、カスタマイズ モードに切り替わります。カスタマイズ モードでは、ダッシュボードに名前を付けられるほか、タイルを追加したり並べ替えたりできます。  このモードでは、折りたたみ可能なタイル ギャラリーが左側のナビゲーション メニューに表示されます。  タイル ギャラリーでは、さまざまな方法で Azure リソース用のタイルを探すことができます。[リソース グループ](../azure-resource-manager/resource-group-overview.md#resource-groups)、リソースの種類、[タグ](../azure-resource-manager/resource-group-using-tags.md)、または名前によるリソースの検索を使用して参照できます。  

![customize dashboard](./media/azure-portal-dashboards/customize-dashboard.png)

ダッシュボード画面上のお好きな場所にドラッグ アンド ドロップしてタイルを追加します。

特定のリソースに関連付けられていないタイル用に**全般**という新しいカテゴリがあります。  この例では、[マークダウン] タイルをピン留めします。  このタイルを使用してカスタム コンテンツをダッシュボードに追加します。  このタイルでは、プレーン テキスト、[マークダウン構文](https://daringfireball.net/projects/markdown/syntax)、一部の HTML がサポートされます   (安全のために、`<script>` タグを挿入したり、ポータルに干渉するおそれのある CSS の特定のスタイル要素を使用したりすることはできなくなっています)。 

![add markdown](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>ダッシュボードを編集する
ダッシュボードの作成後、タイル ギャラリーのタイルまたはブレードのタイル表示をピン留めできます。 リソース グループの表示をピン留めしてみましょう。 項目の参照中にピン留めすることも、リソース グループのブレードからピン留めすることもできます。 どちらの方法でも、リソース グループのタイル表示がピン留めされます。

![[ダッシュボードにピン留めする]](./media/azure-portal-dashboards/pin-to-dashboard.png)

項目のピン留め後、ダッシュボードにそれが表示されます。

![view dashboard](./media/azure-portal-dashboards/view-dashboard.png)

マークダウン タイルとリソース グループをダッシュボードにピン留めしたので、適切なレイアウトになるようにタイルのサイズを変更して並べ替えることができます。

[…] の上にカーソルを合わせて選択するか、タイルを右クリックして、そのタイルのコンテキストに応じたコマンドをすべて表示できます。 既定では、次の 2 つの項目があります。

1. **[ダッシュボードへのピン留めを外す]** – ダッシュボードからタイルが削除されます。
2. **[カスタマイズ]** – カスタマイズ モードに切り替わります。

![customize tile](./media/azure-portal-dashboards/customize-tile.png)

[カスタマイズ] を選択すると、タイルのサイズの変更と並べ替えを行えます。 タイルのサイズを変更するには、次の画像のようにコンテキスト メニューで新しいサイズを選択します。

![resize tile](./media/azure-portal-dashboards/resize-tile.png)

また、タイルで任意のサイズがサポートされている場合、右下隅をドラッグして目的のサイズにすることができます。

![resize tile](./media/azure-portal-dashboards/resize-corner.png)

タイルのサイズ変更が終わったら、ダッシュボードを表示します。

![view tile](./media/azure-portal-dashboards/view-tile.png)

ダッシュボードのカスタマイズが完了したら、**[カスタマイズ完了]** を選択してカスタマイズ モードを終了します。または、右クリックしてコンテキスト メニューで **[カスタマイズ完了]** を選択します。

## <a name="publish-a-dashboard-and-manage-access-control"></a>ダッシュボードを発行してアクセス制御を管理する
作成したダッシュボードは既定ではプライベートです。つまり、自分だけがこのダッシュボードを見ることができます。  他のユーザーもこのダッシュボードを表示できるようにするには、ダッシュボードの他のコマンドと共に表示されている **[共有]** ボタンを使用します。

![share dashboard](./media/azure-portal-dashboards/share-dashboard.png)

ダッシュボードの発行先となるサブスクリプションとリソース グループを選択するよう求められます。 ダッシュボードをエコシステムにシームレスに統合するために、Microsoft は Azure リソースとして共有ダッシュボードを実装しています (このため、電子メール アドレスを入力して共有することはできません)。  ポータルのほとんどのタイルに表示される情報へのアクセスは、[Azure のロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md)によって管理されています。 アクセス制御の見地からは、共有ダッシュボードは仮想マシンやストレージ アカウントと変わりありません。  

Azure サブスクリプションがあり、チームのメンバーに、サブスクリプションの**所有者**、**共同作成者**、または**閲覧者**ロールが割り当てられているとします。  所有者または共同作成者であるユーザーは、そのサブスクリプション内でダッシュボードを一覧表示、表示、作成、変更、削除できます。  閲覧者であるユーザーは、ダッシュボードの一覧表示と表示はできますが、変更と削除はできません。  閲覧者アクセス許可を持つユーザーは、共有ダッシュボードにローカルで編集を加えることはできますが、その変更をサーバーに戻すことはできません。  ただし、自分で使用するためにダッシュボードのプライベート コピーを作成することができます。  通常どおり、ダッシュボードの個々のタイルには、対応するリソースに基づいて独自のアクセス制御ルールが適用されます。  

便宜上、ポータルの発行機能によって、 **dashboards**という名前のリソース グループにダッシュボードを配置するように案内されます。  

![publish dashboard](./media/azure-portal-dashboards/publish-dashboard.png)

ダッシュボードを特定のリソース グループに発行することもできます。  このダッシュボードのアクセス制御は、リソース グループのアクセス制御に一致します。  このリソース グループ内のリソースを管理できるユーザーは、ダッシュボードにもアクセスすることができます。

![publish dashboard to resource group](./media/azure-portal-dashboards/publish-to-resource-group.png)

ダッシュボードの発行後、**[共有 + アクセス制御]** ウィンドウが更新され、発行したダッシュボードの情報が表示されます。ここには、ダッシュボードに対するユーザー アクセスを管理するためのリンクが含まれています。  このリンクから、Azure リソースのアクセス管理に使用される、ロールベースのアクセス制御の標準的なブレードが開きます。  **[共有]** を選択することで、いつでもこのビューに戻ることができます。

![manage access control](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>次の手順
* リソースを管理するには、「[Manage Azure resources by using the Azure portal (Azure portal を使用した Azure リソースの管理)](../azure-resource-manager/manage-resources-portal.md)」を参照してください。
* リソースのデプロイについては、「 [Resource Manager テンプレートと Azure Portal を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-portal.md)」を参照してください。

