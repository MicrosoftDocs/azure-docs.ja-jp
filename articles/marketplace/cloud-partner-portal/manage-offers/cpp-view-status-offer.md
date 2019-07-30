---
title: Marketplace のオファーの状態を表示する | Azure Marketplace
description: Cloud パートナー ポータルを使用して Azure Marketplace および AppSource Marketplace でオファーの状態を表示する
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: fff89dd8a17aaf6d45462edeaa22f1d2efc8d02b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064320"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Azure Marketplace オファーと AppSource オファーの発行状態を表示する

オファーを作成した後、特に発行プロセス中には、Cloud パートナー ポータルでオファーの状態を表示できます。  全体的な発行状態は、ポータルの [ **[すべてのプラン]** ](../portal-tour/cpp-all-offers-page.md) と [ **[承認]** ](../portal-tour/cpp-approvals-page.md) ページで確認できます。  各オファーには、以下の状態インジケーターのいずれかが表示されます。  

|            Status              |   説明                                                           |
|            ------              |   -----------                                                           |
| **-**                          | オファーは作成されましたが、発行プロセスは開始されていません。            |
| **発行中**        | オファーは、発行プロセスの手順の実行中です。   |
| **発行できませんでした**             | Microsoft による検証またはレビュー中に重大な問題が発見されました。 |
| **発行が取り消されました**           | パブリッシャーがオファーの発行プロセスを取り消しました。  この状態は、マーケットプレースに既存のオファーの掲載停止を示すものではありません。 | 
| **Awaiting publisher sign out (パブリッシャーのサインアウト待ち)** | オファーは Microsoft によるレビューが完了し、現在はパブリッシャーによる最終確認を待っています。 |
| **Delisted (掲載停止)**                   | マーケットプレースに以前に発行されたオファーが削除されました。      | 
|  |  |


## <a name="publishing-status-details"></a>発行状態の詳細 

発行処理中のオファーの詳細な状態は、 **[新しいプラン]** ページの **[状態]** タブに表示されます。  このページには、そのオファーの種類に関するすべての発行手順が一覧表示されます。  *手順の数と具体的な手順はオファーの種類によって変わることが多いので注意してください。*  このページには、Microsoft の検証およびレビューの手順で提起された未解決の問題も表示されます。多くの場合、発行プロセスを進める前にパブリッシャーによるアクションが必要な問題です。  たとえば、次の図は、新しい仮想マシン オファーの **[状態]** タブを示しています。 

![VM オファーの [状態] タブ](./media/vm-offer-pub-steps1.png)

コンサルティング サービスの次の例の **[状態]** タブには、潜在顧客管理設定で報告されたエラーが表示されています。  潜在顧客管理はコンサルティング サービスに必要なので、発行を続行する前にこのエラーを修正する必要があります。

![エラーが表示されているコンサルティング サービスの [状態] タブ](./media/consulting-service-error.png)

Azure アプリケーションの状態の最後の例は、Microsoft の重大なレビューの問題を示しています。  このレビューの問題に関する詳細情報を含む Azure DevOps 項目へのホット リンクが含まれています。  詳細については、[Azure アプリケーション オファーの発行](cpp-publish-offer.md)に関するページを参照してください。

![レビューの問題が表示されている Azure アプリの [状態] タブ](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>次の手順

未解決の問題を修正する場合、オファーの設定を更新する場合は、[オファーを更新する](./cpp-update-offer.md)必要があります。 
