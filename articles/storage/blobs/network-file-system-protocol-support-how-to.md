---
title: NFS 3.0 プロトコル (プレビュー) を使用して Azure Blob Storage をマウントする | Microsoft Docs
description: NFS 3.0 プロトコルを使用して、Azure 仮想マシン (VM) から、またはオンプレミスで実行されているクライアントから、BLOB ストレージにコンテナーをマウントする方法について説明します。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 8ed63a508447104f9073c986debfae73ba7de89f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102428645"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>ネットワーク ファイル システム (NFS) 3.0 プロトコル (プレビュー) を使用して Blob Storage をマウントする

NFS 3.0 プロトコルを使用して、オンプレミスで実行される Linux ベースの Azure 仮想マシン (VM) または Linux システムから BLOB ストレージにコンテナーをマウントすることができます。 この記事では、ステップ バイ ステップ ガイダンスを提供しています。 BLOB ストレージでの NFS 3.0 プロトコルのサポートの詳細については、「[Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート (プレビュー)](network-file-system-protocol-support.md)」を参照してください。

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>手順 1:NFS 3.0 プロトコル機能をサブスクリプションに登録する

1. PowerShell コマンド ウィンドウを開きます。 

2. `Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```powershell
   Connect-AzAccount
   ```

3. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを設定します。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

4. 次のコマンドを使用して、`AllowNFSV3` 機能を登録します。

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. 次のコマンドを使用して、リソース プロバイダーを登録します。
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>手順 2:機能が登録されたことを確認する 

登録の承認には、最大 1 時間かかります。 登録が完了したことを確認するには、次のコマンドを使用します。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>手順 3:Azure 仮想ネットワーク (VNet) を作成する

ストレージ アカウントは VNet 内に含まれている必要があります。 VNet を使用すると、クライアントをストレージ アカウントに安全に接続できます。 VNet とその作成方法の詳細については、[Virtual Network のドキュメント](../../virtual-network/index.yml)を参照してください。

> [!NOTE]
> 同じ VNet 内のクライアントは、アカウントにコンテナーをマウントできます。 オンプレミス ネットワークで実行されているクライアントからコンテナーをマウントすることもできますが、そのためにはまずオンプレミス ネットワークを VNet に接続する必要があります。 詳細については「[サポートされているネットワーク接続](network-file-system-protocol-support.md#supported-network-connections)」を参照してください。

## <a name="step-4-configure-network-security"></a>手順 4:ネットワーク セキュリティの構成

アカウント内のデータをセキュリティで保護する唯一の方法は、VNet とその他のネットワーク セキュリティ設定を使用することです。 アカウント キーの承認、Azure Active Directory (AD) セキュリティ、アクセス制御リスト (ACL) などのデータをセキュリティで保護するために使用されるその他のツールは、NFS 3.0 プロトコルのサポートが有効にされているアカウントではまだサポートされません。 

アカウントのデータをセキュリティで保護するには、次の推奨事項を参照してください。[BLOB ストレージのセキュリティに関する推奨事項](security-recommendations.md#networking)。

## <a name="step-5-create-and-configure-a-storage-account"></a>手順 5:ストレージ アカウントの作成と構成

NFS 3.0 を使用してコンテナーをマウントするには、その機能をサブスクリプションに登録した **後に** ストレージ アカウントを作成する必要があります。 機能を登録する前に存在していたアカウントを有効にすることはできません。 

この機能のプレビュー リリースでは、NFS 3.0 プロトコルは [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) および [general-purpose V2](../common/storage-account-overview.md#general-purpose-v2-accounts) アカウントでサポートされています。

アカウントを構成するときに、次の値を選択します。

|設定 | Premium パフォーマンス | Standard パフォーマンス  
|----|---|---|
|場所|利用可能なすべてのリージョン |次のいずれかのリージョン:オーストラリア東部、韓国中部、米国中南部   
|パフォーマンス|Premium| Standard
|アカウントの種類|BlockBlobStorage| 汎用 v2
|レプリケーション|ローカル冗長ストレージ (LRS)| ローカル冗長ストレージ (LRS)
|接続方法|パブリック エンドポイント (選択されたネットワーク) またはプライベート エンドポイント |パブリック エンドポイント (選択されたネットワーク) またはプライベート エンドポイント
|安全な転送が必須|無効|無効
|階層型名前空間|Enabled|Enabled
|NFS V3|Enabled |Enabled 

他のすべての設定については、既定値をそのまま使用できます。 

## <a name="step-6-create-a-container"></a>手順 6:コンテナーを作成する

次のいずれかのツールまたは SDK を使用して、ストレージ アカウントにコンテナーを作成します。

|ツール|SDK|
|---|---|
|[Azure Portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>手順 7:コンテナーをマウントする

Linux システムにディレクトリを作成してから、コンテナーをストレージ アカウントにマウントします。

1. Linux システムで、ディレクトリを作成します。

   ```
   mkdir -p /mnt/test
   ```

2. 次のコマンドを使用して、コンテナーをマウントします。

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - このコマンドに表示される `<storage-account-name>` プレースホルダーをストレージ アカウントの名前に置き換えます。  

   - `<container-name>` プレースホルダーは、実際のコンテナーの名前に置き換えます。

---

## <a name="resolve-common-issues"></a>一般的な問題を解決

|問題またはエラー | 解決方法|
|---|---|
|`Access denied by server while mounting`|サポートされているサブネット内でクライアントが実行されていることを確認します。 [サポートされているネットワークの場所](network-file-system-protocol-support.md#supported-network-connections)のページを参照してください。|
|`No such file or directory`| マウントするコンテナーが、その機能が登録されていることを確認した後に作成されたことを確認します。 「[手順 2: 機能が登録されていることを確認する](#step-2-verify-that-the-feature-is-registered)」を参照してください。また、mount コマンドとそのパラメーターを直接ターミナルに入力してください。 別のアプリケーションからこのコマンドの一部をコピーしてターミナルに貼り付けると、貼り付けた情報の非表示の文字が原因でこのエラーが発生することがあります。|

## <a name="see-also"></a>関連項目

[Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート (プレビュー)](network-file-system-protocol-support.md)
