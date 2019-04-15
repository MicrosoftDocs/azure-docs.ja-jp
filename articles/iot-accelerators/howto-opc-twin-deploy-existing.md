---
title: Azure IoT OPC UA デバイス管理モジュールを既存のプロジェクトにデプロイする方法 | Microsoft Docs
description: 既存のプロジェクトに OPC Twin をデプロイする方法。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: dcf6acca344fe2a34fdc48fe89c5a1ee62b10b23
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59255888"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>既存のプロジェクトに OPC Twin をデプロイする

OPC Device Twin モジュールは IoT Edge 上で動作し、OPC Device Twin および Registry サービスにいくつかのエッジ サービスを提供します。 

OPC Device Twin マイクロサービスは、OPC Twin IoT Edge モジュールを介して、工場オペレーターと、工場フロアにある OPC UA サーバー デバイスの間の通信を容易にします。 マイクロサービスは、その REST API を介して OPC UA サービス (参照、読み取り、書き込み、実行) を公開します。 

OPC UA Device Registry マイクロサービスは、登録済みの OPC UA アプリケーションとそのエンドポイントへのアクセスを提供します。 オペレーターと管理者は、新しい OPC UA アプリケーションを登録および登録解除したり、既存のアプリケーションをそのエンドポイントを含めて参照したりできます。 アプリケーションとエンドポイントの管理に加えて、レジストリ サービスは登録済みの OPC Device Twin IoT Edge モジュールのカタログ化も行います。 サービス API を使用して、エッジ モジュールの機能を制御できます。たとえば、サーバー検出 (スキャン サービス)を開始または停止したり、OPC Twin マイクロサービスを使用してアクセスできる新しいエンドポイント ツインをアクティブ化したりできます。

モジュールのコアはスーパーバイザー ID です。 スーパーバイザーはエンドポイント ツインを管理します。エンドポイント ツインは、対応する OPC UA レジストリ API を使用してアクティブ化される OPC UA サーバー エンドポイントに対応します。 このエンドポイント ツインは、クラウドで実行されている OPC Twin マイクロサービスから受信した OPC UA JSON を OPC UA バイナリ メッセージに変換し、このメッセージが、セキュリティで保護されたステートフル チャネル経由で管理対象エンドポイントに送信されます。 また、スーパーバイザーが提供する検出サービスは、デバイス検出イベントを OPC UA Device Onboarding サービスに送信して処理を委ね、これらのイベントの結果として OPC UA レジストリが更新されます。  この記事では、既存のプロジェクトに OPC Twin モジュールをデプロイする方法を示します。 

> [!NOTE]
> デプロイの詳細と手順については、GitHub の[リポジトリ](https://github.com/Azure/azure-iiot-opc-twin-module)を参照してください。

## <a name="prerequisites"></a>前提条件

PowerShell および [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) 拡張機能がインストールされていることを確認します。   まだの場合は、この GitHub リポジトリを複製します。  コマンド プロンプトまたはターミナルを開き、次を実行します。

```bash
git clone --recursive https://github.com/Azure/azure-iiot-components 
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>産業用 IoT サービスを Azure にデプロイする

1. 開いているコマンド プロンプトまたはターミナルで、次を実行します。

   ```bash
   deploy
   ```

2. プロンプトに従って、デプロイのリソース グループの名前と、Web サイトの名前を割り当てます。   スクリプトにより、マイクロサービスとその Azure プラットフォームの依存関係が、Azure サブスクリプションのリソース グループにデプロイされます。  また、OAUTH ベースの認証をサポートするために、スクリプトによって Azure Active Directory (AAD) テナントにアプリケーションが登録されます。  デプロイには数分かかります。  ソリューションが正常にデプロイされた後の表示の例を次に示します。

   ![既存のプロジェクトへの産業用 IoT OPC Twin のデプロイ](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   出力には、パブリック エンドポイントの URL が含まれています。 

3. スクリプトが正常に完了したら、.env ファイルを保存するかどうかを選択します。  .env 環境ファイルは、Console などのツールを使用してクラウド エンドポイントに接続する場合や、開発とデバッグのためにモジュールをデプロイする場合に必要になります。

## <a name="troubleshooting-deployment-failures"></a>デプロイ失敗のトラブルシューティング

### <a name="resource-group-name"></a>リソース グループ名

短くてシンプルなリソース グループ名を使用してください。  この名前はリソースの命名にも使用されるので、リソースの命名要件に従っている必要があります。  

### <a name="website-name-already-in-use"></a>既に使用されている Web サイト名

Web サイトの名前が既に使用されている可能性があります。  このエラーが発生した場合は、別のアプリケーション名を使用する必要があります。

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) の登録

デプロイ スクリプトは、Azure Active Directory に 2 つの AAD アプリケーションを登録しようとします。  選択した AAD テナントに対する権限によっては、デプロイが失敗する可能性があります。 2 つのオプションがあります。

1. テナントの一覧から AAD テナントを選択した場合は、スクリプトを再起動して別のものを一覧から選択します。
2. または、プライベート AAD テナントを別のサブスクリプションにデプロイし、スクリプトを再起動して、それを使用することを選択します。

> [!WARNING]
> 認証なしで続行しないでください。  そのようにした場合、誰もがインターネットから認証なしで OPC Device Management エンドポイントにアクセスできる状態になってしまいます。   いつでも、["local" デプロイ オプション](howto-opc-twin-deploy-dependencies.md)を選択することで簡単なチェックができます。

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>オールインワンの産業用 IoT サービス デモをデプロイする

サービスと依存関係だけでなく、オールインワンのデモをデプロイすることもできます。  オールインワンのデモには、3 つの OPC UA サーバー、OPC Twin モジュール、すべてのマイクロサービス、およびサンプル Web アプリケーションが含まれています。  これはデモンストレーション用です。

1. リポジトリの複製があることを確認します (上記参照)。 リポジトリのルートでコマンド プロンプトまたはターミナルを開き、次を実行します。

   ```bash
   deploy -type demo
   ```

2. プロンプトに従って、リソース グループの新しい名前と、Web サイトの名前を割り当てます。  正常にデプロイされると、スクリプトは Web アプリケーション エンドポイントの URL を表示します。

## <a name="deployment-script-options"></a>デプロイ スクリプトのオプション

スクリプトは次のパラメーターを受け取ります。

```bash
-type
```

デプロイの種類 (vm、local、demo)

```bash
-resourceGroupName
```

既存または新規のリソース グループの名前にすることができます。

```bash
-subscriptionId
```

(省略可能) リソースがデプロイされるサブスクリプション ID。

```bash
-subscriptionName
```

またはサブスクリプション名。

```bash
-resourceGroupLocation
```

(省略可能) リソース グループの場所。 指定した場合、この場所に新しいリソース グループを作成しようとします。

```bash
-aadApplicationName
```

登録する AAD アプリケーションの名前。 

```bash
-tenantId
```

使用する AAD テナント。

```bash
-credentials
```

## <a name="next-steps"></a>次の手順

ここでは、OPC Twin を既存のプロジェクトにデプロイする方法を学習しました。次に以下の記事を読むことをお勧めします。

> [!div class="nextstepaction"]
> [OPC クライアントと OPC PLC の通信をセキュリティで保護する](howto-opc-vault-deploy-existing-client-plc-communication.md)
