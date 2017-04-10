---
title: "Marketplace のソリューション テンプレート プランのテスト | Microsoft Docs"
description: "Azure Marketplace のソリューション テンプレート プランをテストする方法を理解します。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: ef8f9b5e-b98c-49f3-913f-cdf772c14c12
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2015
ms.author: hascipio; v-divte
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: da1fc4713fd1d832c7ba91226f72cbef63b241bc


---
# <a name="test-your-solution-template-offer-in-staging"></a>ステージングでのソリューション テンプレート プランのテスト
ステージングとは、プランをプライベートの「サンドボックス」にデプロイすることです。そこでは、本番にプッシュする前に、機能をテストおよび検証できます。 ステージングでは、プランはそれをデプロイしたお客様に表示されるのと同じように表示されます。 プランをステージングにプッシュするには認定を受ける必要があります。

プランがステージングされると、 [Azure ポータル](https://portal.azure.com/)でプランを表示したり、テストしたりできるようになります。

次の手順に従って、プランをステージングにプッシュし、 [Azure ポータル](https://portal.azure.com/)でテストしてください。

1. [[発行ポータル]](https://publish.windowsazure.com) > **[ソリューション テンプレート]** タブ > 使用するプラン > **[発行]** > **[ステージングにプッシュ]** の順に移動します。
2. プランのプレビューおよびテストに使用する Azure サブスクリプションの一覧を指定します。
3. 前の手順で使用したサブスクリプション ID を使用して、Azure プレビュー ポータルにサインインします。
4. 以下の点について、Azure プレビュー ポータルで少なくとも 1 回テストを実行します。
   * マーケティング コンテンツが Azure Marketplace に正しく表示されているか
   * トポロジのエンド ツー エンドのデプロイメント
   * パフォーマンス テストとストレス テストの実行
   * トポロジがベスト プラクティスに準拠しているか

## <a name="next-steps"></a>次のステップ
結果に問題がなければ、プラン発行の最終段階である**手順 4** の「[Marketplace へのプランのデプロイ](marketplace-publishing-push-to-production.md)」に進むことができます。 問題があれば、プランに変更を加え、もう一度認定を要求してください。

> [!NOTE]
> マーケティング コンテンツの変更であれば、認定は必要ありません。
> 
> 

公開元のタスクの詳細については、「 [概要: Azure Marketplace へのプランの発行方法](marketplace-publishing-getting-started.md) 」を参照してください。




<!--HONumber=Nov16_HO3-->


