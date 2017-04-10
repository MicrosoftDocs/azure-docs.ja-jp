---
title: "StorSimple サポート パッケージの作成 | Microsoft Docs"
description: "StorSimple デバイスのサポート パッケージを作成、暗号化解除、編集する方法について説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: eac76f5f-5db1-4c92-af8c-54053b91e66c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 42b245d4e205dffda7ec62199ced3834c278a466


---
# <a name="create-and-manage-a-storsimple-support-package"></a>StorSimple サポート パッケージを作成および管理する
## <a name="overview"></a>Overview
StorSimple サポート パッケージは、Microsoft サポートが StorSimple デバイスの問題を解決するときに役立つ、すべての関連するログを収集する使いやすいメカニズムです。 収集したログが暗号化され、圧縮されます。

このチュートリアルでは、サポート パッケージを作成および管理するための手順を取り上げています。

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>Azure クラシック ポータルでサポート パッケージを作成およびアップロードする
サポート パッケージを作成し、Microsoft サポート サイトにアップロードするには、Azure クラシック ポータルのサービスの **[メンテナンス]** ページを使用します。

> [!NOTE]
> アップロードには、サポートのパスキーが必要です。 パスキーは、サポート エンジニアが電子メールでお知らせします。
> 
> 

暗号化され、圧縮されたサポート パッケージ (.cab ファイル) が作成され、サポート サイトにアップロードされます。 サポート エンジニアはそのパッケージをサポート サイトから取得し、問題を解決します。

サポート パッケージを作成するには、クラシック ポータルで次の手順を実行します。

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Azure クラシック ポータルでサポート パッケージを作成するには
1. **[デバイス]** > **[メンテナンス]** を選択します。
2. **[サポート パッケージ]** セクションで **[サポート パッケージの作成とアップロード]** を選択します。
3. **[サポート パッケージの作成とアップロード]** ダイアログ ボックスで、次の操作を実行します。
   
    ![サポート パッケージの作成](./media/storsimple-create-manage-support-package/IC740923.png)
   
   * **[サポート パスキー]** テキストボックスにパスキーを入力します。 Microsoft サポート エンジニアから、電子メールでこのパスキーが送信されます。
   * サポート パッケージを Microsoft サポート サイトへ自動的にアップロードすることに同意するチェック ボックスをオンにします。
   * チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-create-manage-support-package/IC740895.png) の順に選択します。

## <a name="manually-create-a-support-package"></a>サポート パッケージを手動で作成する
場合によっては、StorSimple 用 Windows PowerShell でサポート パッケージを手動で作成する必要があります。 次に例を示します。

* Microsoft サポートにログ ファイルを送信する前に、ログから機密情報を削除する必要がある場合。
* 接続の問題があり、パッケージをアップロードできない場合。

手動で生成されたサポート パッケージを電子メールで Microsoft サポートと共有できます。 StorSimple 用 Windows PowerShell でサポート パッケージを作成するには、次の手順を実行します。

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell でサポート パッケージを作成するには
1. StorSimple デバイスへの接続に使用されるリモート コンピューターで、次のコマンドを入力し、Windows PowerShell セッションを管理者として起動します。
   
    `Start PowerShell`
2. Windows PowerShell セッションで、次の手順を実行し、デバイスの SSAdmin コンソールに接続します。
   
   * コマンド プロンプトに、次のコマンドを入力します。
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   * 表示されたダイアログ ボックスで、デバイス管理者のパスワードを入力します。 既定のパスワードは次のとおりです。
     
      `Password1`
     
      ![PowerShell の [資格情報] ダイアログ ボックス](./media/storsimple-create-manage-support-package/IC740962.png)
   * **[OK]**を選択します。
   * コマンド プロンプトに、次のコマンドを入力します。
     
      `Enter-PSSession $MS`
3. 表示されたセッションに、適切なコマンドを入力します。
   
   * ネットワーク共有がパスワードで保護されている場合は、次のコマンドを入力します。
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`
     
       パスワード、ネットワーク共有フォルダーへのパス、および暗号化パスフレーズ (サポート パッケージが暗号化されているため) を入力するよう求められます。 指定したフォルダーにサポート パッケージが作成されます。
   * パスワードで保護されていない共有の場合、 `-Credential` パラメーターは必要ありません。 次のように入力します。
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`
     
       サポート パッケージは、指定したネットワーク共有フォルダー内の両方のコントローラー用に作成されます。 これは、トラブルシューティングの際に Microsoft サポートに送信できる、暗号化された圧縮ファイルです。 詳細については、「 [Microsoft サポートに問い合わせる](storsimple-contact-microsoft-support.md)」を参照してください。

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Export-HcsSupportPackage コマンドレットのパラメーター
Export-HcsSupportPackage コマンドレットには、次のパラメーターを使用できます。

