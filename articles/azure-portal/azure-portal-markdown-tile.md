---
title: Azure ダッシュボードでカスタムのマークダウン タイルを使用する
description: マークダウン タイルを Azure ダッシュボードに追加して、静的コンテンツを表示する方法について説明します
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ec8cbddda4137656a53fd4968c451cd413959274
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110785"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Azure ダッシュボードでマークダウン タイルを使用して、カスタム コンテンツを表示する

マークダウン タイルを Azure ダッシュボードに追加して、カスタム コンテンツ、静的コンテンツを表示できます。 たとえば、マークダウン タイルを使用して、基本的な手順、画像、または一連のハイパーリンクを表示できます。

## <a name="add-a-markdown-tile-to-your-dashboard"></a>マークダウン タイルをダッシュボードに追加する

1. Azure portal のサイドバーから **[ダッシュボード]** を選択します。 カスタム ダッシュボードを既に作成している場合は、ダッシュボード ビューで、ドロップダウンを使用して、カスタムのマークダウン タイルが表示されるダッシュボードを選択します。 編集アイコンを選択して、**タイル ギャラリー**を開きます。

   ![ダッシュボード編集ビューを示すスクリーンショット](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. **[タイル ギャラリー]** で **[Markdown]\(マークダウン\)** というタイルを見つけて、**[追加]** をクリックします。 タイルがダッシュボードに追加され、**[マークダウンの編集]** ウィンドウが開きます。

1. **[タイトル]**、**[サブタイトル]**、および **[コンテンツ]** フィールドを編集して、タイルをカスタマイズします。 以下の例では、マークダウン タイルがカスタム ヘルプ デスク情報を表示するように編集されています。

   ![マークダウン タイルの編集ビューを示すスクリーンショット](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. **[完了]** を選択して、**[マークダウンの編集]** ウィンドウを閉じます。 コンテンツが [マークダウン] タイルに表示されます。これは、右下隅のハンドルをドラッグしてサイズ変更できます。

   ![カスタムのマークダウン タイルを示すスクリーンショット](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>マークダウン コンテンツの機能と制限

マークダウン タイルでは、プレーン テキスト、マークダウン構文、HTML コンテンツの任意の組み合わせを使用できます。 Azure portal により "_マーク済_" というオープンソース ライブラリが使用され、コンテンツがタイルに表示される HTML に変換されます。 "_マーク済_" によって生成される HTML が、レンダリングされる前にポータルによって事前処理されます。 この手順は、独自のカスタマイズが、セキュリティまたはポータルのレイアウトに影響しないことを確認するのに役立ちます。 事前処理中に、潜在的な脅威となる HTML のすべての部分が削除されます。 次の種類のコンテンツは、ポータルによって許可されていません。

* JavaScript – `<script>` タグとインライン JavaScript 評価が削除されます。
* iframe - `<iframe>` タグが削除されます。
* スタイル - `<style>` タグが削除されます。 HTML 要素のインライン スタイル属性は、公式にはサポートされていません。 一部のインライン スタイル要素が動作する場合がありますが、ポータルのレイアウトに干渉した場合、いつでもその動作は停止します。 [マークダウン] タイルは、ポータルの既定のスタイルを使用する基本的な静的コンテンツを対象としています。

## <a name="next-steps"></a>次の手順

* カスタム ダッシュボードを作成するには、「[Azure portal でのダッシュボードの作成および共有方法](../azure-portal/azure-portal-dashboards.md)」を参照してください。
