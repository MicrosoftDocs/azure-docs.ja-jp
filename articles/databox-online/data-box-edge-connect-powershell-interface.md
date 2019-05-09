---
title: Microsoft Azure Data Box Edge デバイスに Windows PowerShell インターフェイス経由で接続し、管理する | Microsoft Docs
description: Data Box Edge に Windows PowerShell インターフェイス経由で接続し、管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/29/2019
ms.author: alkohli
ms.openlocfilehash: b4d047f4266d11a5f6b77f33054eb93e31f7090b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58791577"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Azure Data Box Edge デバイスを Windows PowerShell 経由で管理する

Azure Data Box Edge ソリューションにより、データを処理してネットワーク経由で Azure に送信できます。 この記事では、Data Box Edge デバイスの構成と管理のタスクをいくつか説明します。 Azure portal、ローカル Web UI、または Windows PowerShell インターフェイスを使用してデバイスを管理できます。

この記事では、PowerShell インターフェイスを使用して行うタスクに重点を置いて説明します。

この記事には、次の手順が含まれています。

- PowerShell インターフェイスに接続する
- サポート セッションを開始する
- サポート パッケージを作成する
- 証明書のアップロード
- デバイスをリセットする
- デバイス情報を表示する
- コンピューティング ログを取得する
- コンピューティング モジュールの監視とトラブルシューティングを行う

## <a name="connect-to-the-powershell-interface"></a>PowerShell インターフェイスに接続する

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>サポート パッケージを作成する

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>証明書のアップロード

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

ご使用の IoT Edge デバイスと、そのデバイスに接続できるダウンストリーム デバイスとの間にセキュリティで保護された接続を確立するために、IoT Edge 証明書をアップロードすることもできます。 インストールする必要がある IoT Edge 証明書は 3 つあります (*.pem* 形式)。

- ルート CA 証明書または所有者 CA
- デバイス CA 証明書
- デバイス キー証明書

このコマンドレットを使用して、IoT Edge 証明書をインストールする例を次に示します。

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username/password"
```

証明書の詳細については、[Azure IoT Edge 証明書](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs)に関するページまたは「[ゲートウェイに証明書をインストール](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway#install-certificates-on-the-gateway)」に移動してください。

## <a name="view-device-information"></a>デバイス情報を表示する

 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>デバイスをリセットする

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>コンピューティング ログを取得する

デバイスにコンピューティング ロールが構成されている場合は、PowerShell インターフェイス経由でコンピューティング ログを取得することもできます。

1. [PowerShell インターフェイスに接続します](#connect-to-the-powershell-interface)。
2. `Get-AzureDataBoxEdgeComputeRoleLogs` を使用してデバイスのコンピューティング ログを取得します。

    このコマンドレットの使用例を次に示します。

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```

    このコマンドレットで使用されるパラメーターの説明を次に示します。
    - `Path`:コンピューティング ログ パッケージを作成する共有へのネットワーク パスを指定します。
    - `Credential`:ネットワーク共有のユーザー名とパスワードを指定します。
    - `RoleInstanceName`:このパラメーターには文字列 `IotRole` を指定します。
    - `FullLogCollection`:このパラメーターにより、ログ パッケージにすべてのコンピューティング ログが確実に含まれます。 既定では、ログ パッケージに含まれるものは、ログのサブセットのみです。

## <a name="monitor-and-troubleshoot-compute-modules"></a>コンピューティング モジュールの監視とトラブルシューティングを行う

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]


## <a name="next-steps"></a>次の手順

- Azure portal で [Azure Data Box Edge](data-box-edge-deploy-prep.md) を配置する。