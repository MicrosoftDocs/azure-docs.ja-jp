---
title: "Azure Marketplace へのプランのデプロイ | Microsoft Docs"
description: "プラン (仮想マシン イメージ、開発者向けサービス、データ サービスなど) を Azure Marketplace にデプロイする方法を手順に従って学習します。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 12dc81642905cd9449a1032c7ab57298e6b69ba8


---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Azure Marketplace へのプランのデプロイ
プラン (テスト済みの顧客シナリオやマーケティング コンテンツなど) の内容に問題がなく、発行の準備が整ったら、**[発行]** タブで**本番へのプッシュ**を要求します。  

1. 発行ポータルのチュートリアル ページの 4 つの手順が完了済みで、緑色で表示されている必要があります。 Virtual Machine プランについては、次のガイドラインに従っていることを確認します。
   
    ![図][img-pubportal-walkthru-checked]
2. 左側のリストから **[発行]** タブを選択します。
   
    ![図][img-pubportal-menu-publish]
3. **[Request approval to push to production (本番にプッシュするための承認を要求)]**をクリックします。 要求が行われると、承認チームが最終レビューを実行し、その後、プランが Azure Marketplace で使用できるようになります。
   
    ![図][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> Virtual Machines の場合、[Request approval to push to production (本番にプッシュするための承認を要求)] をクリックすると、次の手順が見えないところで実行されます。 各手順の進行状況は、発行ポータルの [発行] タブで確認できます。 このページは (ステータスが "表示済み" になるまで) 定期的にチェックして、ご自身で修正する必要があるエラーがないかどうかを確認してください。
> 
> * 本番要求は、まず、VHD を検証する認定チームに送信されます。 ただし、既に表示されているプランを更新している場合で、マーケティングの変更のみのときは、認定手順はスキップされます。
> * 次の手順で、要求は、プランのマーケティングコンテンツを確認するコンテンツ検証チームに送信されます。
> * 前の手順が成功した場合、プランは運用環境で承認されます。 この時点で、発行ポータルでのステータスは "表示済み" になります。 ただし、この "表示済み" ステータスは、プロセスが完了したことを意味するものではありません。 プランを Azure Marketplace で使用できるようにするには、以下の手順を完了する必要があります。
> * 前の手順によって本番環境でプランが承認されると、すべての Azure データセンターへのプランのレプリケーションが開始されます。 一般的に、レプリケーションの所要時間は 24 ～ 48 時間ですが、VHD のサイズによっては最大で 1 週間かかる場合があります。 ただし、既に表示されているプランを更新している場合で、マーケティングの変更のみのときは、レプリケーションはもっと早く完了します。
> * レプリケーションが完了したら、Azure Marketplace でプランが使用できるようになります。
> 
> **[ドラフト]** 状態の間は、プランをいつでも削除できます (つまり、**ステージングにプッシュ**や**本番にプッシュ**されていない状態)。 ドラフトを削除するページの下部にある **[履歴]** タブの **[ドラフトの破棄]** ボタンをクリックします。
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>すべての Virtual Machine プラン用の運用環境チェックリスト
* Microsoft Azure Certified パートナーであることを確認します
* [SKUs (SKU)] タブで、[Hide this SKU from the Marketplace because it should always be bought via a solution template (この SKU は、常にソリューション テンプレートを介して購入する必要があるため、Marketplace に表示しない)] オプションは、SKU がソリューション テンプレートに含まれる場合にのみ [はい] とマークします。 それ以外の場合、このオプションは常に [いいえ] としてマークします。
* 重要: SKU が表示されたら、SKU の可視性の設定は変更しないでください。 この機能はサポートされていません。
* ロゴが以下で説明する Azure Marketplace ロゴ ガイドラインに従っていることを確認します。
* プランと SKU の説明は同じにしないでください。
* SKU のタイトルとプランの長い概要は、同じにしないでください。
* SKU のタイトルとプランの概要は、同じにしないでください。
* 複数の SKU が含まれるプランの場合、その SKU のタイトルは同じにしないでください。

**Azure Marketplace のロゴのガイドライン**

* Azure の設計には簡単なカラー パレットがあります。 ロゴのプライマリ カラーとセカンダリ カラーの数は少なくしてください。
* Azure ポータルのテーマの色は白黒です。 したがって、この色を、ロゴの背景色として使用しないでください。 Azure ポータルでロゴが目立つ色を背景色として使用します。 背景色としてはシンプルなプライマリ カラーをお勧めします。 透明な背景を使用している場合は、ロゴ/テキストが白または黒ではないことを確認します。
* ロゴではグラデーションの背景を使用しないでください。
* ロゴにはテキストを使用しないでください (会社またはブランドの名前であっても)。
* ロゴのルック アンド フィールは "フラット" にする必要があり、グラデーションは避ける必要があります。
* ロゴは拡大しないでください。

**Hero ロゴに関する追加のガイドライン:**

* Hero ロゴはオプションです。 パブリッシャーは、Hero ロゴをアップロードしなくてもかまいません。 **ただし、アップロードされたら、Hero アイコンは発行ポータルから削除できません。その時点で、パートナーは、Hero アイコンの Azure Marketplace ガイドラインに従う必要があります。ガイドラインに従っていない場合、プランは運用環境に対して承認されません。**
* 発行者表示名、SKU のタイトル、およびプランの長い概要は、白のフォント色で表示されます。 このため、Hero アイコンの背景色には明るい色を使用しないでください。 黒、白、および透明は Hero アイコンの背景では使用できません。
* プランが表示されると、パブリッシャーの表示名、SKU のタイトル、プランの長い概要、作成ボタンが、Hero ロゴ内にプログラムによって埋め込まれます。 したがって、Hero ロゴのデザイン時にはテキストを入力しないでください。 右側にスペースを空けておくだけで、テキスト (つまり、 パブリッシャーの表示名、SKU のタイトル、プランの長い概要) は、その場所にプログラムによって挿入されます。 右側のテキスト用の空のスペースは 415 x 100 です (また、左から 370 ピクセルだけオフセットされます)。

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>既に表示されている Virtual Machine プラン用の追加の運用環境チェックリスト
* 会社からのプランで同じ名前のものがあるかどうかを確認します。 同じ名前のプランがある場合は、重複するプランを新しく作成するのではなく、既存のプランに新しいバージョンの SKU を追加します。
* 同じ SKU の 2 つのバージョン間でデータ ディスクを変更しないでください。
* 表示されている SKU の価格を変更すると、既存のお客様への請求に影響を及ぼすため、Azure Marketplace ではこの価格変更はサポートされていません。 SKU が使用可能なリージョンで、表示されている SKU の価格を変更していないことを確認します。 ただし、新しい SKU を追加したり、既存の SKU に新しいリージョンを追加したりすることはできます。

## <a name="next-steps"></a>次のステップ
プランが公開されたら、顧客シナリオをテストし、ステージング環境でテストおよび検証したとおりに、運用環境でもすべてのコントラクトと機能が適切に機能することを検証します。

## <a name="see-also"></a>関連項目
* [Microsoft Azure Marketplace へのプランの発行方法](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png



<!--HONumber=Dec16_HO2-->


