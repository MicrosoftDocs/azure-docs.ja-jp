---
title: Azure Stack Edge、Azure Data Box Gateway のログ サポート チケット | Microsoft Docs
description: Azure Stack Edge または Azure Stack Gateway の注文に関連する問題のサポート リクエストをログに記録する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4d513471e288c1aadbf70b24ef367965a0b69a80
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84339911"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-and-azure-data-box-gateway"></a>Azure Stack Edge と Azure Data Box Gateway のサポート チケットを開く

この記事は、どちらも Azure Stack Edge または Azure Data Box Gateway サービスによって管理されている Azure Stack Edge と Azure Data Box Gateway に適用されます。 お使いのサービスで問題が発生した場合は、テクニカル サポートに対するサービス要求を作成できます。 この記事で説明する内容は次のとおりです。

* サポート リクエストを作成する方法
* ポータル内からサポート要求ライフ サイクルを管理する方法

## <a name="create-a-support-request"></a>サポート要求の作成

サポート要求を作成するには、次の手順を実行します。

1. Azure Stack Edge または Data Box Gateway の注文に移動します。 **[サポート + トラブルシューティング]** セクションに移動し、 **[新しいサポート リクエスト]** を選択します。

2. **[新しいサポート リクエスト]** の **[基本]** タブで、次の手順を実行します。

    1. **[問題の種類]** ドロップダウン リストで **[技術]** を選択します。
    2. **サブスクリプション**を選択します。
    3. **[サービス]** の **[使用中のサービス]** をオンにします。 ドロップダウン リストから **Azure Stack Edge と Azure Stack Gateway** を選択します。
    4. **[リソース]** を選択します。 これは注文の名前に対応します。
    5. 発生している問題の簡単な **[概要]** を入力します。 
    6. **[問題の種類]** を選択します。
    7. 選択した問題の種類に基づき、対応する **[問題のサブタイプ]** を選択します。
    8. **ソリューション >>** を選択します。

        ![基本](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

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
    11. **確認と作成 >>** を選択します。

        ![問題](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. **[確認と作成]** タブで、サポート チケットに関連する情報を確認します。 **［作成］** を選択します 

    ![問題](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    お客様がサポート チケットを作成すると、サポート エンジニアが可能な限り迅速にお客様に連絡し、リクエストを処理します。

## <a name="get-hardware-support"></a>ハードウェアのサポートを受ける

この情報は Azure Stack デバイスにのみ適用されます。 ハードウェアの問題を報告するプロセスは次のとおりです。

1. Azure portal から、ハードウェアの問題に関するサポート チケットを開きます。 **[問題の種類]** で、 **[Azure Stack Hardware]\(Azure Stack ハードウェア\)** を選択します。 **[Hardware failure]\(ハードウェア障害\)** として **[問題のサブタイプ]** を選択します。

    ![ハードウェアの問題](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    お客様がサポート チケットを作成すると、サポート エンジニアが可能な限り迅速にお客様に連絡し、リクエストを処理します。

2. これがハードウェアの問題であると Microsoft サポートが判断した場合、次のいずれかのアクションが行われます。

    * 故障したハードウェア パーツのフィールド交換ユニット (FRU) が送信されます。 現時点でサポートされている FRU は、電源装置およびハード ドライブのみです。
    * FRU のみは翌営業日に交換され、それ以外はすべてフル システム交換 (FSR) の発送が必要です。

3. 現地時間で午後 4:30 (月曜日から金曜日) 前にサポート チケットが発行された場合は、翌営業日にオンサイトの技術者がお客様の所在地に派遣され、FRU またはフル デバイス交換が行われます。

## <a name="manage-a-support-request"></a>サポート要求を管理する

サポート チケットを作成したら、ポータル内からチケットのライフサイクルを管理できます。

### <a name="to-manage-your-support-requests"></a>サポート要求を管理するには

1. ヘルプとサポート ページにアクセスするには、 **[参照] > [ヘルプとサポート]** の順に移動します。

    ![サポート要求を管理する](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)

2. **[ヘルプとサポート]** に **[最近のサポート要求]** の表形式の一覧が表示されます。

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. サポート要求を選択してクリックします。 この要求のステータスと詳細を表示することができます。 この要求をフォローアップする場合は、 **[+ 新しいメッセージ]** をクリックします。

## <a name="next-steps"></a>次のステップ

[Azure Stack Edge に関連する問題のトラブルシューティング](azure-stack-edge-troubleshoot.md)方法について説明します。
[Data Box Gateway に関する問題のトラブルシューティング](data-box-gateway-troubleshoot.md)方法を確認します。
