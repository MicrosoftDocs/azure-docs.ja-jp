---
title: 需要の変化に対応するために Azure データ エクスプローラー クラスターをスケールアップする
description: この記事では、需要の変化に基づいて、Azure Data Explorer クラスターをスケールアップおよびスケールダウンする手順について説明します。
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 1f130f79b6b6924559e1693e1eef8ced2972b3d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783868"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>需要の変化に対応するようにクラスターのスケールアップを管理する

Azure Data Explorer クラスターのスケーリングには、スケールアップと[スケールアウト](manage-cluster-scale-out.md)の 2 つのワークフローがあります。この記事では、クラスターのスケールアップを管理する方法を示します。

クラスターのサイズを適切に設定することは、Azure データ エクスプローラーのパフォーマンスにとって重要なことです。 しかし、クラスターに対する需要を絶対の精度で予測することはできません。 静的クラスター サイズは過少使用や過剰使用につながる可能性があります。いずれも理想的ではありません。 変化する需要に合わせて容量と CPU リソースを追加および削除して、クラスターを*スケーリング*することをお勧めします。 

## <a name="steps-to-scale-up"></a>スケールアップする手順
1. クラスターに移動します。 **[設定]** で、**[スケール アップ]** を選択します。

    利用可能な SKU の一覧が表示されます。 たとえば、次の図では、利用可能な SKU は 4 つのみです。

    ![スケールアップ](media/manage-cluster-scale-up/scale-up.png)

    SKU が無効になっているのは、それが現在の SKU であるか、そのクラスターが配置されているリージョンで利用できないためです。

1. SKU を変更するには、目的の SKU を選択し、**[選択]** ボタンをクリックします。

> [!NOTE]
> スケールアップ プロセスには数分かかる可能性があり、その間クラスターは停止されます。 スケールダウンは、クラスターのパフォーマンスが低下することがあるので注意してください。

これで、Azure Data Explorer クラスターのスケールアップまたはスケールダウンの操作を行いました。

## <a name="next-steps"></a>次の手順
[クラスターのスケール アウトを管理](manage-cluster-scale-out.md)して、指定したメトリックに基づいてインスタンス数を動的にスケールアウトすることもできます。

クラスターのスケーリングに関する問題が発生したときにサポートが必要な場合は、Azure portal で[サポート要求を開いてください](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

この記事に従って、リソース使用量を監視します。[メトリックを使用した Azure Data Explorer のパフォーマンス、正常性、および使用状況の監視](using-metrics.md)。
