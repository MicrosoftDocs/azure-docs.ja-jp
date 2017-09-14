---
title: "Azure IoT Suite コネクテッド ファクトリについての FAQ | Microsoft Docs"
description: "IoT Suite コネクテッド ファクトリに関してよく寄せられる質問"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: corywink
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 35cf824210a14410d7ea2aedddde0040309901f9
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---
# <a name="frequently-asked-questions-for-iot-suite-connected-factory-preconfigured-solution"></a>Azure IoT Suite コネクテッド ファクトリ事前構成済みソリューションに関してよく寄せられる質問

IoT Suite の一般的な [FAQ](iot-suite-faq.md) もご覧ください。

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solution"></a>事前構成済みソリューションのソース コードはどこで入手できますか

ソース コードは、次の GitHub リポジトリに格納されています。

* [コネクテッド ファクトリの事前構成済みソリューション](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>OPC UA とは

OPC Unified Architecture (UA) は、プラットフォームに依存しないサービス指向の相互運用性の標準規格として、2008 年にリリースされました。 OPC UA は、さままざまな産業システムやデバイス (例: 産業用 PC、PLC、センサー) で使用されています。 OPC UA では、セキュリティを組み込まれた 1 つの拡張可能なフレームワークに、OPC Classic 仕様の機能が統合されています。 これは、OPC Foundation によって主導されている標準です。 [OPC Foundation](http://opcfoundation.org/) は440 社以上の会員が参加する非営利組織です。 組織の目標は、OPC 仕様によってマルチ ベンダー、マルチ プラットフォーム、および安全性と信頼性の高い相互運用を促進することにあります。

* インフラストラクチャ
* 仕様
* テクノロジ
* 処理

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-preconfigured-solution"></a>Microsoft が接続済みファクトリの事前に構成されたソリューションとして OPC UA を選んだ理由

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

www.azureiotsuite.com からソリューションをデプロイした場合は、VM にサインインすることはできません。 パスワードはランダムに生成され、リセットはできないため、サインインできません。

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
    * シェル変数を **IOTHUB_CONNECTIONSTRING** という名前でエクスポートします。 `<name of your deployment>.config.user` ファイルの **IotHubOwnerConnectionString** 設定の値を使用します。 For example:

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * `./startsimulation` を実行します。

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>VM のシミュレーションを更新するにはどうすればいいですか

シミュレーションに何らかの変更を加えた場合は、`updatedimulation` コマンドを使用すると、[リポジトリ](https://github.com/Azure/azure-iot-connected-factory)内の `build.ps1` PowerShell script を使用できます。 このスクリプトはすべてのシミュレーション コンポーネントをビルドして、VM 上のシミュレーションの停止、アップロード、インストール、および開始を行います。

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>ソリューションで使用される IoT ハブの接続文字列を探すにはどうすればいいですか

[リポジトリ](https://github.com/Azure/azure-iot-connected-factory)の `build.ps1` スクリプトを使用してソリューションをデプロイした場合、接続文字列は `<name of your deployment>.config.user` ファイル内の **IotHubOwnerConnectionString** の値になります。

次のように Azure Portal を使用して、接続文字列を検索することもできます。 デプロイ環境のリソース グループに含まれる IoT Hub リソースから、接続文字列の設定を見つけます。

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>コネクテッド ファクトリのシミュレーションは、どの IoT Hub デバイスを使用しますか。

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

[DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) または [iothub-explorer](https://github.com/azure/iothub-explorer) ツールを使用すると、ソリューションが使用している IoT ハブに、どのデバイスが登録されているかを確認できます。 これらのツールを使用するには、デプロイ環境の IoT ハブ用の接続文字列が必要になります。

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>シミュレーション コンポーネントからログ データを取得するにはどうすればいいですか

シミュレーションのすべてのコンポーネントは、ログ ファイルに情報が記録されます。 これらのファイルは `home/docker/Logs` フォルダーの VM 内にあります。 ログを取得するには、[リポジトリ](https://github.com/Azure/azure-iot-connected-factory)内の `Simulation/Factory/Get-SimulationLogs.ps1` PowerShell スクリプトを使用できます。

スクリプトを使用するには VM にサインインする必要があります。 サインイン時には資格情報を入力しなければならない場合があります。 資格情報を探すには、「[シミュレーション VM にサインインするにはどうすればいいですか](#how-do-i-sign-in-to-the-simulation-vm)」をご覧ください。

スクリプトは、VM がパブリック IP アドレスを持っていない/削除していない場合は、パブリック IP の追加/削除を行います。 このスクリプトはすべてのログ ファイルをアーカイブに保存し、開発用ワークステーションにダウンロードします。

または SSH を使用して VM にログインし、実行時にログ ファイルを検査します。

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>シミュレーションがクラウドにデータを送信していることを確認するにはどうすればいいですか

[DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) または [iothub-explorer](https://github.com/azure/iothub-explorer) ツールを使用すると、特定のデバイスから IoT Hub に送信されるデータを検査できます。 これらのツールを使用するには、デプロイ環境の IoT ハブ用の接続文字列を知っている必要があります。 「[ソリューションで使用される IoT ハブの接続文字列を探すにはどうすればいいですか](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)」をご覧ください。

発行元デバイスの 1 つから送信されるデータを検査します。

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

IoT Hub に送信されるデータが表示されない場合は、シミュレーションに問題があります。 分析の最初の手順として、シミュレーション コンポーネントのログ ファイルを分析する必要があります。 「[シミュレーション コンポーネントからログ データを取得するにはどうすればいいですか](#how-can-i-get-log-data-from-the-simulation-components)」をご覧ください。 次にシミュレーションの停止と開始を試行して、送信データが存在しない状態から変わらない場合は、シミュレーションをすべて更新します。 「[VM のシミュレーションを更新するにはどうすればいいですか](#how-do-i-update-the-simulation-in-the-vm)」をご覧ください。

### <a name="next-steps"></a>次のステップ

IoT Suite の事前構成済みのソリューションの他の機能について学習できます。

* [予測メンテナンスの構成済みソリューションの概要](iot-suite-predictive-overview.md)
* [コネクテッド ファクトリ事前構成済みソリューションの概要](iot-suite-connected-factory-overview.md)
* [徹底的な IoT セキュリティ](securing-iot-ground-up.md)
