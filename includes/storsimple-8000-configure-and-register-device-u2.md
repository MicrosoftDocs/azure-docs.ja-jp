---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 56514f5dcf4bfe205ef46ee64dcf4dcf638d4f62
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889700"
---
#### <a name="to-configure-and-register-the-device"></a>デバイスを構成して登録するには

1. StorSimple デバイスのシリアル コンソールで Windows PowerShell インターフェイス にアクセスします。 方法については、「 [PuTTY を使用してデバイスのシリアル コンソールに接続する](#use-putty-to-connect-to-the-device-serial-console) 」を参照してください。 **必ず手順を正確に実行してください。そうしないと、コンソールにアクセスできません。**

2. 開いたセッションで、**Enter** キーを 1 回押して、コマンド プロンプトを開きます。

3. デバイスに設定する言語を選択するように求められます。 言語を指定し、**Enter** キーを押します。

4. 表示されるシリアル コンソール メニューで、オプション 1 の **[Log in with full access]\(フル アクセスによるログイン\)** を選択します。
     手順 5. ～ 12. に従って、デバイスで必要な最小のネットワーク設定を構成します。 **これらの構成手順は、デバイスのアクティブ コントローラーで実行する必要があります。** シリアル コンソール メニューでは、バナー メッセージにコントローラーの状態が示されます。 アクティブ コントローラーに接続されていない場合は、切断してアクティブ コントローラーに接続します。

5. コマンド プロンプトにパスワードを入力します。 デバイスの既定のパスワードは **Password1**です。

6. 次のコマンドを入力します。`Invoke-HcsSetupWizard`

7. デバイスのネットワーク設定の構成に役立つセットアップ ウィザードが表示されます。 次の情報を指定します。
   
   * DATA 0 ネットワーク インターフェイスの IP アドレス
   * サブネット マスク
   * Gateway
   * プライマリ DNS サーバーの IP アドレス

   サンプル出力を次に示します。

    ```
        ---------------------------------------------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: 8100-SHX0991003G44MT
        Software Version: 6.3.9600.17759
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Active
        ---------------------------------------------------------------

        Your device needs to be registered with the Microsoft Azure StorSimple Manager service. Please run 'Invoke-HcsSetupWizard' to set up your device.

        Controller0>Invoke-HcsSetupWizard

        Which IP address family would you like to configure on interface Data0?
        [4] IPv4 [6] IPv6 [B] Both (Default is "4"): 4

        Data0 IPv4 address:10.111.111.00
        Data0 IPv4 subnet: 255.255.252.0
        Data0 IPv4 gateway: 10.111.111.11

        IPv4 primary DNS server [10.222.118.154]:10.222.222.111
    ```

    <br>
    上記のサンプル出力では、処理の各手順の後に、システムによってネットワーク設定が検証されています。

     > [!NOTE]
     > サブネット マスクおよび DNS 設定が適用されるまでに数分かかる場合があります。 "Data 0 へのネットワーク接続を確認してください" というエラー メッセージが表示された場合は、アクティブ コントローラーの DATA 0 ネットワーク インターフェイス上の物理ネットワーク接続を確認します。

8. (省略可能) Web プロキシ サーバーを構成します。 Web プロキシの構成は省略可能ですが、 **Web プロキシを使用する場合は、ここでのみ構成できることに注意してください**。 詳細については、「 [デバイスの Web プロキシの構成](../articles/storsimple/storsimple-8000-configure-web-proxy.md)」を参照してください。
9. デバイスのプライマリ NTP サーバーを構成します。 デバイスは時刻を同期してクラウド サービス プロバイダーに対して認証できるようにする必要があるため、NTP サーバーが必要になります。 データ センターからインターネットへの NTP トラフィックがネットワークで許可されていることを確認します。 このトラフィックが許可されない場合は、内部の NTP サーバーを指定します。

    サンプル出力を次に示します。

    ```
        Would you like to configure a web proxy?
        [Y] Yes [N] No (Default is "N"):N

        Primary NTP server [time.windows.com]:time.windows.com

    ```

10. セキュリティ上の理由で、デバイス管理者のパスワードは最初のセッション後に期限が切れるため、今すぐパスワードを変更する必要があります。 画面の指示に従って、デバイスの管理者パスワードを入力します。 デバイス管理者の有効なパスワードの長さは、8 ～ 15 文字です。 パスワードには、小文字、大文字、数字、および特殊文字のうち 3 種類の文字を組み合わせる必要があります。

    ```
        The device administrator password must be between 8 and 15 characters. The password must contain a combination of uppercase letters, lowercase letters, numbers and special characters.
        Administrator Password:********
        Confirm Administrator Password:********
    ```
11. セットアップ ウィザードの最後の手順では、お使いのデバイスを StorSimple デバイス マネージャー サービスに登録します。 そのためには、手順 2. で取得したサービス登録キーが必要です。 登録キーを指定したら、デバイスが登録されるまでに 2 ～ 3 分かかる場合があります。
    
    > [!NOTE]
    > Ctrl キーを押しながら C キーを押すことで、いつでもセットアップ ウィザードを終了できます。 すべてのネットワーク設定 (Data 0、サブネット マスク、およびゲートウェイの IP アドレス) を入力した場合、エントリは保持されます。
    
    サンプル出力を次に示します。

    ```
        The service registration key is available in the StorSimple Manager service.
        Enter service registration key:**************************************
        Device registration is in progress. Please wait.

    ```

12. デバイスが登録されると、サービス データ暗号化キーが表示されます。 このキーをコピーし、安全な場所に保存しておきます。 **このキーは、StorSimple デバイス マネージャー サービスに追加のデバイスを登録するために、サービス登録キーと共に必要になります。** このキーの詳細については、「 [StorSimple のセキュリティ](../articles/storsimple/storsimple-security.md) 」を参照してください。
    
    ![StorSimple register device 7](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup1.png)
    
    > [!NOTE]
    > シリアル コンソール ウィンドウからテキストをコピーするには、単にテキストを選択します。 その状態で、クリップボードまたは任意のテキスト エディターに貼り付けることができます。 サービス データ暗号化キーをコピーするときには、Ctrl キーを押しながら C キーを押さないでください。 Ctrl キーを押しながら C キーを押すと、セットアップ ウィザードが終了します。 その場合、デバイスの管理者パスワードは変更されず、デバイスは既定のパスワードに戻ります。
    
13. シリアル コンソールを終了します。
14. Azure Portal に戻り、次の手順を実行します。
    
    1. StorSimple デバイス マネージャー サービスに移動します。
    2. **[デバイス]** をクリックします。
    3. 表形式のデバイス一覧で、状態を参照して、デバイスが正常にサービスに接続されていることを確認します。 デバイスは**セットアップの準備が完了している**状態になります。
       
        ![StorSimple デバイス ページ](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup2.png)
       
        デバイスの状態が **[セットアップの準備が完了しました]** に変わるのに数分かかる場合があります。
       
        デバイスが一覧に表示されない場合は、[StorSimple デバイスのネットワーク要件](../articles/storsimple/storsimple-8000-system-requirements.md)に関するページで説明されているとおりにファイアウォール ネットワークが構成されていることを確認する必要があります。 ポート 9354 は Service Bus によって StorSimple デバイス マネージャー のサービスとデバイス間の通信に使用されているため、このポートが送信用に開かれていることを確認してください。

