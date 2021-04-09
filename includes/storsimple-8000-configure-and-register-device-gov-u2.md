---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 338332eb90fda9afe7655f9f2ad19ed726419b6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95556208"
---
### <a name="to-configure-and-register-the-device"></a>デバイスを構成して登録するには
1. StorSimple デバイスのシリアル コンソールで Windows PowerShell インターフェイス にアクセスします。 方法については、「 [PuTTY を使用してデバイスのシリアル コンソールに接続する](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console) 」を参照してください。 **必ず手順を正確に実行してください。そうしないと、コンソールにアクセスできません。**
2. 開いたセッションで、**Enter** キーを 1 回押して、コマンド プロンプトを開きます。
3. デバイスに設定する言語を選択するように求められます。 言語を指定し、**Enter** キーを押します。
   
    ![StorSimple によるデバイスの構成および登録 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. 表示されるシリアル コンソール メニューで、オプション 1 の **[Log in with full access]\(フル アクセスによるログイン\)** を選択します。
   
    ![StorSimple によるデバイスの登録 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. 次の手順を実行して、デバイスで必要な最小のネットワーク設定を構成します。
   
   > [!IMPORTANT]
   > これらの構成手順は、デバイスのアクティブ コントローラーで実行する必要があります。 シリアル コンソール メニューでは、バナー メッセージにコントローラーの状態が示されます。 アクティブ コントローラーに接続されていない場合は、切断してアクティブ コントローラーに接続します。
   
   1. コマンド プロンプトにパスワードを入力します。 デバイスの既定のパスワードは **Password1** です。
   2. 次のコマンドを入力します。
      
        `Invoke-HcsSetupWizard`
   3. デバイスのネットワーク設定の構成に役立つセットアップ ウィザードが表示されます。 次の情報を指定します。
      
      * DATA 0 ネットワーク インターフェイスの IP アドレス
      * サブネット マスク
      * Gateway
      * プライマリ DNS サーバーの IP アドレス
      * プライマリ NTP サーバーの IP アドレス
      
      > [!NOTE]
      > サブネット マスクおよび DNS 設定が適用されるまでに数分かかる場合があります。
    
   4. 必要に応じて、Web プロキシ サーバーを構成します。
      
      > [!IMPORTANT]
      > Web プロキシの構成は省略可能ですが、Web プロキシを使用する場合は、ここでのみ構成できることに注意してください。 詳細については、「 [デバイスの Web プロキシの構成](../articles/storsimple/storsimple-8000-configure-web-proxy.md)」を参照してください。
     
6. Ctrl キーを押しながら C キーを押してセットアップ ウィザードを終了します。
8. 次のコマンドレットを実行して、デバイスが Microsoft Azure Government ポータルを示すように設定します (デバイスは既定でパブリックの Microsoft Azure クラシック ポータルを示します)。 これにより、両方のコントローラーが再起動されます。 2 つの PuTTY セッションを使用して両方のコントローラーに同時に接続して、各コント ローラーが再起動されるのを確認できるようにすることをお勧めします。
   
    `Set-CloudPlatform -AzureGovt_US`
   
   確認メッセージが表示されます。 既定値を受け入れます (**Y**)。
9. 次のコマンドレットを実行してセットアップを再開します。
   
    `Invoke-HcsSetupWizard`
   
    ![Resume セットアップ ウィザード](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. ネットワークの設定を受け入れます。 各設定を受け入れた後、検証メッセージが表示されます。
11. セキュリティ上の理由で、デバイス管理者のパスワードは最初のセッション後に期限が切れるため、今すぐパスワードを変更する必要があります。 画面の指示に従って、デバイスの管理者パスワードを入力します。 デバイス管理者の有効なパスワードの長さは、8 ～ 15 文字です。 パスワードには、小文字、大文字、数字、および特殊文字のうち 3 種類の文字を組み合わせる必要があります。
    
    <br/>![StorSimple によるデバイスの登録 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. セットアップ ウィザードの最後の手順では、お使いのデバイスを StorSimple デバイス マネージャー サービスに登録します。 そのためには、 [手順 2. (サービス登録キーの取得)](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key)で取得したサービス登録キーが必要です。 登録キーを指定したら、デバイスが登録されるまでに 2 ～ 3 分かかる場合があります。
    
    > [!NOTE]
    > Ctrl キーを押しながら C キーを押すことで、いつでもセットアップ ウィザードを終了できます。 すべてのネットワーク設定 (Data 0、サブネット マスク、およびゲートウェイの IP アドレス) を入力した場合、エントリは保持されます。
    
    ![StorSimple 登録進行状況](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. デバイスが登録されると、サービス データ暗号化キーが表示されます。 このキーをコピーし、安全な場所に保存しておきます。 **このキーは、StorSimple デバイス マネージャー サービスに追加のデバイスを登録するときに、サービス登録キーと共に必要になります。** このキーの詳細については、「 [StorSimple のセキュリティ](../articles/storsimple/storsimple-8000-security.md) 」を参照してください。
    
    ![StorSimple register device 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > シリアル コンソール ウィンドウからテキストをコピーするには、単にテキストを選択します。 その状態で、クリップボードまたは任意のテキスト エディターに貼り付けることができます。
    > 
    > サービス データ暗号化キーをコピーするときには、**Ctrl キーを押しながら C キー** を押さないでください。 **Ctrl キーを押しながら C キー** を押すと、セットアップ ウィザードが終了します。 その場合、デバイスの管理者パスワードは変更されず、デバイスは既定のパスワードに戻ります。
    
14. シリアル コンソールを終了します。
15. Azure Government ポータルに戻り、次の手順を実行します。
    
    1. StorSimple デバイス マネージャー サービスに移動します。
    2. [**デバイス**] をクリックします。 デバイスの一覧からデプロイするデバイスを特定します。 デバイスが正常にサービスに接続されていることを、その状態を参照して確認します。 デバイスの状態は **"オンライン"** と表示されます。
            
        デバイスの状態が [**オフライン**] の場合、デバイスがオンラインになるまで数分待ちます。
       
        数分待ってもデバイスがまだオフラインである場合は、「[StorSimple デバイスのネットワーク要件](../articles/storsimple/storsimple-8000-system-requirements.md)」で説明されているとおりにファイアウォール ネットワークが構成されていることを確認する必要があります。
       
        ポート 9354 は Service Bus によって StorSimple デバイス マネージャー のサービスとデバイス間の通信に使用されているため、このポートが送信用に開かれていることを確認してください。