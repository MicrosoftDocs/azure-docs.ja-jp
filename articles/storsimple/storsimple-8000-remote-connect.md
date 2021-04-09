---
title: StorSimple デバイスにリモート接続する
description: リモート管理用にデバイスを構成する方法と、HTTP または HTTPS を介して StorSimple 用 Windows PowerShell に接続する方法について説明します。
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1da688dfb00b26ca6b561d5aa0fb548c221381c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514568"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>StorSimple 8000 シリーズ デバイスにリモート接続する

## <a name="overview"></a>概要

Windows PowerShell を使用してデバイスにリモート接続できます。 この方法で接続する場合、メニューが表示されません。 (メニューは、デバイスのシリアル コンソールを使用して接続する場合のみ表示されます)。Windows PowerShell リモート処理を使用して、特定の実行空間に接続します。 表示言語を指定することもできます。

Windows PowerShell リモート処理を使用してデバイスを管理する方法の詳細については、「 [Windows PowerShell for StorSimple を使用してデバイスを管理する](storsimple-8000-windows-powershell-administration.md)」をご覧ください。

このチュートリアルでは、リモート管理用にデバイスを構成する方法と、Windows PowerShell for StorSimple に接続する方法について説明します。 HTTP または HTTPS を使用して Windows PowerShell 経由でリモート接続できます。 ただし、StorSimple 用 Windows PowerShell に接続する方法を決定するときは、次の情報をご考慮ください。

* デバイスのシリアル コンソールへの直接接続はセキュリティで保護されていますが、ネットワーク スイッチ経由での接続は保護されていません。 ネットワーク スイッチ経由でデバイスのシリアル コンソールに接続するときは、セキュリティ上のリスクに注意してください。
* HTTP セッション経由での接続は、ネットワーク経由でシリアル コンソールに接続するよりも安全性が高い可能性があります。 これは最も安全な方法ではありませんが、信頼されたネットワークでは容認できます。
* 自己署名証明書を使用する HTTPS セッション経由での接続は、最も安全であり、推奨されるオプションです。

Windows PowerShell インターフェイスにリモート接続できます。 ただし、Windows PowerShell インターフェイス経由での StorSimple デバイスへのリモート アクセスは既定では有効になっていません。 最初にデバイスでリモート管理を有効にし、次にデバイスにアクセスするために使用するクライアントでリモート管理を有効にする必要があります。

この記事で説明する手順は、Windows Server 2012 R2 を実行しているホスト システムで実行されました。

## <a name="connect-through-http"></a>HTTP 経由の接続

HTTP セッション経由の Windows PowerShell for StorSimple への接続は、StorSimple デバイスのシリアル コンソール経由での接続よりも高い安全性を提供します。 これは最も安全な方法ではありませんが、信頼されたネットワークでは容認できます。

Azure Portal またはシリアル コンソールを使用してリモート管理を構成できます。 次の手順から選択してください。

