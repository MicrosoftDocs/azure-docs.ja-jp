---
title: 接続済みファクトリ ソリューションの FAQ - Azure | Microsoft Docs
description: 接続済みファクトリ ソリューション アクセラレータに関してよく寄せられる質問
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: ed429d923cad2c715621990c146d4cf3a23e7bca
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57904755"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>接続済みファクトリ ソリューション アクセラレータに関してよく寄せられる質問

IoT ソリューション アクセラレータの一般的な [FAQ](iot-accelerators-faq.md) もご覧ください。

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>ソリューション アクセラレータのソース コードはどこで入手できますか

ソース コードは、次の GitHub リポジトリに格納されています。

* [接続済みファクトリ ソリューション アクセラレータ](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>OPC UA とは

OPC Unified Architecture (UA) は、プラットフォームに依存しないサービス指向の相互運用性の標準規格として、2008 年にリリースされました。 OPC UA は、さままざまな産業システムやデバイス (例: 産業用 PC、PLC、センサー) で使用されています。 OPC UA では、セキュリティを組み込まれた 1 つの拡張可能なフレームワークに、OPC Classic 仕様の機能が統合されています。 これは、OPC Foundation によって主導されている標準です。 [OPC Foundation](https://opcfoundation.org/) は440 社以上の会員が参加する非営利組織です。 組織の目標は、OPC 仕様によってマルチ ベンダー、マルチ プラットフォーム、および安全性と信頼性の高い相互運用を促進することにあります。

* インフラストラクチャ
* 仕様
* テクノロジ
* 処理

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Microsoft が接続済みファクトリ ソリューション アクセラレータに OPC UA を選んだ理由

Microsoft が OPC UA を選んだのは、それがオープンであり、特定の企業が占有する技術ではなく、プラットフォームから独立しており、業界で認められた実績のある標準であるためです。 OPC UA は、製造プロセスと機器の広範なセットの間の相互運用性を保証する Industrie 4.0 (RAMI4.0) 参照アーキテクチャ ソリューションの要件になっています。 Microsoft には、お客様から Industrie 4.0 ソリューション構築の要望が寄せられています。 OPC UA をサポートすることで、お客様が目標を達成するときの障害が低くなり、ビジネス価値がお客様に直接提供されます。

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>パブリック IP アドレスをシミュレーション VM に追加するにはどうすればいいですか

IP アドレスを追加するには次の 2 つのオプションがあります。

* [リポジトリ](https://github.com/Azure/azure-iot-connected-factory)で `Simulation/Factory/Add-SimulationPublicIp.ps1` PowerShell スクリプトを使用する。 パラメーターとしてデプロイ名を渡します。 ローカル デプロイの場合は、`<your username>ConnFactoryLocal` を使用します。 このスクリプトは VM の IP アドレスを出力します。

* Azure Portal でデプロイ環境のリソース グループを見つけます。 ローカル デプロイの場合を除き、リソース グループは、ソリューション名またはデプロイ名に指定した名前になります。 ビルド スクリプトを使用したローカル デプロイの場合は、リソース グループの名前は `<your username>ConnFactoryLocal` になります。 ここで新しい**パブリック IP アドレス** リソースをリソース グループに追加します。

> [!NOTE]
> いずれの場合でも、[Ubuntu Web サイト](https://wiki.ubuntu.com/Security/Upgrades)の次の手順を行って、最新のパッチをインストールするようにします。 できる限り長期にわたってパブリック IP アドレスからVMにアクセスできるようにするため、インストールを最新に保ってください。

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>シミュレーション VM のパブリック IP アドレスを削除するにはどうすればいいですか

IP アドレスを削除するには次の 2 つのオプションがあります。

* [リポジトリ](https://github.com/Azure/azure-iot-connected-factory)の Simulation/Factory/Remove-SimulationPublicIp.ps1 PowerShell スクリプトを使用する。 パラメーターとしてデプロイ名を渡します。 ローカル デプロイの場合は、`<your username>ConnFactoryLocal` を使用します。 このスクリプトは VM の IP アドレスを出力します。

* Azure Portal でデプロイ環境のリソース グループを見つけます。 ローカル デプロイの場合を除き、リソース グループは、ソリューション名またはデプロイ名に指定した名前になります。 ビルド スクリプトを使用したローカル デプロイの場合は、リソース グループの名前は `<your username>ConnFactoryLocal` になります。 ここでリソース グループから**パブリック IP アドレス** リソースを削除します。

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>シミュレーション VM にサインインするにはどうすればいいですか

シミュレーション VM へのサインインは、[リポジトリ](https://github.com/Azure/azure-iot-connected-factory)の `build.ps1` PowerShell スクリプトを使用してソリューションをデプロイしている場合にのみサポートされます。

www.azureiotsolutions.com からソリューションをデプロイした場合は、VM にサインインすることはできません。 パスワードはランダムに生成され、リセットはできないため、サインインできません。

1. VM にパブリック IP アドレスを追加します。 「[パブリック IP アドレスをシミュレーション VM に追加するにはどうすればいいですか](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)」をご覧ください。
1. VM の IP アドレスを使用して VM への SSH セッションを作成します。
1. 使用するユーザー名は `docker` です。
1. 使用するパスワードは、デプロイに使用したバージョンによって異なります。
    * 2017 年 6 月 1 日よりも前に build.ps1 スクリプトを使用してソリューションをデプロイした場合は、パスワードは `Passw0rd` です。
    * 2017 年 6 月 1 日よりも後に build.ps1 スクリプトを使用してソリューションをデプロイした場合は、`<name of your deployment>.config.user` ファイル内にパスワードがあります。 パスワードは **VmAdminPassword** 設定に保存されています。 パスワードは `build.ps1` スクリプト パラメーター `-VmAdminPassword` を使用して指定しない限り、デプロイ時にランダムに生成されます。

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>シミュレーション VM のすべての Docker プロセスを停止して開始するにはどうすればいいですか

1. シミュレーション VM にサインインします。 「[シミュレーション VM にサインインするにはどうすればいいですか](#how-do-i-sign-in-to-the-simulation-vm)」をご覧ください。
1. アクティブなコンテナーを確認するには、`docker ps` を実行します。
1. すべてのシミュレーション コンテナーを停止するには、`./stopsimulation` を実行します。
1. すべてのシミュレーション コンテナーを開始するには、次のようにします。
    * シェル変数を **IOTHUB_CONNECTIONSTRING** という名前でエクスポートします。 `<name of your deployment>.config.user` ファイルの **IotHubOwnerConnectionString** 設定の値を使用します。 例: 

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * `./startsimulation` を実行します。

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>VM のシミュレーションを更新するにはどうすればいいですか

シミュレーションに何らかの変更を加えた場合は、`updatedimulation` コマンドを使用すると、[リポジトリ](https://github.com/Azure/azure-iot-connected-factory)内の `build.ps1` PowerShell script を使用できます。 このスクリプトはすべてのシミュレーション コンポーネントをビルドして、VM 上のシミュレーションの停止、アップロード、インストール、および開始を行います。

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>ソリューションで使用される IoT ハブの接続文字列を探すにはどうすればいいですか

[リポジトリ](https://github.com/Azure/azure-iot-connected-factory)の `build.ps1` スクリプトを使用してソリューションをデプロイした場合、接続文字列は `<name of your deployment>.config.user` ファイル内の **IotHubOwnerConnectionString** の値になります。

次のように Azure Portal を使用して、接続文字列を検索することもできます。 デプロイ環境のリソース グループに含まれる IoT Hub リソースから、接続文字列の設定を見つけます。

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>接続済みファクトリのシミュレーションは、どの IoT ハブ デバイスを使用しますか

シミュレーション自体が次のデバイスを登録します。

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

[DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) または [Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)ツールを使うと、ソリューションが使っている IoT ハブに、どのデバイスが登録されているかを確認できます。 Device Explorer を使うには、デプロイ環境の IoT ハブ用の接続文字列が必要になります。 Azure CLI 向け IoT 拡張機能を使うには、IoT Hub の名前が必要です。

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>シミュレーション コンポーネントからログ データを取得するにはどうすればいいですか

シミュレーションのすべてのコンポーネントは、ログ ファイルに情報が記録されます。 これらのファイルは `home/docker/Logs` フォルダーの VM 内にあります。 ログを取得するには、[リポジトリ](https://github.com/Azure/azure-iot-connected-factory)内の `Simulation/Factory/Get-SimulationLogs.ps1` PowerShell スクリプトを使用できます。

スクリプトを使用するには VM にサインインする必要があります。 サインイン時には資格情報を入力しなければならない場合があります。 資格情報を探すには、「[シミュレーション VM にサインインするにはどうすればいいですか](#how-do-i-sign-in-to-the-simulation-vm)」をご覧ください。

スクリプトは、VM がパブリック IP アドレスを持っていない/削除していない場合は、パブリック IP の追加/削除を行います。 このスクリプトはすべてのログ ファイルをアーカイブに保存し、開発用ワークステーションにダウンロードします。

または SSH を使用して VM にログインし、実行時にログ ファイルを検査します。

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>シミュレーションがクラウドにデータを送信していることを確認するにはどうすればいいですか

[DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) または [Azure IoT CLI Extension monitor-events](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) コマンドを使用すると、特定のデバイスから IoT Hub に送信されるデータを検査できます。 これらのツールを使用するには、デプロイ環境の IoT ハブ用の接続文字列を知っている必要があります。 「[ソリューションで使用される IoT ハブの接続文字列を探すにはどうすればいいですか](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)」をご覧ください。

発行元デバイスの 1 つから送信されるデータを検査します。

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

IoT Hub に送信されるデータが表示されない場合は、シミュレーションに問題があります。 分析の最初の手順として、シミュレーション コンポーネントのログ ファイルを分析する必要があります。 「[シミュレーション コンポーネントからログ データを取得するにはどうすればいいですか](#how-can-i-get-log-data-from-the-simulation-components)」をご覧ください。 次にシミュレーションの停止と開始を試行して、送信データが存在しない状態から変わらない場合は、シミュレーションをすべて更新します。 「[VM のシミュレーションを更新するにはどうすればいいですか](#how-do-i-update-the-simulation-in-the-vm)」をご覧ください。

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>接続済みファクトリ ソリューションでインタラクティブ マップを有効にするにはどうすればいいですか

接続済みファクトリ ソリューション内でインタラクティブ マップを有効にするには、Azure Maps アカウントが必要です。

[www.azureiotsolutions.com](https://www.azureiotsolutions.com) からデプロイするときに、デプロイ プロセスによって、ソリューション アクセラレータ サービスを含むリソース グループに Azure Maps アカウントが追加されます。

接続済みファクトリの GitHub リポジトリにある `build.ps1` スクリプトを使ってデプロイする場合は、ビルド ウィンドウの環境変数 `$env:MapApiQueryKey` を [Azure Maps アカウントのキー](../azure-maps/how-to-manage-account-keys.md)に設定します。 このようにすると、インタラクティブ マップが自動的に有効になります。

デプロイ後に、ソリューション アクセラレータに Azure Maps アカウント キーを追加することもできます。 Azure Portal に移動し、接続済みファクトリの展開の App Service リソースにアクセスします。 **[アプリケーション設定]** に移動し、**[アプリケーション設定]** セクションを探します。 **MapApiQueryKey** を [Azure Maps アカウントのキー](../azure-maps/how-to-manage-account-keys.md)に設定します。 設定を保存した後、**[概要]** に移動して、App Service を再起動します。

### <a name="how-do-i-create-an-azure-maps-account"></a>Azure Maps アカウントを作成するにはどうすればいいですか

「[Azure Maps のアカウントとキーを管理する方法](../azure-maps/how-to-manage-account-keys.md)」をご覧ください。

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Azure Maps アカウント キーを取得するにはどうすればいいですか

「[Azure Maps のアカウントとキーを管理する方法](../azure-maps/how-to-manage-account-keys.md)」をご覧ください。

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>ローカルでのデバッグ中にインタラクティブ マップを有効にするにはどうすればいいですか

ローカルでのデバッグ中にインタラクティブ マップを有効にするには、デプロイのルートにあるファイル `local.user.config` と `<yourdeploymentname>.user.config` で設定 `MapApiQueryKey` の値を、事前にコピーしておいた **QueryKey** の値に設定します。

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>ダッシュボードのホーム ページに別のイメージを使用するにはどうすればいいですか

ダッシュボードのホーム ページに表示される静的イメージを変更するには、イメージ `WebApp\Content\img\world.jpg` を置き換えます。 その後で、Web アプリをリビルドして再デプロイします。

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>OPC UA 非対応デバイスを接続済みファクトリと共に使用するにはどうすればいいですか

OPC UA 非対応デバイスから接続済みファクトリに利用統計情報を送信するには、次の手順に従います。

1. `ContosoTopologyDescription.json` ファイルで[接続済みファクトリ トポロジに新しいステーションを構成](iot-accelerators-connected-factory-configure.md)します。

1. 接続済みファクトリと互換性のある次のような JSON 形式で利用統計情報を取り込みます。

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. `<timestamp>` の書式は `2017-12-08T19:24:51.886753Z` です。

1. 接続済みファクトリ App Service を再起動します。

### <a name="next-steps"></a>次の手順

IoT ソリューション アクセラレータの他の機能についても学習できます。

* [予測メンテナンスのソリューション アクセラレータの概要](iot-accelerators-predictive-overview.md)
* [接続済みファクトリ ソリューション アクセラレータの展開](quickstart-connected-factory-deploy.md)
* [徹底的な IoT セキュリティ](/azure/iot-fundamentals/iot-security-ground-up)
