---
title: "Azure Storage への接続文字列の構成 |Microsoft Docs"
description: "Azure ストレージ アカウントへの接続文字列を構成します。 接続文字列には、アプリケーションがストレージ アカウントに対し、そのアクセスが信頼できるものであることを実行時に証明するうえで必要な情報が格納されます。"
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: ecb0acb5-90a9-4eb2-93e6-e9860eda5e53
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 1a3c754bf2f2b73d0bf72cbf48b906d8085eaef1
ms.openlocfilehash: f4410c10ce66d50b64307e364e64a3367b9397f9


---
# <a name="configure-azure-storage-connection-strings"></a>Azure Storage の接続文字列を構成する
## <a name="overview"></a>概要
アプリケーションから実行時に Azure ストレージ アカウント内のデータにアクセスするために必要な認証情報は、接続文字列に指定します。 接続文字列は、次の目的に構成できます。

* Azure ストレージ エミュレーターに接続する。
* Azure のストレージ アカウントにアクセスする。
* Shared Access Signature (SAS) を介して Azure の特定のリソースにアクセスする。

[!INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>接続文字列の保存
アプリケーションは、その要求が信頼できるものであることを Azure Storage に対して証明するために、実行時に接続文字列にアクセスする必要があります。 接続文字列の保存にはいくつかのオプションがあります。

* デスクトップまたはデバイスで実行するアプリケーションの場合は、**app.config** ファイルまたは **web.config** ファイルに接続文字列を保存できます。 この接続文字列は、 **AppSettings** セクションに追加します。
* アプリケーションが Azure クラウド サービスで実行している場合、接続文字列は [Azure サービスの構成スキーマ (.cscfg) ファイル](https://msdn.microsoft.com/library/ee758710.aspx)に保存できます。 接続文字列をサービス構成ファイルの **ConfigurationSettings** セクションに追加します。
* 直接コード内に接続文字列を記述することもできますが、 通常は、構成ファイルに保存するようお勧めします。

構成ファイル内に接続文字列を保存すると、接続文字列を簡単に更新してストレージ エミュレーターとクラウドの Azure ストレージ アカウントを切り替えることができます。 必要な作業は、対象となる環境に合わせて接続文字列を編集するだけです。

[Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) クラスを使用すると、アプリケーションの実行場所に関係なく、実行時に接続文字列にアクセスできます。

## <a name="create-a-connection-string-to-the-storage-emulator"></a>ストレージ エミュレーターへの接続文字列を作成する
[!INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

ストレージ エミュレーターについて詳しくは、「 [開発とテストのための Azure のストレージ エミュレーター使用](storage-use-emulator.md) 」を参照してください。

## <a name="create-a-connection-string-to-an-azure-storage-account"></a>Azure ストレージ アカウントへの接続文字列を作成する
Azure ストレージ アカウントへの接続文字列を作成するには、次の接続文字列の形式を使用します。 HTTPS (推奨) と HTTP のどちらを使用してストレージ アカウントに接続するかを指定し、*myAccountName* をストレージ アカウントの名前に、*myAccountKey* をアカウント アクセス キーに置き換えます。

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

たとえば、接続文字列は次のサンプルの接続文字列のようになります。

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

> [!NOTE]
> Azure Storage では、HTTP と HTTPS の両方の接続文字列をサポートします。ただし、HTTPS の使用を強くお勧めします。
>
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Shared Access Signature を使用して接続文字列を作成する
[!INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="creating-a-connection-string-to-an-explicit-storage-endpoint"></a>明示的なストレージ エンドポイントへの接続文字列を作成する
接続文字列の中で明示的に (既定のエンドポイントを使用せずに) サービス エンドポイントを指定することができます。 明示的なエンドポイントを指定する接続文字列を作成するには、次に示す形式を使用して、プロトコル仕様 (HTTPS (推奨) または HTTP) を含む完全なサービス エンドポイントをサービスごとに指定します。

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

たとえば Blob Storage のエンドポイントをカスタム ドメインにマップしてある場合、明示的なエンドポイントの指定が必要になります。 その場合、Blob Storage のカスタム エンドポイントを接続文字列の中で指定したうえで、他のサービス用の既定のエンドポイントをアプリケーションで使用する場合は、それらを必要に応じて指定できます。

BLOB サービスのエンドポイントを明示的に指定する有効な接続文字列の例を次に示します。

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=www.mydomain.com;
AccountName=storagesample;
AccountKey=account-key

# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=www.mydomain.com;
FileEndpoint=myaccount.file.core.windows.net;
QueueEndpoint=myaccount.queue.core.windows.net;
TableEndpoint=myaccount;
AccountName=storagesample;
AccountKey=account-key
```

接続文字列に示されているエンドポイント値を使用することで、BLOB サービスへの要求 URI が作成され、コードに返される URI の形式も決定されます。

サービス エンドポイントを接続文字列に指定しなかった場合、コードからその接続文字列を使用して、サービスのデータにアクセスすることはできません。

### <a name="creating-a-connection-string-with-an-endpoint-suffix"></a>エンドポイント サフィックスを含む接続文字列を作成する
Azure China、Azure Governance など、別のエンドポイント サフィックスを持つリージョンまたはインスタンスのストレージ サービスの接続文字列を作成するには、次の接続文字列の形式を使用します。 HTTP と HTTPS のどちらを使用してストレージ アカウントに接続するかを指定し、*myAccountName* をストレージ アカウントの名前に、*myAccountKey* をアカウント アクセス キーに置き換え、*mySuffix* を URｌ サフィックスに置き換えます。

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

たとえば、接続文字列は次の接続文字列のように表示されます。

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>接続文字列の解析
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>次のステップ
* [開発とテストのための Azure のストレージ エミュレーター使用](storage-use-emulator.md)
* [Azure ストレージ エクスプローラー](storage-explorers.md)
* [Shared Access Signatures (SAS) の使用](storage-dotnet-shared-access-signature-part-1.md)




<!--HONumber=Dec16_HO2-->