| パラメーター | 必須/省略可能 | Description |
| --- | --- | --- |
| `-Path` |必須 |サポート パッケージが配置されるネットワーク共有フォルダーの場所を指定するためにします。 |
| `-EncryptionPassphrase` |必須 |サポート パッケージの暗号化に使用するパスフレーズを指定するためにします。 |
| `-Credential` |省略可能 |ネットワーク共有フォルダーのアクセス資格情報を指定するために使用します。 |
| `-Force` |省略可能 |暗号化パスフレーズの確認手順をスキップするために使用します。 |
| `-PackageTag` |省略可能 |サポート パッケージが配置される、 *Path* で指定した場所の下のディレクトリを指定するために使用します。 既定値は [デバイス名]-[現在の日時 (yyyy-MM-dd-HH-mm-ss)] です。 |
| `-Scope` |省略可能 |両方のコントローラーのサポート パッケージを作成する場合は、 **Cluster** (既定値) と指定します。 現在のコントローラーのみのパッケージを作成する場合は、 **Controller**を指定します。 |

## <a name="edit-a-support-package"></a>サポート パッケージを編集する
サポート パッケージを生成した後に、必要に応じて機密情報を削除するようにパッケージを編集します。 機密情報には、ボリューム名、デバイスの IP アドレス、ログ ファイルのバックアップ名などが含まれます。

> [!IMPORTANT]
> 編集できるのは、StorSimple 用 Windows PowerShell を使って生成されたサポート パッケージのみです。 Azure クラシック ポータルで StorSimple Manager サービスを使用して作成したパッケージを編集することはできません。
> 
> 

サポート パッケージを Microsoft サポート サイトにアップロードする前に編集するには、まずサポート パッケージの暗号化を解除し、ファイルを編集して、もう一度暗号化する必要があります。 次の手順に従います。

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell でサポート パッケージを編集するには
1. 「 [StorSimple 用 Windows PowerShell でサポート パッケージを作成する](#to-create-a-support-package-in-windows-powershell-for-storsimple)」の説明に従って、サポート パッケージを生成します。
2. [スクリプトをダウンロード](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) します。
3. Windows PowerShell モジュールをインポートします。 スクリプトをダウンロードしたローカル フォルダーへのパスを指定します。 モジュールをインポートするには、次のコマンドを入力します。
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. すべてのファイルが圧縮および暗号化された *.aes* ファイルです。 ファイルの圧縮と暗号化を解除するには、次のコマンドを入力します。
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    すべてのファイルに実際のファイルの拡張子が表示されます。
   
    ![サポート パッケージの編集](./media/storsimple-create-manage-support-package/IC750706.png)
5. 暗号化パスフレーズの入力を求められたら、サポート パッケージの作成時に使用したパスフレーズを入力します。
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. ログ ファイルが格納されているフォルダーに移動します。 ログ ファイルが展開され、暗号化が解除されているので、元のファイル拡張子が表示されます。 これらのファイルを変更し、ユーザー固有の情報 (ボリューム名やデバイスの IP アドレスなど) があれば削除して、ファイルを保存します。
7. ファイルを閉じて gzip で圧縮し、AES-256 で暗号化します。 これは、サポート パッケージをネットワーク経由で転送するときの速度とセキュリティのためです。 ファイルを圧縮して暗号化するには、次のコマンドを入力します。
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![サポート パッケージの編集](./media/storsimple-create-manage-support-package/IC750707.png)
8. 入力を求められたら、変更したサポート パッケージの暗号化パスフレーズを入力します。
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. 要求されたときに Microsoft サポートと共有できるように、新しいパスフレーズを書き留めます。

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>例: パスワードで保護された共有でサポート パッケージ内のファイルを編集する
次に、サポート パッケージの復号化、編集、再暗号化の例を示します。

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>次のステップ
* [サポート パッケージとデバイスのログを使用してデバイスのデプロイをトラブルシューティングする方法](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting)
* [StorSimple Manager サービスを使用した StorSimple デバイスの管理方法](storsimple-manager-service-administration.md)




<!--HONumber=Nov16_HO3-->


