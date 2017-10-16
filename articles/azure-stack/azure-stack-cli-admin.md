---
title: "Azure Stack ユーザーに対する Azure CLI の有効化 | Microsoft Docs"
description: "クロスプラットフォーム コマンドライン インターフェイス (CLI) を使用して、Azure Stack でリソースを管理およびデプロイする方法"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: d184bb9edbe2542d7321d8b9ccc5d23f2401f8d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Azure Stack ユーザーに対する Azure CLI の有効化

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

CLI を使用して実行できる Azure Stack オペレーターの特定のタスクはありません。 ただし、ユーザーが CLI を使用してリソースを管理できるようにするには、Azure Stack オペレーターがユーザーに以下を提供する必要があります。

* ユーザーが Azure Stack Development Kit の外部のワークステーションから CLI を使用する場合は、**Azure Stack の CA ルート証明書**が必要です。  

* **仮想マシンのエイリアス エンドポイント**は、VM をデプロイするときに、1 つのパラメーターとしてイメージ発行者、プラン、SKU、およびバージョンを参照する "UbuntuLTS" または "Win2012Datacenter" などのエイリアスを提供します。  

これらの値の取得方法については、以下のセクションで説明します。

## <a name="export-the-azure-stack-ca-root-certificate"></a>Azure Stack の CA ルート証明書をエクスポートする

Azure Stack の CA ルート証明書は、開発キットと、開発キット環境内で実行されているテナント仮想マシンで利用できます。 開発キットまたはテナント仮想マシンにサインインし、次のスクリプトを実行して、PEM 形式で Azure Stack ルート証明書をエクスポートします。

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Cerficate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>仮想マシンのエイリアス エンドポイントを設定する

Azure Stack オペレーターは、仮想マシンのエイリアス ファイルをホストする、公的にアクセス可能なエンドポイントを設定する必要があります。  仮想マシンのエイリアス ファイルは、VM を Azure CLI パラメーターとしてデプロイするときに、後で指定されるイメージの共通名を提供する JSON ファイルです。  

エイリアス ファイルにエントリを追加する前に、[Marketplace からイメージをダウンロードする] (azure-stack-download-azure-marketplace-item.md) または[、独自のカスタム イメージを発行](azure-stack-add-vm-image.md)してください。  カスタム イメージを発行する場合は、発行時に指定した発行者、プラン、SKU、およびバージョン情報をメモしておいてください。  Marketplace のイメージである場合は、```Get-AzureVMImage``` コマンドレットを使用して情報を表示できます。  
   
多くの一般的なイメージの別名を含む[サンプル エイリアス ファイル](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)が使用できます。それを出発点として使用できます。  このファイルを CLI クライアントがアクセスできる場所にホストする必要があります。  これを行う方法の 1 つは、BLOB ストレージ アカウント内でホストし、ユーザーと URL を共有することです。

1.  GitHub から[サンプル ファイル](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)をダウンロードします。
2.  Azure Stack に新しいストレージ アカウントを作成します。  完了したら、新しい BLOB コンテナーを作成します。  アクセス ポリシーを [パブリック] に設定します。  
3.  その新しいコンテナーに JSON ファイルをアップロードします。  完了したら、FBLOB 名をクリックし、BLOB のプロパティから URL を選択すると、BLOB の URL を表示できます。


## <a name="next-steps"></a>次のステップ

[Azure CLI を使用したテンプレートのデプロイ](azure-stack-deploy-template-command-line.md)

[PowerShell で接続する](azure-stack-connect-powershell.md)

[ユーザー アクセス許可の管理](azure-stack-manage-permissions.md)

