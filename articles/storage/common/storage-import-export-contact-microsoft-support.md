---
title: Azure Import/Export ジョブのサポート チケットまたはサポート ケースを作成する | Microsoft Docs
description: Import/Export ジョブに関連する問題のサポート要求をログに記録する方法について説明します。
services: storsimple
author: alkohli
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: alkohli;
ms.subservice: common
ms.openlocfilehash: b01c8a3fe9b0c0856dceb5c05f449504ce8c75d6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58120365"
---
# <a name="open-a-support-ticket-for-an-importexport-job"></a>Import/Export ジョブのサポート チケットを開く

Import/Export サービスで問題が発生した場合は、テクニカル サポートに対するサービス要求を作成できます。 この記事で説明する内容は次のとおりです。

* サポート要求を作成する方法
* ポータル内からサポート要求ライフ サイクルを管理する方法

## <a name="create-a-support-request"></a>サポート要求の作成

サポート要求を作成するには、次の手順を実行します。

1. Import/Export ジョブに移動します。 **[サポート + トラブルシューティング]** セクションに移動し、**[新しいサポート要求]** をクリックします。
     
    ![基本](./media/storage-import-export-contact-microsoft-support/import-export-support1.png)
   
2. **[新しいサポート要求]** で、**[基本]** を選択します。 **[基本]** で次の手順を実行します。
    
    1. **[問題の種類]** ドロップダウン リストで **[技術]** を選択します。
    2. **サブスクリプション**を選択します。
    3. **[サービス]** の **[使用中のサービス]** をオンにします。 ドロップ ダウン リストからは **[Storage Account Management]\(ストレージ アカウントの管理\)**、**[BLOB]**、または **[ファイル]** のいずれかのオプションを選択できます。 
        - **[Storage Account Management]\(ストレージ アカウントの管理\)** を選択した場合は、**[リソース]** と **[サポート プラン]** を選択します。
            ![[Storage Account Management]\(ストレージ アカウントの管理\) を選択する](./media/storage-import-export-contact-microsoft-support/import-export-support3.png)
        - **[BLOB]** を選択した場合は、**[リソース]**、**[コンテナー名]** (省略可能)、および **[サポート プラン]** を選択します。
            ![[BLOB] を選択する](./media/storage-import-export-contact-microsoft-support/import-export-support2.png)
        - **[ファイル]** を選択した場合は、**[リソース]**、**[ファイル共有名]** (省略可能)、および **[サポート プラン]** を選択します。![[ファイル] を選択する](./media/storage-import-export-contact-microsoft-support/import-export-support4.png)
    4. **[次へ]** をクリックします。

3. **[新しいサポート要求]** で、**[手順 2 問題]** を選択します。 **[問題]** で次の手順を実行します。
    
    1. **[重大度]** で **[C - 最小限の影響]** を選択します。 必要に応じてサポートが更新されます。
    2. **[問題の種類]** で **[データ移行]** を選択します。
    3. **[カテゴリ]** で **[インポート - エクスポート]** を選択します。
    4. 問題の **[タイトル]** と **[詳細]** を入力します。
    5. 問題の開始日時を指定します。
    6. アップロードするファイルが他にある場合は、**[ファイルのアップロード]** でフォルダー アイコンをクリックしてファイルを参照します。
    7. **[診断情報の共有]** チェック ボックスをオンにします。
    8. **[次へ]** をクリックします。

       ![問題点](./media/storage-import-export-contact-microsoft-support/import-export-support5.png)

4. **[新しいサポート要求]** で、**[手順 3 連絡先情報]** をクリックします。 **[連絡先情報]** で、次の手順を実行します。

   1. **[連絡先オプション]** で、希望する連絡方法 (電話または電子メール) および言語を指定します。 応答時間が、サブスクリプション プランに基づいて自動的に選択されます。
   2. [連絡先情報] で、名前、電子メール、オプションの連絡先、国を指定します。 **[今後のサポート要求用に連絡先の変更を保存]** チェック ボックスをオンにします。
   3. **Create** をクリックしてください。
   
       ![連絡先情報](./media/storage-import-export-contact-microsoft-support/import-export-support7.png)   

      Microsoft サポートでは、この情報を使用してお客様に連絡し、詳細、診断、および解決策に関する情報を提供します。
      要求を送信した後、その要求を処理するサポート エンジニアから速やかに連絡があります。

## <a name="manage-a-support-request"></a>サポート要求を管理する

サポート チケットを作成したら、ポータル内からチケットのライフサイクルを管理できます。

#### <a name="to-manage-your-support-requests"></a>サポート要求を管理するには

1. ヘルプとサポート ページにアクセスするには、**[参照] > [ヘルプとサポート]** の順に移動します。

    ![サポート要求を管理する](./media/storage-import-export-contact-microsoft-support/manage-support-ticket2.png)   

2. **[ヘルプとサポート]** に **[最近のサポート要求]** の表形式の一覧が表示されます。

    ![サポート要求を管理する](./media/storage-import-export-contact-microsoft-support/manage-support-ticket1.png) 

3. サポート要求を選択してクリックします。 この要求のステータスと詳細を表示することができます。 この要求をフォローアップする場合は、**[+ 新しいメッセージ]** をクリックします。

    ![サポート要求を管理する](./media/storage-import-export-contact-microsoft-support/manage-support-ticket3.png) 


## <a name="next-steps"></a>次の手順

[Import/Export サービスに関連する問題を解決する](storage-import-export-tool-troubleshooting-v1.md)方法について説明します。
