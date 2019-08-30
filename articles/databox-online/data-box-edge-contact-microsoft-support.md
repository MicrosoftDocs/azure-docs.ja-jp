---
title: Azure Data Box Edge、Azure Data Box Gateway のログ サポート チケット | Microsoft Docs
description: Data Box Edge または Data Box Gateway の注文に関連する問題のサポート リクエストをログに記録する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: ''
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 9db119733bb80d9ae1462b70c3ffe555b2493405
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361327"
---
# <a name="open-a-support-ticket-for-azure-data-box-edge-and-azure-data-box-gateway"></a>Azure Data Box Edge と Azure Data Box Gateway のサポート チケットを開く

この記事は、どちらも Azure Data Box Edge/Azure Data Box Gateway サービスによって管理されている Azure Data Box Edge と Azure Data Box Gateway に適用されます。 お使いのサービスで問題が発生した場合は、テクニカル サポートに対するサービス要求を作成できます。 この記事で説明する内容は次のとおりです。

* サポート リクエストを作成する方法
* ポータル内からサポート要求ライフ サイクルを管理する方法

## <a name="create-a-support-request"></a>サポート要求の作成

サポート要求を作成するには、次の手順を実行します。

1. Data Box Edge または Data Box Gateway の注文に進みます。 **[サポート + トラブルシューティング]** セクションに移動し、 **[新しいサポート リクエスト]** を選択します。
   
2. **[新しいサポート リクエスト]** の **[基本]** タブで、次の手順を実行します。
    
    1. **[問題の種類]** ドロップダウン リストで **[技術]** を選択します。
    2. **サブスクリプション**を選択します。
    3. **[サービス]** の **[使用中のサービス]** をオンにします。 ドロップ ダウン リストから、 **[Data Box Edge and Data Box Gateway]\(Data Box Edge と Data Box Gateway\)** を選択します。
    4. **[リソース]** を選択します。 これは注文の名前に対応します。
    5. 発生している問題の簡単な **[概要]** を入力します。 
    6. **[問題の種類]** を選択します。
    7. 選択した問題の種類に基づき、対応する **[問題のサブタイプ]** を選択します。
    8. **[次へ:ソリューション >>]** を選択します。

        ![基本](./media/data-box-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. **[詳細]** タブで、次の手順を実行します。
    
    1. 問題の開始日時を指定します。
    2. 問題の **[説明]** を入力します。
    3. アップロードするファイルが他にある場合は、 **[ファイルのアップロード]** でフォルダー アイコンを選択してファイルを参照します。
    4. **[診断情報の共有]** チェック ボックスをオンにします。
    5. お客様のサブスクリプションに基づいて、 **[サポート プラン]** が自動的に入力されます。
    6. ドロップダウン リストから **[重大度]** を選択します。
    7. **[ご希望の連絡方法]** を選択します。
    8. **[応答時間]** は、サブスクリプション プランに基づいて自動的に選択されます。
    9. サポートにご希望の言語を指定します。
    10. **[連絡先情報]** で、名前、メール、電話、オプションの連絡先、国や地域を指定します。 Microsoft サポートでは、この情報を使用してお客様に連絡し、詳細、診断、および解決策に関する情報を提供します。 
    11. **[次へ:確認と作成 >>]** を選択します。

        ![問題点](./media/data-box-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. **[確認と作成]** タブで、サポート チケットに関連する情報を確認します。 **作成** を選択します。 

    ![問題点](./media/data-box-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    お客様がサポート チケットを作成すると、サポート エンジニアが可能な限り迅速にお客様に連絡し、リクエストを処理します。

## <a name="get-hardware-support"></a>ハードウェアのサポートを受ける

この情報は Data Box Edge デバイスにのみ適用されます。 ハードウェアの問題を報告するプロセスは次のとおりです。

1. Azure portal から、ハードウェアの問題に関するサポート チケットを開きます。 **[問題の種類]** で、 **[Data Box Edge Hardware]\(Data Box Edge ハードウェア\)** を選択します。 **[Hardware failure]\(ハードウェア障害\)** として **[問題のサブタイプ]** を選択します。 

    ![ハードウェアの問題](./media/data-box-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    お客様がサポート チケットを作成すると、サポート エンジニアが可能な限り迅速にお客様に連絡し、リクエストを処理します。 

2. これがハードウェアの問題であると Microsoft サポートが判断した場合、次のいずれかのアクションが行われます。 

    - 故障したハードウェア パーツのフィールド交換ユニット (FRU) が送信されます。 現時点では、電源装置が唯一サポートされている FRU です。 
    - その他のパーツの故障に対しては、Microsoft はフル システム交換 (FSR) またはデバイス スワップを行います。

3. 現地時間で午後 4:30 (月曜日から金曜日) 前にサポート チケットが発行された場合は、翌営業日にオンサイトの技術者がお客様の所在地に派遣され、FRU またはフル デバイス交換が行われます。

## <a name="manage-a-support-request"></a>サポート要求を管理する

サポート チケットを作成したら、ポータル内からチケットのライフサイクルを管理できます。

#### <a name="to-manage-your-support-requests"></a>サポート要求を管理するには

1. ヘルプとサポート ページにアクセスするには、 **[参照] > [ヘルプとサポート]** の順に移動します。

    ![サポート要求を管理する](./media/data-box-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)   

2. **[ヘルプとサポート]** に **[最近のサポート要求]** の表形式の一覧が表示されます。

    <!--[Manage support requests](./media/data-box-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. サポート要求を選択してクリックします。 この要求のステータスと詳細を表示することができます。 この要求をフォローアップする場合は、 **[+ 新しいメッセージ]** をクリックします。

   
## <a name="next-steps"></a>次の手順

[Data Box Edge に関する問題のトラブルシューティング](data-box-edge-troubleshoot.md)方法を確認します。
[Data Box Gateway に関する問題のトラブルシューティング](data-box-gateway-troubleshoot.md)方法を確認します。
