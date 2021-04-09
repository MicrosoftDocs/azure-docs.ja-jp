---
title: 'チュートリアル: Azure Stack Edge Pro GPU を使用してストレージ アカウントにデータを転送する | Microsoft Docs'
description: Azure Stack Edge Pro GPU デバイスにローカル ストレージ アカウントと Edge ストレージ アカウントを追加して接続する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to storage accounts on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 4d235136190845ba7531592fdeecd9cd05b0347c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200943"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-pro-gpu"></a>チュートリアル:Azure Stack Edge Pro GPU でストレージ アカウントを使用してデータを転送する 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

このチュートリアルでは、Azure Stack Edge Pro デバイスにストレージ アカウントを追加して接続する方法について説明します。 ストレージ アカウントを追加したら、Azure Stack Edge Pro から Azure にデータを転送できます。

この手順の所要時間は約 30 分です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ストレージ アカウントの追加
> * ストレージ アカウントへの接続

 
## <a name="prerequisites"></a>前提条件

ストレージ アカウントを Azure Stack Edge Pro に追加する前に、次のことを確認してください。

- [Azure Stack Edge Pro の設置](azure-stack-edge-gpu-deploy-install.md)に関するページで説明されているように、物理デバイスが設置されていること。

- [Azure Stack Edge Pro のアクティブ化](azure-stack-edge-gpu-deploy-activate.md)に関するページで説明されているように、物理デバイスがアクティブ化されていること。


## <a name="add-an-edge-storage-account"></a>Edge ストレージ アカウントを追加する

Edge ストレージ アカウントを作成するには、次の手順のようにします。

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>Edge ストレージ アカウントに接続する

*http* または *https* 経由で、Edge ストレージ REST API に接続できます。

- *https* はセキュリティで保護されており、推奨される方法です。
- *http* は、信頼されたネットワーク経由で接続するときに使用します。

## <a name="connect-via-http"></a>http 経由で接続する

http 経由で Edge ストレージ REST API に接続するには、以下の手順のようにする必要があります。

- Azure 整合サービス VIP と Blob service エンドポイントをリモート ホストに追加する
- 接続を確認する 

以下のセクションでは、これらの各手順について説明します。

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>デバイス IP アドレスと Blob service エンドポイントをリモート クライアントに追加する

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>Verify connection

接続を確認するには、通常、前のステップで収集した次の情報 (異なる場合があります) が必要です。

- ストレージ アカウント名。
- ストレージ アカウント アクセス キー。
- Blob service エンドポイント。

ストレージ アカウント名と Blob service エンドポイントは既にわかっています。 ストレージ アカウントのアクセス キーは、Azure PowerShell クライアントを使用して Azure Resource Manager 経由でデバイスに接続することで取得できます。

[Azure Resource Manager を使用したデバイスへの接続](azure-stack-edge-gpu-connect-resource-manager.md)に関する記事の手順のようにします。 Azure Resource Manager 経由でローカル デバイス API にサインインしたら、デバイス上のストレージ アカウントの一覧を取得します。 次のコマンドレットを実行します。

`Get-AzureRMStorageAccount`

デバイス上のストレージ アカウントの一覧から、アクセス キーが必要なストレージ アカウントを特定します。 そのストレージ アカウント名とリソース グループを記録します。

サンプル出力を次に示します。

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

アクセス キーを取得するには、次のコマンドレットを実行します。

`Get-AzureRmStorageAccountAccessKey`

サンプル出力を次に示します。

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

このキーをコピーして保存します。 このキーを使用し、Azure Storage Explorer を使用して接続を確認します。

正常に接続が確立されたことを確認するために、Storage Explorer を使用して外部のストレージ アカウントにアタッチします。 Storage Explorer がない場合は、[Storage Explorer をダウンロードします](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409)。

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>https 経由で接続する

https 経由の Azure Blob Storage REST API への接続では、以下の手順が必要になります。

- BLOB エンドポイント証明書を取得する
- クライアントまたはリモート ホストで証明書をインポートする
- デバイス IP と Blob service エンドポイントをクライアントまたはリモート ホストに追加する
- 接続を構成して確認する

以下のセクションでは、これらの各手順について説明します。

### <a name="get-certificate"></a>証明書を取得する

HTTPS 経由で Blob Storage にアクセスするには、デバイスの SSL 証明書が必要です。 また、この証明書を、秘密キーが添付された *.pfx* ファイルとして、Azure Stack Edge Pro デバイスにアップロードします。 これらの証明書を (テストと開発の目的のみで) 作成して Azure Stack Edge Pro デバイスにアップロードする方法の詳細については、次の記事を参照してください。

- [BLOB エンドポイント証明書を作成する](azure-stack-edge-gpu-manage-certificates.md#create-certificates-optional)。
- [BLOB エンドポイント証明書をアップロードする](azure-stack-edge-gpu-manage-certificates.md#upload-certificates)。
- [デバイスにアクセスするクライアントで証明書をインポートする](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)。

### <a name="import-certificate"></a>証明書のインポート

Azure Storage Explorer を使用してデバイス上のストレージ アカウントに接続する場合は、証明書を PEM 形式で Storage Explorer にインポートする必要もあります。 Windows 環境では、Base-64 でエンコードされた *.cer* は PEM 形式と同じです。

Azure Storage Explorer に証明書をインポートするには、次の手順のようにします。

1. Azure Storage Explorer で Azure Stack API が対象になっていることを確認します。 **[編集] > [Azure Stack API を対象にする]** に移動します。 プロンプトが表示されたら、Storage Explorer を再起動して変更を有効にします。

2. SSL 証明書をインポートするには、 **[編集] > [SSL 証明書] > [証明書のインポート]** に移動します。

  
   ![証明書を Storage Explorer にインポートする](./media/azure-stack-edge-gpu-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. 署名チェーンと BLOB 証明書に移動してそれらを指定します。 署名チェーンと BLOB 証明書はどちらも、Windows システムにおいて Base64 でエンコードされた形式と同じ PEM 形式である必要があります。 証明書が正常にインポートされたことが通知されます。


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>デバイス IP アドレスと Blob service エンドポイントを追加する

[*http* 経由での接続時のデバイスの IP アドレスと Blob service エンドポイントの追加](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client)手順と同じ手順に従います。

### <a name="configure-and-verify-connection"></a>接続を構成して確認する

[*http* 経由での接続時に使用した接続の構成と確認](#verify-connection)の手順に従います。 唯一の違いは、*http を使用するオプション* をオフにしておく必要があることです。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Stack Edge Pro に関する次のトピックについて説明しました。

> [!div class="checklist"]
> * ストレージ アカウントの追加
> * ストレージ アカウントに接続する

Azure Stack Edge Pro を使用してデータを変換する方法については、次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro でデータを変換する](./azure-stack-edge-j-series-deploy-configure-compute.md)
