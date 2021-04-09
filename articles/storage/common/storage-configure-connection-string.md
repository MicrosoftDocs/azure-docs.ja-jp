---
title: 接続文字列を構成する
titleSuffix: Azure Storage
description: Azure Storage アカウントの接続文字列を構成します。 接続文字列には、実行時にアプリケーションから共有キーによる承認を使用してストレージ アカウントへのアクセスを承認するために必要な情報が格納されます。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d7ca1707c89f03683960822591065143d3f8aa4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92783880"
---
# <a name="configure-azure-storage-connection-strings"></a>Azure Storage の接続文字列を構成する

接続文字列には、アプリケーションの実行時に共有キーによる承認を使用して Azure Storage アカウント内のデータにアクセスするために必要な承認情報が格納されています。 接続文字列は、次の目的に構成できます。

* Azurite ストレージ エミュレーターに接続します。
* Azure のストレージ アカウントにアクセスする。
* Shared Access Signature (SAS) を介して Azure の特定のリソースにアクセスする。

アカウント アクセス キーを表示し、接続文字列をコピーする方法については、「[ストレージ アカウント アクセス キーを管理する](storage-account-keys-manage.md)」を参照してください。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="store-a-connection-string"></a>接続文字列を保存する

アプリケーションは実行時に接続文字列にアクセスして、Azure Storage に対する要求を承認する必要があります。 接続文字列は次の方法で保存できます。

* 接続文字列は、環境変数に保存することができます。
* デスクトップまたはデバイスで実行するアプリケーションの場合は、**app.config** ファイルまたは **web.config** ファイルに接続文字列を保存できます。 この接続文字列は、これらのファイルの **AppSettings** セクションに追加します。
* Azure クラウド サービスで実行するアプリケーションの場合、接続文字列は [Azure サービスの構成スキーマ (.cscfg) ファイル](/previous-versions/azure/reference/ee758710(v=azure.100))に保存できます。 接続文字列をサービス構成ファイルの **ConfigurationSettings** セクションに追加します。

構成ファイル内に接続文字列を保存すると、接続文字列を簡単に更新して [Azurite ストレージ エミュレーター](../common/storage-use-azurite.md)とクラウドの Azure Storage アカウントを切り替えることができます。 必要な作業は、対象となる環境に合わせて接続文字列を編集するだけです。

[Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) を使用すると、アプリケーションの実行場所に関係なく、実行時に接続文字列にアクセスできます。

## <a name="configure-a-connection-string-for-azurite"></a>Azurite の接続文字列を構成する

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Azurite の詳細については、[ローカルでの Azure Storage の開発に Azurite エミュレーターを使用する](../common/storage-use-azurite.md)方法に関するページを参照してください。

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Azure Storage アカウントの接続文字列を構成する

Azure Storage アカウントへの接続文字列は、次の形式で作成します。 HTTPS (推奨) と HTTP のどちらを使用してストレージ アカウントに接続するかを指定し、`myAccountName` をストレージ アカウントの名前に、`myAccountKey` をアカウント アクセス キーに置き換えます。

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

たとえば、接続文字列は次のようになります。

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Azure Storage では、HTTP と HTTPS の両方の接続文字列をサポートします。ただし、"*HTTPS の使用を強くお勧めします*"。

> [!TIP]
> お使いのストレージ アカウントの接続文字列は、[Azure Portal](https://portal.azure.com) に見つかります。 ストレージ アカウントのメニュー ブレードの **設定** > 、**アクセス キー** の順に移動して、両方のプライマリおよびセカンダリのアクセス キーの接続文字列を確認できます。
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Shared Access Signature を使用して接続文字列を作成する

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>明示的なストレージ エンドポイントへの接続文字列を作成する

接続文字列の中で、既定のエンドポイントを使用せずに、明示的なサービス エンドポイントを指定することができます。 明示的なエンドポイントを指定する接続文字列を作成するには、次に示す形式を使用して、プロトコル仕様 (HTTPS (推奨) または HTTP) を含む完全なサービス エンドポイントをサービスごとに指定します。

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

たとえば Blob Storage のエンドポイントを[カスタム ドメイン](../blobs/storage-custom-domain-name.md)にマップしてある場合、明示的なエンドポイントの指定が必要になります。 その場合は、接続文字列で Blob Storage へのカスタム エンドポイントを指定できます。 お使いのアプリケーションが使用している場合は、その他のサービスへの既定のエンドポイントを指定することもできます。

Blob service のエンドポイントを明示的に指定する接続文字列の例を次に示します。

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

この例では、Blob service のカスタム ドメインを含むすべてのサービスへの明示的なエンドポイントを指定します。

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

接続文字列のエンドポイント値を使用することで、ストレージ サービスへの要求 URI が作成され、コードに返される URI の形式が決まります。

カスタム ドメインにストレージ エンドポイントをマッピングし、そのエンドポイントを接続文字列に指定しなかった場合、コードからその接続文字列を使用して、サービスのデータにアクセスすることはできません。

Azure Storage 用にカスタム ドメインを構成する方法の詳細については、「[カスタム ドメインを Azure Blob Storage エンドポイントにマップする](../blobs/storage-custom-domain-name.md)」を参照してください。

> [!IMPORTANT]
> 接続文字列のサービス エンドポイントの値は、`https://`(推奨) や `http://` など、整形式の URI である必要があります。

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>エンドポイント サフィックスを含む接続文字列を作成する

Azure China 21Vianet、Azure Government など、別のエンドポイント サフィックスを持つリージョンまたはインスタンスのストレージ サービスの接続文字列を作成するには、次の接続文字列の形式を使用します。 HTTPS (推奨) または HTTP のどちらでストレージ アカウントに接続するかを示し、`myAccountName` をストレージ アカウントの名前、`myAccountKey` をアカウント アクセス キー、`mySuffix` を URI サフィックスに置き換えます。

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Azure China 21Vianet のストレージ サービスの接続文字列の例を次に示します。

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>接続文字列の解析

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>次のステップ

* [ローカルでの Azure Storage の開発に Azurite エミュレーターを使用する](../common/storage-use-azurite.md)
* [Azure Storage Explorer](storage-explorers.md)
* [Shared Access Signatures (SAS) の使用](storage-sas-overview.md)