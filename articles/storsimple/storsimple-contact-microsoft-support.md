---
title: "StorSimple 8000 シリーズのサポート チケットを記録する | Microsoft Docs"
description: "サポート要求を作成する方法と StorSimple デバイスでサポート セッションを開始する方法について説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 2ebc20fe-f490-4749-8e43-c9fac86f1676
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli;anbacker
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6b0e763769564eaa191c78df05c008150c9a363f
ms.openlocfilehash: cecc2566b432e897b5eff0c12e66598f0518ed80
ms.lasthandoff: 03/01/2017


---
# <a name="contact-microsoft-support-for-your-storsimple"></a>StorSimple の Microsoft サポートに問い合わせる
Microsoft Azure StorSimple ソリューションで問題が発生した場合は、テクニカル サポートに対するサービス要求を作成できます。 サポート エンジニアとのオンライン セッションで、StorSimple デバイスのサポート セッションを開始することが必要になる場合もあります。 この記事で説明する内容は次のとおりです。

* サポート要求を作成する方法
* StorSimple デバイスの Windows PowerShell インターフェイスでサポート セッションを開始する方法

サポート要求を作成する前に、 [StorSimple 8000 シリーズのサポート SLA および情報](https://msdn.microsoft.com/library/mt433077.aspx) を確認してください。

## <a name="create-a-support-request"></a>サポート要求の作成
サポート要求を作成するには、次の手順を実行します。

#### <a name="to-create-a-support-request"></a>サポート要求を作成するには
1. [Azure クラシック ポータル](https://manage.windowsazure.com/)の右上隅でアカウント名をクリックし、 **[Microsoft サポートに問い合わせる]**をクリックします。
   
    ![クラシック ポータルでの MS サポートへの問い合わせ](./media/storsimple-contact-microsoft-support/Ibiza1.png)
2. 新しい Azure Portal (portal.azure.com) にリダイレクトされます。 **[新しいサポート要求]** タイルをクリックします。
   
    ![新しいポータルでの MS サポートへの問い合わせ](./media/storsimple-contact-microsoft-support/Ibiza2.png)
   
    画面の右側に、 **[新しいサポート要求]** ウィンドウが表示されます。 
   
    ![[新しいサポート要求] ウィンドウ](./media/storsimple-contact-microsoft-support/Ibiza3a.png)
3. **[基本]** ダイアログ ボックスに次の情報を入力します。                                
   
   1. **[問題の種類]** ドロップダウン リストで **[技術]** を選択します。
   2. **[サブスクリプション]** ボックスの一覧で、サブスクリプションを選択します。
   3. **[サービス]** ドロップダウン リストで **[StorSimple]** を選択します。 
   4. ドロップダウン リストで **[サポート プラン]** を選択します。 テクニカル サポートを利用するには、有料サポート プランに加入している必要があります。
4. **[次へ]**をクリックします。 **[問題点]** ダイアログ ボックスが表示されます。
   
    ![[新しいサポート要求] ウィンドウ](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 
5. **[問題点]** ダイアログ ボックスに次の情報を入力します。
   
   1. ドロップダウン リストで **[重要度]** レベルを選択します。
   2. ドロップダウン リストで **[問題の種類]** を選択します。
   3. ドロップダウン リストで **[カテゴリ]** を選択します。 
   4. **[詳細]** ボックスに問題の簡単な説明を入力します。
   5. **[期間]** ボックスに問題が最後に発生したときの日付、時間、タイム ゾーンを指定します。
   6. **[ファイルのアップロード]**でフォルダー アイコンをクリックしてサポート パッケージを参照します。
   7. **[診断情報の共有]** チェック ボックスをオンします。
6. **[次へ]**をクリックします。 **[連絡先情報]** ダイアログ ボックスが表示されます。
   
    ![[新しいサポート要求] ウィンドウ](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 
7. 連絡先情報を入力し、連絡方法 (電話または電子メール) を選択します。 
8. **[今後のサポート要求用に連絡先の変更を保存]** チェック ボックスをオンにします。
9. **[作成]**をクリックします。

要求を送信した後、その要求を処理するサポート エンジニアから速やかに連絡があります。

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell でのサポート セッションの開始
StorSimple デバイスで発生した問題のトラブルシューティングを行うには、Microsoft サポート チームと情報交換する必要があります。 Microsoft サポートがサポート セッションを使用してユーザーのデバイスにログオンすることが必要になる場合があります。 

サポート セッションを開始するには、次の手順を実行します。

#### <a name="to-start-a-support-session"></a>サポート セッションを開始するには
1. シリアル コンソールから直接デバイスにアクセスするか、リモート コンピューターから telnet セッションを使用してデバイスにアクセスします。 そのためには、「 [PuTTY を使用してデバイスのシリアル コンソールに接続する](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)」の手順を実行します。
2. 開いたセッションで、 **Enter** キーを押して、コマンド プロンプトを開きます。
3. シリアル コンソール メニューで、オプション 1 の **[フル アクセスによるログイン]**を選択します。
4. プロンプトで、次のパスワードを入力します。 
   
    `Password1`
5. プロンプトで、次のコマンドを入力します。
   
    `Enable-HcsSupportAccess`
6. 暗号化された文字列が表示されます。 この文字列をメモ帳などのテキスト エディターにコピーします。
7. この文字列を保存し、電子メール メッセージで Microsoft サポートに送信します。 

> [!IMPORTANT]
> サポートへのアクセスは、`Disable-HcsSupportAccess` を実行して無効にできます。 また、StorSimple デバイスでは、セッションの開始後 8 時間が経過すると、サポートへのアクセスが無効になります。 サポート セッションを開始した後、StorSimple デバイスの資格情報を変更することをお勧めします。
> 
> 


