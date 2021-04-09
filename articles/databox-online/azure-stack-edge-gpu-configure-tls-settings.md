---
title: Azure Stack Edge Pro GPU デバイスにアクセスする Windows クライアント上で TLS 1.2 を構成する
description: Azure Stack Edge Pro GPU デバイスにアクセスする Windows クライアント上で TLS 1.2 を構成する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4a159f7fa384a6899fb3cbb4db3bba9e0ed02d52
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102637271"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro デバイスにアクセスする Windows クライアント上で TLS 1.2 を構成する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Windows クライアントを使用して Azure Stack Edge Pro デバイスにアクセスする場合、クライアント上で TLS 1.2 を構成する必要があります。 この記事では、Windows クライアント上で TLS 1.2 を構成するためのリソースとガイドラインを提供します。 

ここで提供するガイドラインは、Windows Server 2016 を実行するクライアント上で実行されたテストに基づいています。

## <a name="configure-tls-12-for-current-powershell-session"></a>現在の PowerShell セッションに対して TLS 1.2 を構成する

クライアント上で TLS 1.2 を構成するには、次の手順を行います。

1. PowerShell を管理者として実行します。
2. 現在の PowerShell セッションに対して TLS 1.2 を設定するには、次のように入力します。
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>クライアント上で TLS 1.2 を構成する

お使いの環境でシステム全体に TLS 1.2 を設定する場合、次のドキュメントのガイドラインに従ってください。

- [全般 - TLS 1.2 を有効にする方法](/windows-server/security/tls/tls-registry-settings#tls-12)
- [クライアントで TLS 1.2 を有効にする方法](/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [サイト サーバーとリモート サイト システムで TLS 1.2 を有効にする方法](/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [TLS/SSL のプロトコル (Schannel SSP)](/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [暗号スイート](/windows-server/security/tls/tls-registry-settings#tls-12):具体的には、「[TLS 暗号スイートの順序の構成](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)」 必ず現在の暗号スイートを一覧に挙げ、不足しているものがあれば次の一覧から追加してください。

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    レジストリ設定を直接編集することでこれらの暗号スイートを追加することもできます。

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- 楕円曲線を設定する方法

    必ず現在の楕円曲線を一覧に挙げ、不足しているものがあれば次の一覧から追加してください。

    - P-256 
    - P-384

    レジストリ設定を直接編集することでこれらの楕円曲線を追加することもできます。
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [最小 RSA キー交換サイズを 2048 に設定します](/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes)。



## <a name="next-steps"></a>次のステップ

[Azure Resource Manager に接続する](azure-stack-edge-j-series-connect-resource-manager.md)