---
title: Azure Portal でのダッシュボードの作成と共有
description: この記事では、Azure portal でダッシュボードを作成、カスタマイズ、発行、共有する方法について説明します。
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 06cf0a5692b3d80a07c305f53c9e3801cbdb5110
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764011"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Azure Portal でのダッシュボードの作成と共有

ダッシュボードは、Azure portal でクラウドリソースを集中的に整理したビューです。 ダッシュボードは、日常業務のタスクをすばやく起動したり、リソースを監視したりできるワークスペースとして使用します。 たとえば、プロジェクト、タスク、またはユーザー ロールに基づいてカスタム ダッシュボードを作成します。

Azure portal には、出発点として既定のダッシュボードが用意されています。 デフォルトのダッシュボードを編集できます。 追加のダッシュボードを作成およびカスタマイズし、ダッシュボードを公開および共有して、他のユーザーが使用できるようにします。 この記事では、新しいダッシュボードの作成、インターフェイスのカスタマイズ、ダッシュボードの発行と共有の方法について説明します。

## <a name="create-a-new-dashboard"></a>新しいダッシュボードを作成する

この例では、新しいプライベート ダッシュボードを作成し、名前を割り当てます。 作業を開始するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal メニューから、 **[ダッシュボード]** を選択します。 既定のビューがダッシュボードに既に設定されています。

    ![ダッシュボードを開く](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. **[新しいダッシュボード]** を選択します。

    ![新しいダッシュボードのスクリーンショット](./media/azure-portal-dashboards/create-new-dashboard.png)

    この操作により、タイルを選択する **[タイル ギャラリー]** と、タイルを配置する空のグリッドが開きます。

    ![タイル ギャラリーと空のグリッドのスクリーンショット](./media/azure-portal-dashboards/dashboard-name.png)

1. ダッシュボード ラベルの **[マイ ダッシュボード]** というテキストを選択し、カスタム ダッシュボードを簡単に識別できる名前を入力します。

1. ページ ヘッダーの **[カスタマイズ完了]** を選択して編集モードを終了します。

ダッシュボード ビューに新しいダッシュボードが表示されます。 ダッシュボード名の横にある矢印を選択して、使用可能なダッシュボードを表示します。 リストには、他のユーザーが作成および共有したダッシュボードが含まれる場合があります。

## <a name="edit-a-dashboard"></a>ダッシュボードを編集する

次に、ダッシュボードを編集して、Azure リソースを表すタイルの追加、サイズ変更、配置を行いましょう。

### <a name="add-tiles-from-the-dashboard"></a>ダッシュボードからタイルを追加する

ダッシュボードにタイルを追加するには、次の手順に従います。

1. ページ ヘッダーの ![編集アイコン](./media/azure-portal-dashboards/dashboard-edit-icon.png) **[編集]** を選択します。

    ![[編集] が強調表示されたダッシュボードのスクリーンショット](./media/azure-portal-dashboards/dashboard-edit.png)

1. **[タイル ギャラリー]** を参照するか、検索フィールドを使用して必要なタイルを見つけます。

1. **[追加]** を選択して、タイルをデフォルトのサイズと場所でダッシュボードに追加します。 または、タイルをグリッドにドラッグし、希望の場所に配置します。

> [!TIP]
> 複数の組織を操作する場合は、リソースが属する組織を明確に示すために、 **[組織 ID]** タイルをダッシュボードに追加します。

### <a name="add-tiles-from-a-resource-page"></a>リソース ページからタイルを追加する

ダッシュボードにタイルを追加する別の方法があります。 多くのリソースページには、コマンドバーにプッシュピンアイコンが含まれています。 このアイコンを選択すると、ソース ページを表すタイルが、現在アクティブなダッシュボードにピン留めされます。 

![ピン アイコンが含まれたページのコマンド バーのスクリーンショット](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>タイルのサイズまたは配置を変更する

タイルのサイズを変更したり、ダッシュボードのタイルを並べ替えたりするには、次の手順に従います。

1. ページ ヘッダーの ![編集アイコン](./media/azure-portal-dashboards/dashboard-edit-icon.png) **[編集]** を選択します。

1. タイルの右上隅にあるコンテキスト メニューを選択します。 次に、タイルのサイズを選択します。 任意のサイズをサポートするタイルには、右下隅に "ハンドル" も含まれています。これを使用して、タイルを目的のサイズにドラッグできます。

    ![タイル サイズ メニューが表示されたダッシュボードのスクリーンショット](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. タイルを選択し、グリッド上の新しい場所にドラッグしてダッシュボードを編集します。

### <a name="additional-tile-configuration"></a>タイルの追加構成

一部のタイルには必要な情報を表示するため詳細な構成が必要となる場合があります。 たとえば、 **[メトリックのグラフ]** タイルは、**Azure Monitor** のメトリックを表示するように設定する必要があります。 また、タイル データをカスタマイズして、ダッシュボードの既定の時間設定を上書きすることもできます。

設定が必要なタイルには、タイルをカスタマイズするまで **[タイルの構成]** バナーが表示されます。 タイルをカスタマイズするには:

1. ページ ヘッダーの **[カスタマイズ完了]** を選択して編集モードを終了します。

1. バナーを選択し、必要な設定を行います。

    ![構成が必要なタイルのスクリーンショット](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> マークダウン タイルを使用すると、ダッシュボードにカスタムの静的コンテンツを表示できます。 これには、基本的な手順、画像、一連のハイパーリンク、または連絡先情報を指定できます。 マークダウンタイルの使用の詳細については、[Azure ダッシュボードでマークダウンタイルを使用してカスタムコンテンツを表示する](azure-portal-markdown-tile.md)を参照してください。

### <a name="customize-tile-data"></a>タイル データをカスタマイズする

ダッシュボードのデータには、過去 24 時間のアクティビティが自動的に表示されます。 このタイルでのみ異なる期間を表示するには、次の手順に従います。

1. コンテキストメニューから**タイルデータのカスタマイズ**を選択するか、タイルの左上隅から![フィルター アイコン](./media/azure-portal-dashboards/dashboard-filter.png)フィルターを選択します。

    ![タイルのコンテキスト メニューのスクリーンショット](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. **[タイル レベルでのダッシュボード時刻の設定を上書きします]** チェックボックスをオンにします。

    ![タイルの時間設定を構成するためのダイアログのスクリーンショット](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. このタイルに表示する期間を選択します。 過去 30 分から過去 30 日までの範囲の中から選択することも、カスタム範囲を定義することもできます。

1. 表示する時間の粒度を選択します。 1 分単位から 1 か月単位まで任意の粒度で表示できます。

1. **[適用]** を選択します。

## <a name="delete-a-tile"></a>タイルを削除する

ダッシュボードからタイルを削除するには、次の手順に従います。

* タイルの右上隅のコンテキスト メニューを選択し、 **[ダッシュボードから削除する]** を選択します。 または、

* ![編集アイコン](./media/azure-portal-dashboards/dashboard-edit-icon.png) **[編集]** を選択して、カスタマイズ モードに切り替えます。 タイルの右上隅にマウス ポインターを移動し、![削除アイコン](./media/azure-portal-dashboards/dashboard-delete-icon.png) 削除アイコンを選択してダッシュボードからタイルを削除します。

   ![ダッシュボードからタイルを削除する方法を示すスクリーンショット](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>ダッシュボードを複製する

既存のダッシュボードを新しいダッシュボードのテンプレートとして使用するには、次の手順に従います。

1. コピーするダッシュボードがダッシュボード ビューに表示されていることを確認します。

1. ページ ヘッダーで、![複製アイコン](./media/azure-portal-dashboards/dashboard-clone.png) **[複製]** を選択します。

1. *ダッシュボード名*の**複製**という名前のダッシュボードのコピーが編集モードで開きます。 この記事の前の手順を使用して、ダッシュボードの名前を変更し、カスタマイズします。

## <a name="publish-and-share-a-dashboard"></a>ダッシュボードを発行して共有する

作成したダッシュボードは、既定でプライベートになります。つまり、ダッシュボードを表示できるのは、その作成者に限られます。 他のユーザーがダッシュボードを利用できるようにするには、それらを発行して共有できます。 詳細については、「[ロールベースのアクセス制御を使用して Azure ダッシュボードを共有する](azure-portal-dashboard-share-access.md)」を参照してください。

### <a name="open-a-shared-dashboard"></a>共有ダッシュボードを開く

共有ダッシュボードを見つけて開くには、次の手順に従います。

1. ダッシュボード名の横にある矢印を選択します。

1. 表示されたダッシュボードの一覧から選択します。 開きたいダッシュボードが一覧に表示されない場合は、次のようにします。

    1. **[すべてのダッシュボードを参照]** を選択します。

        ![ダッシュボード選択メニューのスクリーンショット](./media/azure-portal-dashboards/dashboard-browse.png)

    1. **[種類]** フィールドで、 **[共有ダッシュボード]** を選択します。

        ![すべてのダッシュボード選択メニューのスクリーンショット](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. 1 つ以上のサブスクリプションを選択します。 テキストを入力して、ダッシュボードを名前でフィルター処理することもできます。

    1. 共有ダッシュボードの一覧からダッシュボードを選択します。

## <a name="delete-a-dashboard"></a>ダッシュボードを削除する

プライベートまたは共有ダッシュボードを完全に削除するには、次の手順に従います。

1. ダッシュボード名の横のリストから削除するダッシュボードを選択します。

1. ページ ヘッダーの ![削除アイコン](./media/azure-portal-dashboards/dashboard-delete-icon.png) **[削除]** を選択します。

1. プライベート ダッシュボードの場合は、確認ダイアログで **[OK]** をクリックしてダッシュボードを削除します。 共有ダッシュボードの場合は、確認ダイアログで、この発行済みのダッシュボードが他のユーザーに表示されなくなることを確認するチェックボックスをオンにします。 **[OK]** をクリックします。

    ![削除の確認のスクリーンショット](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>次のステップ

* [ロールベースのアクセス制御を使用して Azure ダッシュボードを共有する](azure-portal-dashboard-share-access.md)
* [プログラムによる Azure ダッシュボードの作成](azure-portal-dashboards-create-programmatically.md)
