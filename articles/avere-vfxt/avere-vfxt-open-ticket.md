---
title: Avere vFXT for Azure のサポートを受ける方法
description: Avere vFXT for Azure に関するサポート チケットを登録する方法
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 068747fd51779bd8a20636355a9ce2b032f6151d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255364"
---
# <a name="get-help-with-your-system"></a>お使いのシステムでサポートを受ける

お使いの Avere vFXT for Azure でサポートが必要であれば、次のようなさまざまな方法でサポートを受けることができます。

* **Avere vFXT の問題** - Azure portal を使用し、Avere vFXT のサポート チケットを登録します。詳細は[こちらに](#open-a-support-ticket-for-your-avere-vfxt)あります。
* **クォータ** - クォータ関連の問題があるとき、[クォータ増加を要求](#request-a-quota-increase)します。
* **ドキュメントとサンプル** - このドキュメントまたはサンプルに問題が見つかった場合、問題があるページの一番下までスクロールし、 **[フィードバック]** セクションを利用して既存の問題を検索し、必要であれば、新しい問題を登録します。  

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Avere vFXT のサポート チケットを開く

Avere vFXT のデプロイ中または使用中に問題が発生した場合、Azure portal からサポートを要請します。  

以下の手順に従い、お使いのクラスターからのリソースでサポート チケットにタグを付けます。 チケットにタグを付けることで、適切なサポート リソースにチケットを送ることができます。 

1. [https://portal.azure.com](https://portal.azure.com) から **[リソース グループ]** を選択します

   ![Azure portal の左側のメニューのスクリーンショット。[リソース グループ] が赤で囲まれています。](media/avere-vfxt-ticket-rg.png)

1. 問題が発生した vFXT クラスターが含まれるリソース グループを参照し、いずれかの Avere 仮想マシンをクリックします。

    ![Azure portal のリソース グループの "概要" パネルのスクリーンショット。特定の VM が赤で囲まれています。](media/avere-vfxt-ticket-vm.png)

1. VM ページで、左側のパネルの一番下までスクロールし、 **[新しいサポート要求]** をクリックします。

    ![Azure portal の VM ページのスクリーンショット。前のスクリーンショットで赤で囲まれていた VM のページです。 左側のメニューが一番下までスクロールされており、"新しいサポート要求" が赤で囲まれています。](media/avere-vfxt-ticket-request.png)

1. サポート要求のページ 1 で **[すべてのサービス]** をクリックし、 **[ストレージ]** の下を見て **[Avere vFXT]** を選択します。

    ![Azure portal の新しいサポート要求画面のスクリーンショット。ヘッダーに "基本" が選択されており、"サービス" 項目が赤で囲まれています。 "すべてのサービス" ボタンが選択されており、ドロップダウン メニュー フィールドの値が "Avere vFXT" になっています。](media/avere-vfxt-ticket-service.png)

1. ページ 2 で問題の種類と分類を選択します。発生した問題に最も近いものを選択してください。 簡単なタイトルと説明を追加します。問題が発生した日時を含めてください。 

   ![新しいサポート要求画面のスクリーンショット。ヘッダーに "問題" が選択されていますが、この領域に含まれているさまざまなフィールドに入力する必要があります。](media/avere-vfxt-ticket-problem.png)

1. ページ 3 で連絡先情報を入力し、 **[作成]** をクリックします。 指定したメール アドレスに確認とチケット番号が届きます。また、サポート担当者から利用者に連絡が届きます。

## <a name="request-a-quota-increase"></a>クォータの増加を要求する

Avere vFXT for Azure をデプロイするために必要な構成要素については、「[vFXT クラスターのクォータ](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)」を参照してください。 Azure portal から[クォータ増加を要求](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)できます。