* Azure Portal を使用して HTTP 経由でリモート管理を有効にする
* [シリアル コンソールを使用して HTTP 経由でリモート管理を有効にする](#use-the-serial-console-to-enable-remote-management-over-http)

リモート管理を有効にした後、次の手順に従ってリモート接続用のクライアントを準備します。

* [リモート接続用のクライアントを準備する](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Azure Portal を使用して HTTP 経由でリモート管理を有効にする

Azure Portal で、次の手順に従って、HTTP 経由でのリモート管理を有効にします。

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Azure Portal 経由でリモート管理を有効にするには

1. StorSimple デバイス マネージャー サービスに移動します。 **[デバイス]** を選択し、リモート管理で構成するデバイスを選択してクリックします。 **[デバイスの設定] > [セキュリティ]** に移動します。
2. **[セキュリティの設定]** ブレードで **[リモート管理]** をクリックします。
3. **[リモート管理]** ブレードで、**[リモート管理を有効にする]** を **[はい]** に設定します。
4. これで、HTTP を使用した接続を選択できるようになります。 (既定の設定は HTTPS 経由での接続です)。HTTP が選択されていることを確認します。
   
   > [!NOTE]
   > HTTP 経由での接続は、信頼のおけるネットワークでのみ許容されます。
   
5. **[保存]** をクリックし、確認を求められたら、**[はい]** を選択します。

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>シリアル コンソールを使用して HTTP 経由でリモート管理を有効にする
デバイスのシリアル コンソールで、次の手順を実行して、リモート管理を有効にします。

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>デバイスのシリアル コンソール経由でリモート管理を有効にするには
1. シリアル コンソール メニューで、オプション 1 を選択します。 デバイスのシリアル コンソールの使用の詳細については、「[デバイスのシリアル コンソール経由で Windows PowerShell for StorSimple に接続する](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)」を参照してください。
2. プロンプトで、次のように入力します。 `Enable-HcsRemoteManagement –AllowHttp`
3. HTTP を使用してデバイスに接続した場合のセキュリティの脆弱性についての通知が表示されます。 確認メッセージが表示されたら、「**Y**」と入力して確定します。
4. 次のように入力して、HTTP が有効になっていることを確認します。`Get-HcsSystem`
5. **RemoteManagementMode** フィールドに **HttpsAndHttpEnabled** が表示されていることを確認します。次の図に、PuTTY の設定を示します。
   
     ![シリアル HTTPS と HTTP の有効化](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>リモート接続用のクライアントを準備する
クライアントで、次の手順を実行して、リモート管理を有効にします。

#### <a name="to-prepare-the-client-for-remote-connection"></a>リモート接続用のクライアントを準備するには
1. Windows PowerShell セッションを管理者として開始します。 Windows 10 クライアントを使用している場合、Windows リモート管理サービスは、既定で手動に設定されています。 サービスを起動するには、次を入力してください。

    `Start-Service WinRM`
    
2. 次のコマンドを入力して、StorSimple デバイスの IP アドレスをクライアントの信頼されたホストの一覧に追加します。
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     <*device_ip*> を、デバイスの IP アドレスに置き換えます。 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. 次のコマンドを入力して、デバイスの資格情報を変数に保存します。 
   
    ```
    $cred = Get-Credential
    ```
    
4. 表示されるダイアログ ボックスで、次の操作を行います。
   
   1. ユーザー名を *device_ip\SSAdmin* の形式で入力します。
   2. セットアップ ウィザードでデバイスを構成したときに設定したデバイスの管理者パスワードを入力します。 既定のパスワードは *Password1* です。
5. 次のコマンドを入力して、デバイスの Windows PowerShell セッションを開始します。
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > StorSimple 仮想デバイスで使用する Windows PowerShell セッションを作成するには、`–Port` パラメーターを追加し、StorSimple 仮想アプライアンスのリモート処理で構成したパブリック ポートを指定します。
   
   
この時点で、デバイスに対するアクティブなリモート Windows PowerShell セッションが確立されます。
   
![HTTP を使用した PowerShell リモート処理](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>HTTPS 経由の接続

HTTPS セッション経由での Windows PowerShell for StorSimple への接続は、Microsoft Azure StorSimple デバイスへのリモート接続よりも安全性が高い推奨される方法です。 次の手順は、HTTPS を使用して Windows PowerShell for StorSimple に接続できるようにシリアル コンソールとクライアント コンピューターを設定する方法について説明しています。

Azure Portal またはシリアル コンソールを使用してリモート管理を構成できます。 次の手順から選択してください。

* Azure Portal を使用して HTTPS 経由でリモート管理を有効にする
* [シリアル コンソールを使用して HTTPS 経由でのリモート管理を有効にする](#use-the-serial-console-to-enable-remote-management-over-https)

リモート管理を有効にした後、次の手順に従ってリモート管理用のホストを準備し、リモート ホストからデバイスに接続します。

* [リモート管理用のホストを準備する](#prepare-the-host-for-remote-management)
* [リモート ホストからデバイスに接続する](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Azure Portal を使用して HTTPS 経由でリモート管理を有効にする

Azure Portal で、次の手順に従って、HTTPS 経由でのリモート管理を有効にします。

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Azure Portal 経由で HTTPS 経由でリモート管理を有効にするには

1. StorSimple デバイス マネージャー サービスに移動します。 **[デバイス]** を選択し、リモート管理で構成するデバイスを選択してクリックします。 **[デバイスの設定] > [セキュリティ]** に移動します。
2. **[セキュリティの設定]** ブレードで **[リモート管理]** をクリックします。
3. **[リモート管理を有効にする]** を **[はい]** に設定します。
4. これで、HTTPS を使用した接続を選択できるようになります  (既定の設定は HTTPS 経由での接続です)。HTTPS が選択されていることを確認します。
5. [...] をクリックし、**[リモート管理証明書をダウンロードする]** をクリックします。 このファイルを保存する場所を指定します。 この証明書を、デバイスに接続するために使用するクライアントまたはホスト コンピューターにインストールする必要があります。
6. **[保存]** をクリックし、確認を求められたら **[はい]** をクリックします。

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>シリアル コンソールを使用して HTTPS 経由でのリモート管理を有効にする

デバイスのシリアル コンソールで、次の手順を実行して、リモート管理を有効にします。

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>デバイスのシリアル コンソール経由でリモート管理を有効にするには
1. シリアル コンソール メニューで、オプション 1 を選択します。 デバイスのシリアル コンソールの使用の詳細については、「[デバイスのシリアル コンソール経由で Windows PowerShell for StorSimple に接続する](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)」を参照してください。
2. プロンプトで、次のように入力します。
   
     `Enable-HcsRemoteManagement`
   
    これで、デバイスの HTTPS が有効になります。
3. 次のように入力して、HTTPS が有効になっていることを確認します。 
   
     `Get-HcsSystem`
   
    **RemoteManagementMode** フィールドに **HttpsEnabled** が表示されていることを確認します。次の図に、PuTTY の設定を示します。
   
     ![シリアル HTTPS の有効化](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. `Get-HcsSystem`の出力から、デバイスのシリアル番号をコピーし、後で使用するために保存しておきます。
   
   > [!NOTE]
   > シリアル番号は、証明書の CN 名に対応します。
   
5. 次のように入力して、リモート管理証明書を取得します。 
   
     `Get-HcsRemoteManagementCert`
   
    証明書は、次の画面に似たものになります。
   
    ![リモート管理証明書の取得](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. **-----BEGIN CERTIFICATE-----** から **-----END CERTIFICATE-----** までの証明書の情報をメモ帳などのテキスト エディターにコピーし、.cer ファイルとして保存します  (このファイルは、ホストを準備するときにリモート ホストにコピーします)。
   
   > [!NOTE]
   > 新しい証明書を生成する場合は、`Set-HcsRemoteManagementCert` コマンドレットを使用します。
   
### <a name="prepare-the-host-for-remote-management"></a>リモート管理用のホストを準備する

HTTPS セッションを使用するリモート接続のホスト コンピューターを準備するには、次の手順を実行します。

* [.cer ファイルをクライアントまたはリモート ホストのルート ストアにインポートする](#to-import-the-certificate-on-the-remote-host)。
* [デバイスのシリアル番号をリモート ホストの hosts ファイルに追加する](#to-add-device-serial-numbers-to-the-remote-host)。

この後、上記の各手順について説明します。

#### <a name="to-import-the-certificate-on-the-remote-host"></a>証明書をリモート ホストにインポートするには
1. .cer ファイルを右クリックし、[**証明書のインストール**] を選択します。 これにより、証明書のインポート ウィザードが開始されます。
   
    ![証明書インポート ウィザード (その 1)](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. **[ストアの場所]** で **[ローカル マシン]** を選択し、 **[次へ]** をクリックします。
3. **[証明書をすべて次のストアに配置する]** を選択し、 **[参照]** をクリックします。 リモート ホストのルート ストアに移動し、 **[次へ]** をクリックします。
   
    ![証明書インポート ウィザード (その 2)](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. **[完了]** をクリックします。 インポートが成功したことを通知するメッセージが表示されます。
   
    ![証明書インポート ウィザード (その 3)](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>デバイスのシリアル番号をリモート ホストに追加するには
1. メモ帳を管理者として起動し、\Windows\System32\Drivers\etc にあるホスト ファイルを開きます。
2. 次の 3 つのエントリを hosts ファイルに追加します。**DATA 0 の IP アドレス**、**コントローラー 0 の固定 IP アドレス**、および **コントローラー 1 の固定 IP アドレス**。
3. 前に保存したデバイスのシリアル番号を入力します。 次の図に示すように、IP アドレスにマップします。 Controller 0 と Controller 1 については、シリアル番号 (CN 名) の終わりに **Controller0** と **Controller1** を追加します。
   
    ![hosts ファイルへの CN 名の追加](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. hosts ファイルを保存します。

### <a name="connect-to-the-device-from-the-remote-host"></a>リモート ホストからデバイスに接続する

Windows PowerShell と TLS を使用して、リモート ホストまたはクライアントからデバイス上の SSAdmin セッションに移動します。 この SSAdmin セッションは、デバイスの[シリアル コンソール](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) メニューのオプション 1 に対応します。

Windows PowerShell のリモート接続元にするコンピューターで次の手順を実行します。

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-tls"></a>Windows PowerShell と TLS を使用してデバイス上の SSAdmin セッションに移動するには
1. Windows PowerShell セッションを管理者として開始します。 Windows 10 クライアントを使用している場合、Windows リモート管理サービスは、既定で手動に設定されています。 サービスを起動するには、次を入力してください。

    `Start-Service WinRM`

2. 次のように入力して、クライアントの信頼されたホストにデバイスの IP アドレスを追加します。
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    <*device_ip*> はデバイスの IP アドレスです。例:  
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. 新しい資格情報を作成するには、次のように入力します。
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    <*IP of target device*> はデバイスの DATA 0 の IP アドレスです。たとえば、前に示した hosts ファイルの画面の **10.126.173.90** です。 デバイスの管理者パスワードも指定します。
4. 次のように入力して、セッションを作成します。
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    コマンドレットの -ComputerName パラメーターには、<*ターゲット デバイスのシリアル番号*> を指定します。 このシリアル番号は、リモート ホストの hosts ファイルで、DATA 0 の IP アドレスにマップしたものです。たとえば、次の図に示す **SHX0991003G44MT** です。
5. 型:
   
     `Enter-PSSession $session`
6. 数分待つ必要があります。その後、HTTPS over TLS 経由でデバイスに接続されます。 デバイスに接続されたことを示すメッセージが表示されます。
   
    ![HTTPS と TLS を使用した PowerShell リモート処理](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>次のステップ

* [Windows PowerShell を使用した StorSimple デバイスの管理](storsimple-8000-windows-powershell-administration.md)の詳細を確認する。
* [StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理](storsimple-8000-manager-service-administration.md)の詳細

