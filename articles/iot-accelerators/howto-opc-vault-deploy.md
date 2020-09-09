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
ms.openlocfilehash: 3c6385ff804b047cca11587ce5da5a0a682fdce8
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84307930"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>OPC Vault 証明書管理サービスをビルドしてデプロイする

この記事では、Azure で OPC Vault 証明書管理サービスをデプロイする方法について説明します。

> [!NOTE]
> 詳細については、GitHub の [OPC Vault リポジトリ](https://github.com/Azure/azure-iiot-opc-vault-service)を参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="install-required-software"></a>必要なソフトウェアのインストール

現在、ビルドおよびデプロイ操作は Windows に限定されています。
サンプルはすべて C# .NET Standard 用に記述されているので、デプロイするためにサービスとサンプルをビルドするにはそれが必要です。
.NET Standard に必要なすべてのツールは、.NET Core ツールに付属しています。 「[.NET Core の概要](https://docs.microsoft.com/dotnet/articles/core/getting-started)」を参照してください。

1. [.NET Core 2.1 以降をインストールします][dotnet-install]。
2. [Docker をインストールします][docker-url] (省略可能。Docker のローカル ビルドが必要な場合のみ)。
4. [PowerShell 用 Azure コマンドライン ツール][powershell-install]をインストールします。
5. [Azure サブスクリプション][azure-free]にサインアップします。

### <a name="clone-the-repository"></a>リポジトリの複製

まだの場合は、この GitHub リポジトリを複製します。 コマンド プロンプトまたはターミナルを開き、次を実行します。

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

または、Visual Studio 2017 でリポジトリを直接複製してもかまいません。

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Windows で Azure サービスをビルドしてデプロイする

PowerShell スクリプトを使用すると、OPC Vault マイクロサービスとアプリケーションを簡単にデプロイできます。

1. リポジトリのルートで PowerShell ウィンドウを開きます。 
3. デプロイ フォルダー `cd deploy` に移動します。
3. 他のデプロイされた Web ページとの競合を引き起こす可能性が低い、`myResourceGroup` の名前を選択します。 後の「既に使用されている Web サイト名」セクションを参照してください。
5. `.\deploy.ps1` と入力して対話型インストールでデプロイを始めるか、または完全なコマンド ラインを入力します。  
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

   > [!NOTE]
   > 問題が発生した場合は、後の「デプロイ失敗のトラブルシューティング」セクションを参照してください。

8. 好みのブラウザーを開き、アプリケーション ページ `https://myResourceGroup.azurewebsites.net` を開きます
8. デプロイ後に Web アプリと OPC Vault マイクロサービスがウォーム アップされるまで数分かかります。 最初の使用時は、最初の応答が得られるまで Web ホーム ページが最大 1 分間応答が停止することがあります。
11. Swagger API を確認するには、`https://myResourceGroup-service.azurewebsites.net` を開きます
13. dotnet でローカルの GDS サーバーを起動するには、`.\myResourceGroup-gds.cmd` を開始します。 Docker の場合は、`.\myResourceGroup-dockergds.cmd` を開始します。

まったく同じ設定を使用して、ビルドを再デプロイすることができます。 このような操作では、すべてのアプリケーション シークレットが更新され、Azure Active Directory (Azure AD) アプリケーションの登録の一部の設定がリセットされる可能性があることに注意してください。

Web アプリ バイナリのみを再デプロイすることもできます。 パラメーター `-onlyBuild 1` を指定すると、サービスとアプリの新しい zip パッケージが Web アプリケーションにデプロイされます。

デプロイが正常に完了したら、サービスを使い始めることができます。 「[OPC Vault 証明書サービスを管理する方法](howto-opc-vault-manage.md)」を参照してください。

## <a name="delete-the-services-from-the-subscription"></a>サブスクリプションからサービスを削除する

その方法は次のとおりです。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. サービスがデプロイされたリソース グループに移動します。
3. **[リソース グループの削除]** を選択し、確認します。
4. しばらくすると、デプロイされたすべてのサービス コンポーネントが削除されます。
5. **[Azure Active Directory]**  >  **[アプリの登録]** に移動します。
6. デプロイされたリソース グループごとに、3 つの登録が一覧に表示されるはずです。 登録の名前は、`resourcegroup-client`、`resourcegroup-module`、`resourcegroup-service` です。 各登録を個別に削除します。

これで、デプロイされたすべてのコンポーネントが削除されます。

## <a name="troubleshooting-deployment-failures"></a>デプロイ失敗のトラブルシューティング

### <a name="resource-group-name"></a>リソース グループ名

短くてシンプルなリソース グループ名を使用します。 その名前は、リソースとサービス URL プレフィックスの名前にも使用されます。 そのため、リソースの名前付け要件に準拠している必要があります。  

### <a name="website-name-already-in-use"></a>既に使用されている Web サイト名

Web サイトの名前が既に使用されている可能性があります。 別のリソース グループ名を使用する必要があります。 デプロイ スクリプトで使用されているホスト名は https:\//resourcegroupname.azurewebsites.net と https:\//resourgroupname-service.azurewebsites.net です。
他のサービス名は、短い名前ハッシュの組み合わせによって作成され、他のサービスと競合する可能性は低くなります。

### <a name="azure-ad-registration"></a>Azure AD の登録 

デプロイ スクリプトでは、Azure AD への 3 つの Azure AD アプリケーションの登録が試みられます。 選択された Azure AD テナントのアクセス許可によっては、この操作が失敗する可能性があります。 2 つのオプションがあります。

- テナントの一覧から Azure AD テナントを選択した場合は、スクリプトを再起動して別のものを一覧から選択します。
- または、別のサブスクリプションにプライベート Azure AD テナントをデプロイします。 スクリプトを再起動し、それを選択して使用します。

## <a name="deployment-script-options"></a>デプロイ スクリプトのオプション

スクリプトは次のパラメーターを受け取ります。


```
-resourceGroupName
```

これは、既存または新規のリソース グループの名前にすることができます。

```
-subscriptionId
```


これは、リソースがデプロイされるサブスクリプション ID です。 省略可能です。

```
-subscriptionName
```


または、サブスクリプション名を使用できます。

```
-resourceGroupLocation
```


これは、リソース グループの場所です。 このパラメーターを指定すると、この場所への新しいリソース グループの作成が試みられます。 このパラメーターも省略可能です。


```
-tenantId
```


これは、使用する Azure AD テナントです。 

```
-development 0|1
```

これは、開発用にデプロイするためのものです。 デバッグ ビルドを使用し、開発のための ASP.NET 環境を設定します。 Visual Studio 2017 でのインポート用に `.publishsettings` を作成し、アプリとサービスを直接デプロイできるようにします。 このパラメーターも省略可能です。

```
-onlyBuild 0|1
```

これは、Web アプリのみをリビルドして再デプロイし、Docker コンテナーをリビルドするためのものです。 このパラメーターも省略可能です。

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>次のステップ

ここでは、OPC Vault をゼロからデプロイする方法を学習しました。次のことができます。

> [!div class="nextstepaction"]
> [OPC Vault を管理する](howto-opc-vault-manage.md)
