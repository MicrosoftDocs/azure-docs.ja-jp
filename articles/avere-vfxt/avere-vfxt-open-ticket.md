---
title: Avere vFXT for Azure のサポートを受ける方法
description: Azure portal で作成したサポート チケットで、Avere vFXT for Azure のデプロイまたは使用で発生した問題に対応する方法について説明します。
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 8159d83624cdd474d91030d5376a3db447beffe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008431"
---
# <a name="get-help-with-your-system"></a>お使いのシステムでサポートを受ける

Avere vFXT for Azure システム関連の問題がある場合にサポートを受ける方法を次に示します。

* **Avere vFXT の問題** - Azure portal を使用し、Avere vFXT のサポート チケットを登録します。詳細は [こちらに](#open-a-support-ticket-for-your-avere-vfxt)あります。
* **クォータ** - クォータ関連の問題があるとき、[クォータ増加を要求](#request-a-quota-increase)します。
* **ドキュメントとサンプル** - このドキュメントまたはサンプルに問題が見つかった場合、問題があるページの一番下までスクロールし、 **[フィードバック]** セクションを利用して既存の問題を検索し、必要であれば、新しい問題を登録します。

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Avere vFXT のサポート チケットを開く

Avere vFXT のデプロイ中または使用中に問題が発生した場合、Azure portal からサポートを要請します。

以下の手順に従い、お使いのクラスターからのリソースでサポート チケットにタグを付けます。 チケットにタグを付けることで、適切なサポート リソースにチケットを送ることができます。

1. [https://portal.azure.com](https://portal.azure.com) から **[リソース グループ]** を選択します 問題が発生した vFXT クラスターが含まれるリソース グループを参照し、いずれかの Avere クラスターの仮想マシンをクリックします。

    ![Azure portal のリソース グループの "概要" パネルのスクリーンショット。特定の VM が赤で囲まれています。](media/avere-vfxt-ticket-vm.png)

1. VM ページで、左側のパネルの一番下までスクロールし、 **[新しいサポート要求]** をクリックします。

    ![Azure portal の VM ページのスクリーンショット。前のスクリーンショットで赤で囲まれていた VM のページです。 左側のメニューが一番下までスクロールされており、[新しいサポート リクエスト] が赤で囲まれています。](media/avere-vfxt-ticket-request.png)

1. サポート リクエストの最初のページで、問題の種類を選択し、正しいサブスクリプションが選択されていることを確認します。

   **[サービス]** の **[すべてのサービス]** をクリックし、 **[ストレージ]** を調べて **[Avere vFXT]** を選択します。

   簡単な概要を追加し、問題の種類を選択します。

    ![Azure portal の新しいサポート リクエスト画面のスクリーンショット。 [基本] タブが選択されています。 画面の項目には、[問題の種類]、[サブスクリプション]、[サービス]、[概要]、および [問題の種類] が含まれます。](media/ticket-basics.png)

   **[次へ]** をクリックして次に進みます。

1. サポート フォームの 2 ページ目には、概要の説明に基づいて問題を解決するための提案が表示されます。 サポート チケットを作成する必要がある場合は、下部の **[次へ]** ボタンをクリックします。

   ![[ソリューション] タブが選択された状態の新しいサポート リクエスト画面のスクリーンショット。 中央のテキスト フィールドには [推奨される解決策] というタイトルと、考えられる解決策の説明が表示されます。](media/ticket-solutions.png)

1. 3 ページ目では、詳細を入力します。たとえば、クラスター、問題が発生した時間、重大度、および連絡方法に関する情報などです。 情報を入力し、下部にある **[次へ]** ボタンをクリックします。

   ![[詳細] タブが選択された [新しいサポート リクエスト] 画面のスクリーンショット。 情報フィールドは、[問題の詳細]、[サポート方法]、および [連絡先情報] のカテゴリに分類されています。](media/ticket-details.png)

1. 最後のページの情報を確認し、 **[作成]** をクリックします。 指定したメール アドレスに確認とチケット番号が届きます。また、サポート担当者から利用者に連絡が届きます。

## <a name="request-a-quota-increase"></a>クォータの増加を要求する

Avere vFXT for Azure をデプロイするために必要な構成要素については、「[vFXT クラスターのクォータ](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)」を参照してください。 Azure portal から[クォータ増加を要求](../azure-portal/supportability/resource-manager-core-quotas-request.md)できます。