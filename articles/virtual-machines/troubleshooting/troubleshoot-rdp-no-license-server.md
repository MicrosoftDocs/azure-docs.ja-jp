---
title: Azure VM に接続すると、リモート デスクトップ ライセンス サーバーが利用できない | Microsoft Docs
description: リモート デスクトップ ライセンス サーバーが利用できないために RDP が失敗する問題をトラブルシューティングを行う方法について説明する | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 2c5eb25ae536a6cdb0eb12f1233307215fe2d7d1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080010"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Azure VM に接続すると、リモート デスクトップ ライセンス サーバーが利用できない

この記事は、ライセンスを提供するリモート デスクトップ ライセンス サーバーが利用できないために Azure 仮想マシン (VM) に接続できないという問題を解決するのに役立ちます。

## <a name="symptoms"></a>現象

仮想マシン (VM) に接続しようとすると、次のような状況が発生します。

- VM のスクリーンショットに、オペレーティング システムが完全に読み込まれて、資格情報を待機していることが示されます。
- Microsoft リモート デスクトップ プロトコル (RDP) 接続を作成しようとすると、次のエラー メッセージが表示されます。

  - ライセンスを提供するためのリモート デスクトップ ライセンス サーバーがないため、リモート セッションは切断されました。

  - 利用できるリモート デスクトップ ライセンス サーバーがありません。 このコンピューターは猶予期間を過ぎており、少なくとも有効な Windows Server 2008 ライセンス サーバーが接続されていないため、リモート デスクトップ サービスは動作を停止します。 このメッセージをクリックして RD セッション ホスト サーバーの構成ツールを開き、ライセンス診断を使用してください。

ただし、管理セッションを使用することで VM に正常に接続できます。

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>原因

この問題は、リモート セッションを開始するためのライセンスをリモート デスクトップ ライセンス サーバーが提供できない場合に発生します。 リモート デスクトップ セッション ホストの役割が VM にセットアップされている場合でも、次のいくつかのシナリオで発生する可能性があります。

- 環境にリモート デスクトップ ライセンス役割が存在したことが一度もなく、猶予期間 (180 日) が過ぎている。
- リモート デスクトップ ライセンスが環境にインストールされているがアクティベートされていない。
- 環境内のリモート デスクトップ ライセンスに、接続を設定するためのクライアント アクセス ライセンス (CAL) が挿入されていない。
- リモート デスクトップ ライセンスが環境にインストールされている。 使用可能な CAL はあるが適切に構成されていない。
- リモート デスクトップ ライセンスに CAL が存在し、アクティブ化されている。 ただし、リモート デスクトップ ライセンス サーバー上の他のいくつかの問題が原因で、環境内にライセンスを提供できない。

## <a name="solution"></a>解決策

この問題を解決するには、[OS ディスクをバックアップ](../windows/snapshot-copy-managed-disk.md)し、次の手順に従います。

1. 管理セッションを使用して VM に接続します。

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    管理セッションを使用して VM に接続できない場合、[Azure 上の仮想マシン シリアル コンソール](serial-console-windows.md)を使用して次のように VM にアクセスできます。

    1. **[サポートとトラブルシューティング]**  >  **[Serial console (Preview)]\(シリアル コンソール (プレビュー))** を選択して[シリアル コンソール] にアクセスします。 VM で機能が有効な場合、VM を正常に接続できます。

    2. CMD インスタンス用の新しいチャネルを作成します。 「**CMD**」と入力してチャネルを開始し、チャネル名を取得します。

    3. CMD インスタンスを実行しているチャネルに切り替えます。 この例では、チャネル 1 です。

       ```
       ch -si 1
       ```

    4. **Enter** キーをもう一度押し、VM の有効なユーザー名とパスワード (ローカルまたはドメイン ID) を入力します。

2. VM のリモート デスクトップ セッション ホスト役割が有効になっているかどうかを確認します。 役割が有効になっている場合は、適切に機能していることを確認します。 昇格された CMD インスタンスを開き、次の手順に従います。

    1. 次のコマンドを使用して、リモート デスクトップ セッション ホストの役割の状態を確認します。

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        このコマンドが 0 の値を返す場合は、役割が無効になっていることを意味し、手順 3 に進むことができます。

    2. 次のコマンドを使用して、ポリシーを確認し、必要に応じて再構成します。

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        **LicensingMode** の値がユーザーあたり 4 以外の値に設定されている場合、値を 4 に設定します。

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       **SpecifiedLicenseServers** の値が存在しないか、ライセンス サーバーの情報が正しくない場合は、次のように変更します。

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. レジストリを変更した後は、VM を再起動します。

    4. CAL を持っていない場合は、リモート デスクトップ セッション ホストの役割を削除します。 RDP は、通常の設定に戻されます。 VM に対しては 2 つの同時 RDP 接続のみが許可されます。

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        VM にリモート デスクトップ ライセンス役割があって、使用されていない場合は、その役割を削除することもできます。

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. VM がリモート デスクトップ ライセンス サーバーに接続できることを確認します。 VM とライセンス サーバーの間でポート 135 で接続をテストすることができます。 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. 環境にリモート デスクトップ ライセンス サーバーがなく、このサーバーが必要な場合は、[リモート デスクトップ ライセンス役割サービスをインストールする](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11))ことができます。 その後、[RDS ライセンスを構成します](https://blogs.technet.microsoft.com/askperf/2013/09/20/rd-licensing-configuration-on-windows-server-2012/)。

4. リモート デスクトップ ライセンス サーバーが構成されていて正常な状態の場合、リモート デスクトップ ライセンス サーバーを CAL でアクティブ化するようにします。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、[サポートに問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)て問題を解決してください。
