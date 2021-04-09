---
title: StorSimple 8000 シリーズのサポート チケットまたはケースを作成する
description: サポート要求をログに記録する方法と StorSimple 8000 シリーズ デバイスでサポート セッションを開始する方法について説明します。
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: c288f59b1cb9a85ed4bc978d1c3fba18ba30b572
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017204"
---
# <a name="contact-microsoft-support"></a>Microsoft サポートに問い合わせる

StorSimple デバイス マネージャーのサービスの概要ブレードには、**新しいサポート要求を記録** する機能が用意されています。 StorSimple ソリューションで問題が発生した場合は、テクニカル サポートに対するサービス要求を作成できます。 サポート エンジニアとのオンライン セッションで、StorSimple デバイスのサポート セッションを開始することが必要になる場合もあります。 この記事で説明する内容は次のとおりです。

* サポート リクエストを作成する方法
* ポータル内からサポート要求ライフ サイクルを管理する方法
* StorSimple デバイスの Windows PowerShell インターフェイスでサポート セッションを開始する方法

サポート要求を作成する前に、 [StorSimple 8000 シリーズのサポート SLA および情報](./storsimple-8000-support-options.md) を確認してください。

## <a name="create-a-support-request"></a>サポート要求の作成

[サポート プラン](https://azure.microsoft.com/support/plans/)に応じて、StorSimple デバイス マネージャー サービスの概要ブレードから、StorSimple デバイスに関する問題のサポート チケットを直接作成できます。 サポート要求を作成するには、次の手順を実行します。

#### <a name="to-create-a-support-request"></a>サポート要求を作成するには

1. StorSimple デバイス マネージャー サービスに移動します。 サービスの概要ブレードの設定で、 **[サポート + トラブルシューティング]** セクションに移動し、 **[新しいサポート要求]** をクリックします。
     
    ![新しいポータルでの MS サポートへの問い合わせ](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. **[新しいサポート要求]** ブレードで、 **[基本]** を選択します。 **[基本]** ブレードで、次の手順を実行します。
   1. **[問題の種類]** ドロップダウン リストで **[技術]** を選択します。
   2. 現在の **サブスクリプション**、**サービス** の種類、および **リソース** (StorSimple デバイス マネージャー サービス) が自動的に選択されています。 
   3. 複数のプランがサブスクリプションに関連付けられている場合は、適切な **サポート プラン** をドロップダウン リストから選択します。 テクニカル サポートを利用するには、有料サポート プランに加入している必要があります。
   4. **[次へ]** をクリックします。

       ![新しいポータルでの MS サポートへの問い合わせ 2](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. **[新しいサポート要求]** ブレードで、 **[手順 2 問題]** を選択します。 **[問題]** ブレードで、次の手順を実行します。
    
    1. **[重大度]** を選択します。
    2. 問題がアプライアンスまたは StorSimple デバイス マネージャー サービスに関連付けられているかどうかを指定します。
    3. この問題の **カテゴリ** を選択し、問題の **詳細** を指定します。
    4. 問題の開始日時を指定します。
    5. **[ファイルのアップロード]** で、フォルダー アイコンをクリックしてサポート パッケージを参照します。
    6. **[診断情報の共有]** チェック ボックスをオンにします。
    7. **[次へ]** をクリックします。

       ![新しいポータルでの MS サポートへの問い合わせ 3](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. **[新しいサポート要求]** ブレードで、 **[手順 3 連絡先情報]** をクリックします。 **[連絡先情報]** ブレードで、次の手順を実行します。

   1. **[連絡先オプション]** で、希望する連絡方法 (電話または電子メール) および言語を指定します。 応答時間が、サブスクリプション プランに基づいて自動的に選択されます。
   2. [連絡先情報] で、名前、メール、オプションの連絡先、国や地域を指定します。 **[今後のサポート要求用に連絡先の変更を保存]** チェック ボックスをオンにします。
   3. **Create** をクリックしてください。
   
       ![新しいポータルでの MS サポートへの問い合わせ 4](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      Microsoft サポートでは、この情報を使用してお客様に連絡し、詳細、診断、および解決策に関する情報を提供します。
      要求を送信した後、その要求を処理するサポート エンジニアから速やかに連絡があります。

## <a name="manage-a-support-request"></a>サポート要求を管理する

サポート チケットを作成したら、ポータル内からチケットのライフサイクルを管理できます。

#### <a name="to-manage-your-support-requests"></a>サポート要求を管理するには

1. ヘルプとサポート ページにアクセスするには、 **[参照] > [ヘルプとサポート]** の順に移動します。

    ![サポート要求を管理する](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. **[ヘルプとサポート]** ブレードにすべてのサポート リクエストの表形式の一覧が表示されます。

    ![サポート要求を管理する 2](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. サポート要求を選択してクリックします。 この要求のステータスと詳細を表示することができます。 この要求をフォローアップする場合は、 **[+ 新しいメッセージ]** をクリックします。

    ![サポート要求を管理する 3](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell でのサポート セッションの開始

StorSimple デバイスで発生した問題のトラブルシューティングを行うには、Microsoft サポート チームと情報交換する必要があります。 Microsoft サポートがサポート セッションを使用してユーザーのデバイスにログオンすることが必要になる場合があります。

サポート セッションを開始するには、次の手順を実行します。

#### <a name="to-start-a-support-session"></a>サポート セッションを開始するには

1. シリアル コンソールから直接デバイスにアクセスするか、リモート コンピューターから telnet セッションを使用してデバイスにアクセスします。 そのためには、「 [PuTTY を使用してデバイスのシリアル コンソールに接続する](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)」の手順を実行します。
2. 開いたセッションで、 **Enter** キーを押して、コマンド プロンプトを開きます。
3. シリアル コンソール メニューで、オプション 1 の **[フル アクセスによるログイン]** を選択します。
4. プロンプトで、次のパスワードを入力します。
   
    `Password1`
5. プロンプトで、次のコマンドを入力します。
   
    `Enable-HcsSupportAccess`
6. 暗号化された文字列が表示されます。 この文字列をメモ帳などのテキスト エディターにコピーします。
7. この文字列を保存し、電子メール メッセージで Microsoft サポートに送信します。

> [!IMPORTANT]
> サポートへのアクセスは、`Disable-HcsSupportAccess` を実行して無効にできます。 また、StorSimple デバイスでは、セッションの開始後 8 時間が経過すると、サポートへのアクセスが無効になります。 サポート セッションを開始した後、StorSimple デバイスの資格情報を変更することをお勧めします。


## <a name="next-steps"></a>次のステップ

[StorSimple 8000 シリーズ デバイスに関連する問題を診断および解決](storsimple-8000-troubleshoot-deployment.md)する方法を確認します