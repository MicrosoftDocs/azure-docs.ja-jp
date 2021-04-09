---
title: Azure portal を使用した Azure Stack Edge Pro と GPU のトラブルシューティング | Microsoft Docs
description: Azure Stack Edge Pro GPU の問題をトラブルシューティングする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 696faaecd2227c9b9ef74f20763e36661991ff67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438985"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスの問題のトラブルシューティング 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイスの問題をトラブルシューティングする方法について説明します。 


## <a name="run-diagnostics"></a>診断の実行

デバイスのエラーの診断とトラブルシューティングを行うには、診断テストを実行します。 診断テストを実行するには、お客様のデバイスのローカル Web UI で、次の手順を実行します。

1. ローカル Web UI で、 **[トラブルシューティング]、[診断テスト]** の順に移動します。 実行したいテストを選択し、 **[テストの実行]** を選択します。 このテストにより、お客様のネットワーク、デバイス、Web プロキシ、時刻、またはクラウドの設定で発生する可能性のある問題が診断されます。 デバイスでテストを実行中であることが通知されます。

    ![テストの選択 ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. テストが完了すると、結果が表示されます。 

    ![テスト結果を表示する](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    テストが失敗すると、推奨される操作の URL が表示されます。 URL を選択して、推奨される操作を表示します。
 
    ![失敗したテストの警告の確認](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>サポート パッケージの収集

ログ パッケージは、Microsoft サポートがデバイスの問題のトラブルシューティングを行う際に役立つすべての関連ログで構成されます。 ログ パッケージは、ローカル Web UI を使用して生成することができます。

サポート パッケージを収集するには、次の手順を実行します。 

1. ローカル Web UI で、 **[トラブルシューティング]、[サポート]** の順に移動します。 **[サポートパッケージの作成]** を選択します。 システムでサポート パッケージの収集が開始されます。 パッケージの収集には数分かかる場合があります。

    ![[ユーザーの追加] の選択](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. サポート パッケージが作成されたら、 **[サポート パッケージのダウンロード]** を選択します。 圧縮されたパッケージが、お客様が選択したパスにダウンロードされます。 パッケージを解凍して、システム ログ ファイルを表示できます。

    ![[ユーザーの追加] の選択 2](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>高度なセキュリティ ログの収集

高度なセキュリティ ログには、Azure Stack Edge Pro デバイスへのソフトウェアまたはハードウェア侵入のログが記録されることがあります。

### <a name="software-intrusion-logs"></a>ソフトウェア侵入ログ

ソフトウェア侵入ログまたは既定のファイアウォール ログは、受信および送信トラフィックを対象に収集されます。 

- 工場でデバイスがイメージ化されると、既定のファイアウォール ログが有効になります。 これらのログは、ローカル UI またはデバイスの Windows PowerShell インターフェイスからサポート パッケージを作成すると、既定でサポート パッケージにバンドルされます。

- デバイスへのソフトウェア (ネットワーク) 侵入を確認するために、ファイアウォール ログのみがサポート パッケージに必要な場合は、サポート パッケージの作成時に `-Include FirewallLog` オプションを使用します。 

- 特定のインクルード オプションを指定しない場合、ファイアウォール ログは既定でサポートパッケージに含まれます。

- サポート パッケージで、ファイアウォール ログは `pfirewall.log` であり、ルート フォルダーにあります。 Azure Stack Edge Pro デバイスへのソフトウェア侵入ログの例を次に示します。 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>ハードウェア侵入ログ

デバイスへのハードウェア侵入を検出するために、現時点では、シャーシの開閉などのすべてのシャーシ イベントがログに記録されます。 

- デバイスからのシステム イベント ログは `racadm` コマンドレットを使用して読み取られます。 その後、これらのイベントをシャーシ関連のイベントに絞り込んだものが `HWIntrusion.txt` ファイルに保存されます。

- サポート パッケージでハードウェア侵入ログのみを取得するには、サポート パッケージの作成時に `-Include HWSelLog` オプションを使用します。 

- 特定のインクルード オプションを指定しない場合、ハードウェア侵入ログは既定でサポートパッケージに含まれます。

- サポート パッケージで、ハードウェア侵入ログは `HWIntrusion.txt` であり、ルート フォルダーにあります。 Azure Stack Edge Pro デバイスへのハードウェア侵入ログの例を次に示します。 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>ログを使用したトラブルシューティング

アップロード プロセス中および更新プロセス中に発生したすべてのエラーが、それぞれのエラー ファイルに含まれています。

1. エラー ファイルを表示するには、共有に移動し、内容を表示する共有を選択します。 


2. "_Microsoft Data Box Edge フォルダー_" を選択します。 このフォルダーには、次の 2 つのサブフォルダーがあります。

    - アップロードのエラー用のログ ファイルがある Upload フォルダー。
    - 更新中のエラー用の Refresh フォルダー。

    以下に更新用のログ ファイルのサンプルを示します。

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. このファイル内にエラー (サンプルの強調表示されている部分) がある場合は、エラー コードを書き留めておきます。この例では 16001 です。 以下のエラー リファレンスで、このエラー コードの説明を参照します。

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>エラーの一覧をトラブルシューティングに使用する

特定されたシナリオからエラーの一覧がまとめられ、自己診断やトラブルシューティングに使用できます。 

## <a name="azure-resource-manager"></a>Azure Resource Manager

デバイスにアクセスするために Azure Resource Manager を構成する間、エラーが表示されることがあります。ここでは、それらのエラーについて説明します。 

| **問題/エラー** |  **解像度** | 
|------------|-----------------|
|一般的な問題|<li>[Edge デバイスが正しく構成されていることを確認します](#verify-the-device-is-configured-properly)。<li> [クライアントが正しく構成されていることを確認します](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment:この要求の送信中にエラーが発生しました。<br>行: 1 文字: 1<br>+ Add-AzureRmEnvironment -Name Az3 -ARMEndpoint "https://management.dbe ...|このエラーは、Azure Stack Edge Pro デバイスに到達できないか、デバイスが正しく構成されていないことを意味します。 Edge デバイスとクライアントが正しく構成されていることを確認してください。 ガイダンスについては、この表の「**一般的な問題**」の行を参照してください。|
|サービスからエラーが返されました。 詳細は InnerException を確認してください:基になる接続が閉じられました。SSL/TLS のセキュリティで保護されているチャネルに対する信頼関係を確立できませんでした。 |   このエラーの原因としては、独自の証明書の持ち込みの手順の 1 つ以上が正しく実行されていないことが考えられます。 [こちら](./azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)のガイダンスを参照してください。 |
|操作によって無効な状態コード 'ServiceUnavailable' が返されました <br> 応答状態コードは成功を示していません:503 (サービスは利用できません)。 | このエラーは、次のいずれかの条件の結果である可能性があります。<li>ArmStsPool が停止状態です。</li><li>Azure Resource Manager/セキュリティ トークン サービスの Web サイトのどちらかがダウンしています。</li><li>Azure Resource Manager クラスター リソースがダウンしています。</li><br><strong>注:</strong>アプライアンスを再起動すると問題が解決する場合がありますが、さらにデバッグを行えるようにサポート パッケージを収集してください。|
|AADSTS50126:ユーザー名またはパスワードが無効です。<br>トレース ID:29317da9-52fc-4ba0-9778-446ae5625e5a<br>関連付け ID:1b9752c4-8cbf-4304-a714-8a16527410f4<br>タイムスタンプ:2019-11-15 09:21:57Z:リモート サーバーがエラー(400) 要求が正しくありません。<br>行: 1 文字: 1 |このエラーは、次のいずれかの条件の結果である可能性があります。<li>ユーザー名とパスワードが無効な場合は、[こちら](./azure-stack-edge-j-series-set-azure-resource-manager-password.md)の手順に従い、正しいパスワードを使用して、顧客が Azure portal からパスワードを変更したことを確認してください。<li>テナント ID が無効な場合、テナント ID は固定 GUID であるため、`c0257de7-538f-415c-993a-1b87a031879d` に設定してください</li>|
|connect-AzureRmAccount:AADSTS90056:リソースが無効になっているか、存在しません。 アプリのコードをチェックして、アクセスしようとしているリソースの正確なリソース URL を指定していることを確認します。<br>トレース ID: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>関連付け ID:75c8ef5a-830e-48b5-b039-595a96488ff9 タイムスタンプ:2019-11-18 07:00:51Z:リモート サーバーがエラー(400) Bad を返しました |`Add-AzureRmEnvironment` コマンドで使用したリソース エンドポイントが正しくありません。|
|クラウドからエンドポイントを取得できません。<br>ネットワークに接続していることを確認してください。 エラーの詳細:HTTPSConnectionPool(host='management.dbg-of4k6suvm.microsoftdatabox.com', port=30005):次の URL で最大再試行回数を超えました: /metadata/endpoints?api-version=2015-01-01 (Caused by SSLError(SSLError("bad handshake:Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')],)",),)) |このエラーは主に Mac/Linux 環境で発生し、次の問題が原因です。<li>PEM 形式の証明書が python 証明書ストアに追加されませんでした。</li> |

### <a name="verify-the-device-is-configured-properly"></a>デバイスが正しく構成されていることの確認

1. ローカル UI から、デバイスのネットワークが正しく構成されていることを確認します。

2. [ここ](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)に記載されているように、すべてのエンドポイントで証明書が更新されていることを確認します。

3. ローカル UI の **[デバイス]** ページから、Azure Resource Manager の管理およびログイン エンドポイントを取得します。

4. デバイスがアクティブ化され、Azure に登録されていることを確認します。


### <a name="verify-the-client-is-configured-properly"></a>クライアントが正しく構成されていることを確認します

1. [ここ](azure-stack-edge-j-series-connect-resource-manager.md#step-3-install-powershell-on-the-client)に記載されているように、正しい PowerShell バージョンがインストールされていることを確認します。

2. [ここ](azure-stack-edge-j-series-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client)に記載されているように、正しい PowerShell モジュールがインストールされていることを確認します。

3. Azure Resource Manager とログイン エンドポイントに到達できることを確認します。 エンドポイントに対して ping を試すことができます。 次に例を示します。

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   到達できない場合は、[ここ](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)に記載されているように DNS/ホスト ファイルのエントリを追加します。
   
4. [ここ](azure-stack-edge-j-series-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell)に記載されているように、クライアント証明書がインストールされていることを確認します。

5. 顧客が PowerShell を使用している場合は、次の PowerShell コマンドを実行してデバッグ設定を有効にし、詳細なメッセージが表示されるようにしてください。 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>デバイス上の BLOB ストレージ 

ここでは、Azure Stack Edge Pro/Data Box Gateway デバイス上の BLOB ストレージに関連するエラーについて説明します。

| **問題/エラー** |  **解像度** | 
|--------------------|-----------------|
|Unable to retrieve child resources. (子リソースを取得できません。) The value for one of the HTTP headers is not in the correct format. (いずれかの HTTP ヘッダーの値の形式が正しくありません。)| **[編集]** メニューから **[Target Azure Stack APIs]\(Azure Stack API を対象とする\)** を選択します。 その後、Azure Storage Explorer を再起動します。|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|エンドポイント名 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` が、Windows では `C:\Windows\System32\drivers\etc\hosts`、Linux では `/etc/hosts` にある hosts ファイルに追加されていることを確認します。|
|Unable to retrieve child resources. (子リソースを取得できません。)<br> Details: self-signed certificate (詳細: 自己署名証明書) |次の手順に従って、ご使用のデバイス用の SSL 証明書を Azure Storage Explorer にインポートします。 <ol><li>Azure portal から証明書をダウンロードします。 詳細については、「[証明書のダウンロード](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)」を参照してください。</li><li>**[編集]** メニューから、 [SSL 証明書] 、 **[証明書のインポート]** の順に選択します。</li></ol>|
|AzCopy コマンドが 1 分間応答を停止しているように見えた後、次のメッセージが表示されます。<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|エンドポイント名 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` が `C:\Windows\System32\drivers\etc\hosts` にあるホスト ファイルに追加されていることを確認します。|
|AzCopy コマンドが 1 分間応答を停止しているように見えた後、次のメッセージが表示されます。<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |ご使用のデバイス用の SSL 証明書をシステムの証明書ストアにインポートします。 詳細については、「[証明書のダウンロード](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)」を参照してください。|
|AzCopy コマンドが 20 分間応答を停止しているように見えた後、次のメッセージが表示されます。<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |エンドポイント名 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` が `/etc/hosts` にあるホスト ファイルに追加されていることを確認します。|
|AzCopy コマンドが 20 分間応答を停止しているように見えた後、次のメッセージが表示されます。<br>`Error parsing source location… The SSL connection could not be established`. |ご使用のデバイス用の SSL 証明書をシステムの証明書ストアにインポートします。 詳細については、「[証明書のダウンロード](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)」を参照してください。|
|AzCopy コマンドが 20 分間応答を停止しているように見えた後、次のメッセージが表示されます。<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|エンドポイント名 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` が `/etc/hosts` にあるホスト ファイルに追加されていることを確認します。|
|AzCopy コマンドが 20 分間応答を停止しているように見えた後、次のエラーが表示されます。`Error parsing source location… The SSL connection could not be established`|ご使用のデバイス用の SSL 証明書をシステムの証明書ストアにインポートします。 詳細については、「[証明書のダウンロード](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)」を参照してください。|
|The value for one of the HTTP headers is not in the correct format. (いずれかの HTTP ヘッダーの値の形式が正しくありません。)|インストールしたバージョンの Python 用 Azure Storage ライブラリが Data Box でサポートされていません。 Azure Data Box Blob ストレージの要件で、サポートされるバージョンを確認してください。|
|… [SSL:CERTIFICATE_VERIFY_FAILED] …| Python を実行する前に、REQUESTS_CA_BUNDLE 環境変数を Base64 でエンコードされた SSL 証明書ファイルのパスに設定してください ([証明書のダウンロード](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)方法を参照してください)。 次に例を示します。<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>あるいは、証明書をシステムの証明書ストアに追加し、この環境変数をそのストアのパスに設定します。 Ubuntu の場合の例:<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|The connection times out. (接続がタイムアウトになりました。)|Azure Stack Edge Pro にサインインし、ロックが解除されていることを確認します。 デバイスは再起動されるたびに、ユーザーがサインインするまでロックされた状態になります。|

## <a name="troubleshoot-iot-edge-errors"></a>IoT Edge のエラーのトラブルシューティング

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]


## <a name="next-steps"></a>次のステップ

- 詳細については、[デバイスのアクティブ化に関する問題をトラブルシューティング](azure-stack-edge-gpu-troubleshoot-activation.md)する方法に関する記事を参照してください。