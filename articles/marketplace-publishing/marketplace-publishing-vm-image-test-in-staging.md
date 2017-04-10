---
title: "Marketplace の VM プランのテスト | Microsoft Docs"
description: "Azure Marketplace の VM イメージをテストする方法を理解します。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ad057a9129156c0ad5094a73c0da2899fc2a3812


---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>ステージングでの Azure Marketplace の VM プランのテスト
ステージングとは、SKU をプライベートの「サンドボックス」にデプロイすることです。そこから、Marketplace にデプロイする前に、機能をテストおよび検証できます。 ステージングでは、SKU はそれをデプロイしたお客様に表示されるのと同じように表示されます。 VM イメージをステージングにプッシュするには認定を受ける必要があります。

## <a name="step-1-push-your-offer-to-staging"></a>手順 1: ステージングへのプランのプッシュ
1. **[発行]** タブで **[ステージングにプッシュ]** をクリックします。
   
    ![図](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. 発行ポータルでエラーが通知される場合は、それらを修正します。
3. **[ステージングされたプランにアクセスできるユーザー]** ダイアログ ボックスで、 [Azure プレビュー ポータル](https://portal.azure.com)でプランをプレビューするために使用する Azure サブスクリプションのリストを入力します。
   
   > [!NOTE]
   > Virtual Machines テンプレートとソリューション テンプレートの場合、タイプが CSP、DreamSpark、または Azure イン オープン プランのサブスクリプションは、ホワイトリストに登録 **しない** でください。
   > 
   > 

    > Virtual Machines の場合、 **[PUSH TO STAGING (ステージングにプッシュ)]**をクリックすると、次の手順が見えないところで実行されます。 各手順の進行状況は、発行ポータルの [発行] タブで確認できます。 このページは (ステータスが "ステージング済み" になるまで) 定期的にチェックして、ご自身で修正する必要があるエラーがないかどうかを確認してください。

    > - ステージング要求は、まず、VHD を検証する認定チームに送信されます。 ただし、マーケティングの変更のみの場合、認定手順はスキップされます。
    > - 認定が完了したら、すべての Azure データセンターへのプランのレプリケーションが開始されます。 一般的に、レプリケーションの所要時間は 24 ～ 48 時間ですが、VHD のサイズによっては最大で 1 週間かかる場合があります。 ただし、マーケティングの変更だけの場合、レプリケーションはもっと早く完了します。
    > - レプリケーションが完了したら、 [Azure ポータル](http:/portal.azure.com)でプランが使用できるようになります。 この時点で、発行ポータルでのステータスは "ステージング済み" になります。 ステージングされたプランを [Azure ポータル](http:/portal.azure.com) に表示するには、プランのステージングで使用されたサブスクリプションに関連付けられている、電子メール ID を使う必要があります。

1. 前の手順で示されている上記のいずれかの Azure サブスクリプションを使用して、 [Azure プレビュー ポータル](https://portal.azure.com) にサインインします。
2. プランを検索して、VM イメージについて以下の点を検証します。
   
   * マーケティング コンテンツが Marketplace に正しく表示されていることを確認します。
   * VM イメージのエンド ツー エンドのデプロイメント。
     
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> プランは、本番にプッシュする準備が整っている発行ポータル (**[発行]** タブで **[Request approval to push to production (本番にプッシュするための承認を要求)]** をクリック) を介して Microsoft に通知するまで、ステージング状態のままです。 この時期が、プラン表示への移行の準備段階で必要な項目をチームのすべてのメンバーにチェックさせるのに最適です。
> 
> ステージング プラットフォームは、発行元がプレビュー モードでプランをテストするために設計されています。 このプラットフォームは、商用目的では使用しないことを強くお勧めします。
> 
> 

## <a name="next-steps"></a>次のステップ
これで、プランは「ステージングされた」状態になり、その機能とマーケティング コンテンツがテストされたので、最終発行段階である **手順 4**の「 [Azure Marketplace へのプランのデプロイ](marketplace-publishing-push-to-production.md)」に進むことができます。

## <a name="see-also"></a>関連項目
* [Microsoft Azure Marketplace へのプランの発行方法](marketplace-publishing-getting-started.md)




<!--HONumber=Nov16_HO3-->


