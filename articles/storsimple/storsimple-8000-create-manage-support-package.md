---
title: StorSimple 8000 シリーズ　サポート パッケージの作成
description: StorSimple 8000 シリーズ デバイスのサポート パッケージを作成、暗号化解除、編集する方法について説明します。
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4a847b273472ecc9d2aaa3993ec9d88aa46f2e7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017170"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>StorSimple 8000 シリーズのサポート パッケージの作成と管理

## <a name="overview"></a>概要

StorSimple サポート パッケージは、Microsoft サポートが StorSimple デバイスの問題を解決するときに役立つ、すべての関連するログを収集する使いやすいメカニズムです。 収集したログが暗号化され、圧縮されます。

このチュートリアルでは、StorSimple 8000 シリーズ デバイスのサポート パッケージを作成および管理するための手順を取り上げています。 StorSimple Virtual Array を使用する場合は、「[ログ パッケージの生成](storsimple-ova-web-ui-admin.md#generate-a-log-package)」に進んでください。

## <a name="create-a-support-package"></a>サポート パッケージを作成する

場合によっては、StorSimple 用 Windows PowerShell でサポート パッケージを手動で作成する必要があります。 次に例を示します。

* Microsoft サポートにログ ファイルを送信する前に、ログから機密情報を削除する必要がある場合。
* 接続の問題があり、パッケージをアップロードできない場合。

手動で生成されたサポート パッケージを電子メールで Microsoft サポートと共有できます。 StorSimple 用 Windows PowerShell でサポート パッケージを作成するには、次の手順を実行します。

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell でサポート パッケージを作成するには

1. StorSimple デバイスへの接続に使用されるリモート コンピューターで、次のコマンドを入力し、Windows PowerShell セッションを管理者として起動します。
   
    `Start PowerShell`
2. Windows PowerShell セッションで、次の手順を実行し、デバイスの SSAdmin コンソールに接続します。
   
   1. コマンド プロンプトに、次のコマンドを入力します。
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. 表示されたダイアログ ボックスで、デバイス管理者のパスワードを入力します。 既定のパスワードは _Password1_ です。
     
      ![PowerShell の [資格情報] ダイアログ ボックス](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. **[OK]** を選択します。
   4. コマンド プロンプトに、次のコマンドを入力します。
     
      `Enter-PSSession $MS`
3. 表示されたセッションに、適切なコマンドを入力します。
   
   * ネットワーク共有がパスワードで保護されている場合は、次のコマンドを入力します。
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       パスワード、および暗号化パスフレーズを入力するよう求められます (サポート パッケージが暗号化されているため)。 既定のフォルダー (デバイス名に現在の日付と時刻が追加されたもの) にサポート パッケージが作成されます。
   * パスワードで保護されていない共有の場合、 `-Credential` パラメーターは必要ありません。 次のように入力します。
     
       `Export-HcsSupportPackage`
     
       サポート パッケージは、既定のフォルダー内の両方のコントローラー用に作成されます。 このパッケージは、暗号化された圧縮ファイルで、トラブルシューティングの際は Microsoft サポートに送信できます。 詳細については、「 [Microsoft サポートに問い合わせる](storsimple-8000-contact-microsoft-support.md)」を参照してください。

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Export-HcsSupportPackage コマンドレットのパラメーター

Export-HcsSupportPackage コマンドレットには、次のパラメーターを使用できます。

| パラメーター | 必須/省略可能 | 説明 |
| --- | --- | --- |
| `-Path` |必須 |サポート パッケージが配置されるネットワーク共有フォルダーの場所を指定するためにします。 |
| `-EncryptionPassphrase` |必須 |サポート パッケージの暗号化に使用するパスフレーズを指定するためにします。 |
| `-Credential` |省略可能 |ネットワーク共有フォルダーのアクセス資格情報を指定するために使用します。 |
| `-Force` |省略可能 |暗号化パスフレーズの確認手順をスキップするために使用します。 |
| `-PackageTag` |省略可能 |サポート パッケージが配置される、 *Path* で指定した場所の下のディレクトリを指定するために使用します。 既定値は [デバイス名]-[現在の日時 (yyyy-MM-dd-HH-mm-ss)] です。 |
| `-Scope` |省略可能 |両方のコントローラーのサポート パッケージを作成する場合は、 **Cluster** (既定値) と指定します。 現在のコントローラーのみのパッケージを作成する場合は、 **Controller** を指定します。 |

## <a name="edit-a-support-package"></a>サポート パッケージを編集する

サポート パッケージを生成した後に、必要に応じて機密情報を削除するようにパッケージを編集します。 機密情報には、ボリューム名、デバイスの IP アドレス、ログ ファイルのバックアップ名などが含まれます。

> [!IMPORTANT]
> 編集できるのは、StorSimple 用 Windows PowerShell を使って生成されたサポート パッケージのみです。 Azure Portal で StorSimple デバイス マネージャー サービスを使用して作成したパッケージを編集することはできません。

サポート パッケージを Microsoft サポート サイトにアップロードする前に編集するには、まずサポート パッケージの暗号化を解除し、ファイルを編集して、もう一度暗号化する必要があります。 次の手順に従います。

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell でサポート パッケージを編集するには

1. 「 [StorSimple 用 Windows PowerShell でサポート パッケージを作成する](#to-create-a-support-package-in-windows-powershell-for-storsimple)」の説明に従って、サポート パッケージを生成します。
2. [スクリプトをダウンロード](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) します。
3. Windows PowerShell モジュールをインポートします。 スクリプトをダウンロードしたローカル フォルダーへのパスを指定します。 モジュールをインポートするには、次のコマンドを入力します。
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. すべてのファイルが圧縮および暗号化された *.aes* ファイルです。 ファイルの圧縮と暗号化を解除するには、次のコマンドを入力します。
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    すべてのファイルに実際のファイルの拡張子が表示されます。
   
    ![サポート パッケージの編集](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. 暗号化パスフレーズの入力を求められたら、サポート パッケージの作成時に使用したパスフレーズを入力します。
   
    ```powershell
    cmdlet Open-HcsSupportPackage at command pipeline position 1

    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
6. ログ ファイルが格納されているフォルダーに移動します。 ログ ファイルが展開され、暗号化が解除されているので、元のファイル拡張子が表示されます。 これらのファイルを変更し、ユーザー固有の情報 (ボリューム名やデバイスの IP アドレスなど) があれば削除して、ファイルを保存します。
7. ファイルを閉じて gzip で圧縮し、AES-256 で暗号化します。 これは、サポート パッケージをネットワーク経由で転送するときの速度とセキュリティのためです。 ファイルを圧縮して暗号化するには、次のコマンドを入力します。
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![サポート パッケージの編集 2](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. 入力を求められたら、変更したサポート パッケージの暗号化パスフレーズを入力します。
   
    ```powershell
    cmdlet Close-HcsSupportPackage at command pipeline position 1
    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
9. 要求されたときに Microsoft サポートと共有できるように、新しいパスフレーズを書き留めます。

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>例: パスワードで保護された共有でサポート パッケージ内のファイルを編集する

次に、サポート パッケージの復号化、編集、再暗号化の例を示します。

```powershell
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
```

## <a name="next-steps"></a>次のステップ

* [サポート パッケージで収集される情報](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* [サポート パッケージとデバイスのログを使用してデバイスのデプロイをトラブルシューティングする方法](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting)
* [StorSimple デバイス マネージャー サービスを使用して StorSimple デバイスを管理する](storsimple-8000-manager-service-administration.md)方法について説明します。

