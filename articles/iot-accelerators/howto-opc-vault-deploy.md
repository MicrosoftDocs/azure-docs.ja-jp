---
title: OPC Vault 証明書管理サービスをデプロイする方法 - Azure | Microsoft Docs
description: OPC Vault 証明書管理サービスをゼロからデプロイする方法。
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1625f0e6f9bfe8297cae2770e63107bf4f19f95e
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012985"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>OPC Vault 証明書管理サービスをビルドしてデプロイする

この記事では、Azure で OPC Vault 証明書管理サービスをデプロイする方法について説明します。

> [!NOTE]
> デプロイの詳細と手順については、GitHub の「[OPC Vault repository](https://github.com/Azure/azure-iiot-opc-vault-service)」 (OPC Vault リポジトリ) を参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="install-required-software"></a>必要なソフトウェアのインストール

現在、ビルドおよびデプロイ操作は Windows に限定されています。
サンプルはすべて、デプロイ用のサービスとサンプルをビルドするために必要な .Net C# Standard 用に記述されています。
.Net Standard に必要なすべてのツールには、.Net Core ツールが付属しています。 必要なものについては、[こちら](https://docs.microsoft.com/dotnet/articles/core/getting-started)を参照してください。

1. [.NET Core 2.1 以降をインストールします][dotnet-install]。
2. [Docker をインストールします][docker-url] (省略可能。ローカルの Docker ビルドが必要な場合のみ)。
4. [PowerShell 用 Azure コマンドライン ツール][powershell-install]をインストールします。
5. [Azure サブスクリプション][azure-free]にサインアップします。

### <a name="clone-the-repository"></a>リポジトリの複製

まだの場合は、この GitHub リポジトリを複製します。  コマンド プロンプトまたはターミナルを開き、次を実行します。

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

または、Visual Studio 2017 で直接リポジトリを複製します。

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Windows で Azure サービスをビルドしてデプロイする

Powershell スクリプトを使用すると、OPC Vault マイクロサービスとアプリケーションを簡単にデプロイできます。<br>

1. リポジトリのルートで Powershell ウィンドウを開きます。 
3. デプロイ フォルダー `cd deploy` に移動します
3. 他のデプロイされた Web ページとの競合を引き起こす可能性が低い、`myResourceGroup` の名前を選択します。 リソース グループの名前に基づいて Web ページの名前を選択する方法については、[後述](#website-name-already-in-use)を参照してください。
5. デプロイを開始するには、`.\deploy.ps1` を入力して対話型インストールを行います。<br>
または、完全なコマンドラインを入力します。  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. このデプロイで開発する予定の場合は、`-development 1` を追加して Swagger UI を有効にし、デバッグ ビルドをデプロイします。
6. スクリプトの指示に従って、サブスクリプションにサインインし、追加情報を提供します。
9. ビルドとデプロイの操作が正常に行わると、次のメッセージが表示されるはずです。
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

問題が発生した場合は、[後述](#troubleshooting-deployment-failures)の手順に従います。

8. お気に入りのブラウザーを開き、アプリケーション ページ `https://myResourceGroup.azurewebsites.net` を開きます。
8. デプロイ後に Web アプリと OPC Vault マイクロサービスがウォーム アップされるまで数分かかります。 最初の使用時は、最初の応答が得られるまで Web ホーム ページが最大 1 分間反応しないことがあります。
11. Swagger API を確認するには、`https://myResourceGroup-service.azurewebsites.net` を開きます
13. ローカルの GDS サーバーを起動するには、dotnet を使用する場合は `.\myResourceGroup-gds.cmd` を開始し、docker を使用する場合は `.\myResourceGroup-dockergds.cmd` を開始します。

ご参考までに、まったく同じ設定を使用してビルドを再デプロイすることができます。 このような操作では、すべてのアプリケーション シークレットが更新され、Azure Active Directory (Azure AD) アプリケーションの登録の一部の設定がリセットされる可能性があることに注意してください。

Web アプリ バイナリのみを再デプロイすることもできます。 パラメーター `-onlyBuild 1` を指定すると、サービスとアプリの新しい zip パッケージが Web アプリケーションにデプロイされます。

デプロイが正常に完了したら、サービスの使用をいつでも開始できます (「[OPC Vault 証明書サービスを管理する方法](howto-opc-vault-manage.md)」)。

## <a name="delete-the-services-from-the-subscription"></a>サブスクリプションからサービスを削除する

1. Azure portal `https://portal.azure.com` にサインインします。
2. サービスがデプロイされたリソース グループに移動します。
3. `Delete resource group` を選択して確認します。
4. しばらくすると、デプロイされたすべてのサービス コンポーネントが削除されます。
5. ここで、`Azure Active Directory/App registrations` に移動します。
6. デプロイされたリソース グループごとに、`resourcegroup-client`、`resourcegroup-module`、`resourcegroup-service` という名前の 3 つの登録がリストされるはずです。
登録ごとに個別に削除する必要があります。
7. これで、デプロイされたすべてのコンポーネントが削除されます。

## <a name="troubleshooting-deployment-failures"></a>デプロイ失敗のトラブルシューティング

### <a name="resource-group-name"></a>リソース グループ名

短くてシンプルなリソース グループ名を使用してください。  この名前は、リソースとサービス URL プレフィックスに名前を付けるためにも使用されるため、リソースの名前付け要件に準拠している必要があります。  

### <a name="website-name-already-in-use"></a>既に使用されている Web サイト名

Web サイトの名前が既に使用されている可能性があります。  このエラーが発生した場合は、別のリソース グループ名を使用する必要があります。 デプロイ スクリプトで使用されているホスト名は、 https://resourcegroupname.azurewebsites.net と https://resourgroupname-service.azurewebsites.net とです。
その他のサービス名は、短い名前ハッシュの組み合わせによってビルドされ、他のサービスと競合する可能性は低くなります。

### <a name="azure-active-directory-azure-ad-registration"></a>Azure Active Directory (Azure AD) の登録 

デプロイ スクリプトでは、Azure Active Directory への 3 つの Azure AD アプリケーションの登録を試行します。  
選択された Azure AD テナントのアクセス許可によっては、この操作が失敗する可能性があります。   2 つのオプションがあります。

1. テナントの一覧から Azure AD テナントを選択した場合は、スクリプトを再起動して別のものを一覧から選択します。
2. または、プライベート Azure AD テナントを別のサブスクリプションにデプロイし、スクリプトを再起動して、それを使用することを選択します。

## <a name="deployment-script-options"></a>デプロイ スクリプトのオプション

スクリプトは次のパラメーターを受け取ります。


```
-resourceGroupName
```

既存または新規のリソース グループの名前にすることができます。

```
-subscriptionId
```


(省略可能) リソースがデプロイされるサブスクリプション ID。

```
-subscriptionName
```


または、サブスクリプション名。

```
-resourceGroupLocation
```


(省略可能) リソース グループの場所。 指定した場合、この場所に新しいリソース グループを作成しようとします。


```
-tenantId
```


使用する Azure AD テナント。 

```
-development 0|1
```

省略可能。開発用にデプロイします。 デバッグ ビルドを使用して、ASP.Net 環境を開発に設定します。 Visual Studio 2017 でインポート用に '.publishsettings' を作成し、アプリとサービスを直接デプロイできるようにします。

```
-onlyBuild 0|1
```

省略可能。Web アプリのみをリビルドして再デプロイし、Docker コンテナーをリビルドします。

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>次の手順

ここでは、OPC Vault をゼロからデプロイする方法を学習しました。推奨される次の手順は次のとおりです。

> [!div class="nextstepaction"]
> [OPC Vault を管理する](howto-opc-vault-manage.md)
