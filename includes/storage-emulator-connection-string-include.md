---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 85e7cb86217340e77a6f597a357c3de1f91fb8d0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070559"
---
Azurite では、共有キー認証について、単一の固定アカウントと既知の認証キーがサポートされています。 このアカウントとキーのみ、Azurite で共有キー資格情報として使用できます。 これらは次のとおりです。

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Azurite でサポートされている認証キーは、クライアント認証コードの機能をテストすることだけを目的としています。 セキュリティ機能は果たしません。 Azurite で運用環境のストレージ アカウントとキーを使用することはできません。 開発アカウントを運用データで使用することは避けてください。
> 
> Azurite では HTTP 経由の接続のみがサポートされます。 ただし、運用環境の Azure ストレージ アカウント内のリソースにアクセスする際は、HTTPS が推奨されるプロトコルです。
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>ショートカットを使用してエミュレーター アカウントに接続する
アプリケーションから Azurite に接続する最も簡単な方法は、ショートカット `UseDevelopmentStorage=true` を参照するアプリケーションの構成ファイル内で接続文字列を構成することです。 たとえば、*app.config* ファイル内の Azurite の接続文字列は次のようになります。 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>よく知られたアカウント名とキーを使用してエミュレーター アカウントに接続する
エミュレーターのアカウント名とキーを参照する接続文字列を作成するには、接続文字列内のエミュレーターから使用する各サービスのエンドポイントを指定する必要があります。 これは、接続文字列が運用ストレージ アカウントに使用するものと異なるエミュレーター エンドポイントを参照するために必要です。 たとえば、接続文字列の値は次のようになります。

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

この値は、前述のショートカット `UseDevelopmentStorage=true`と同じです。
