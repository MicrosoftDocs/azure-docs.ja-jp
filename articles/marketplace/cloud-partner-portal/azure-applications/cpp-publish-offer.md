---
title: Azure アプリケーション オファーを発行する - Azure Marketplace | Microsoft Docs
description: Azure Marketplace で Azure アプリケーション オファーを発行するプロセスと手順について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pbutlerm
ms.openlocfilehash: fdca47877d4cb2192eef0a26448cd21e8afe4b77
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217939"
---
# <a name="publish-azure-application-offer"></a>Azure アプリケーション オファーを公開する

**[新しいプラン]** ページに情報を指定してオファーを作成した後、オファーを発行できます。 **[発行]** を選択して、プロセスの発行を開始します。

次の図は、"Go Live" へのプランの発行処理の主な手順を示しています。

![オファー発行の手順](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>発行手順の詳しい説明

次の表は各発行手順を説明した一覧です。また、各手順を完了するための推定所要時間を示しています。  "日" 単位の見積時間は営業日として定義されており、週末と祝日を含めていません。

|  **発行ステップ**           | **Time**    | **説明**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 前提条件の検証         | 15 分未満    | プラン情報とプラン設定が有効化されます。                        |
| 影響を受ける収益設定の検証 | 15 分未満  | プランの Azure リソース使用状況属性がチェックされます。             |
| 認定                  | 1 日未満     | オファーが Azure 認定チームによって分析されます。 ウイルス、マルウェア、安全性のコンプライアンス、およびセキュリティの問題についてオファーがスキャンされます。 適格性の条件がすべて満たされていることを確認するため、オファーがチェックされます。 詳細については、「[前提条件](./cpp-prerequisites.md)」を参照してください。 問題が見つかった場合、フィードバックが提供されます。 |
| 体験版の検証          | 2 時間未満   | (省略可能) 体験版が存在する場合、Microsoft では、デプロイおよび複製できることが検証されます。  |
| パッケージ化とリード生成の登録 | 1 時間未満  | オファーの技術資産が顧客の使用のためにパッケージ化され、リード システムが構成されデプロイされます。 |
|  発行元のサインオフ             |  manual    | オファーが稼働状態になる前に、最終的な発行元のレビューと確認が行われます。 オファーはプレビューでご利用いただけるようになりました。  (オファー情報の手順で) 選択されたサブスクリプション内にオファーをデプロイして、すべての要件を満たしていることを確認できます。  オファーを検証した後、オファーが次の手順に進めるように **[起動]** を選択します。 |
| Microsoft によるレビュー                | 7 - 14 日 | Microsoft はお客様の Azure アプリケーションを包括的にレビューし、問題が発見された場合はお客様にメールを送信します。  この手順の長さは、アプリケーションの複雑さ、検出された問題、その問題に対してお客様がどのくらい迅速に対応するかによって変わります。  |
| ライブ                           | 1 日未満 | オファーがリリースされ、指定されたリージョンに複製され、一般公開されます。 |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Cloud パートナー ポータルのオファーの **[状態]** タブで発行プロセスを監視できます。

![Azure アプリ オファーの [状態] タブ](./media/offer-status-tab.png)

発行プロセスが完了した後、オファーが [Microsoft Azure Marketplace のアプリケーション カテゴリ](https://azuremarketplace.microsoft.com/marketplace/apps/)に一覧表示されます。

>[!Note]
>クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。  Microsoft CSP パートナー チャネルを通じたオファーのマーケティングの詳細については、「[Cloud Solution Providers (クラウド ソリューション プロバイダー)](../../cloud-solution-providers.md)」を参照してください。

## <a name="errors-and-review-feedback"></a>エラーとフィードバックの確認

**[状態]** タブには、オファーの発行状態の表示に加え、問題が発生した発行ステップからのエラー メッセージとフィードバックも表示されます。  重大な問題の場合、発行は取り消されます。  その場合は、報告された問題を解決して、オファーを再発行する必要があります。  **Microsoft によるレビュー**のステップには、オファーの広範なレビューとそれに関連する技術資産 (特に Azure Resource Manager テンプレートの場合) が含まれるため、通常、問題は pull request (PR) のリンクとして示されます。  これらの PR を表示してそれに対応する方法については、「[レビュー フィードバックの処理](./cpp-handling-review-feedback.md)」をご覧ください。


## <a name="next-steps"></a>次の手順

発行手順のどこかでエラーが発生した場合は、それらを修正し、オファーを再発行する必要があります。  **Microsoft によるレビュー**のステップで重大な問題が発生した場合は、Microsoft レビュー チームの Azure DevOps リポジトリにアクセスして、[レビューのフィードバックを処理する](./cpp-handling-review-feedback.md)必要があります。

Azure アプリが正常に発行された後は、[既存のオファーを更新](./cpp-update-existing-offer.md)して、ビジネスや技術面の要件の変更を反映することができます。 
