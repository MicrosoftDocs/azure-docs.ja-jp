---
title: Azure IoT Edge 開発環境 | Microsoft Docs
description: サポートされているシステムと、IoT Edge モジュールを作成するのに役立つファースト パーティの開発ツールについて説明します
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ddfa93328fb3533a937cc7f0d81482b66275faf3
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67848959"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>IoT Edge のための開発およびテスト環境の準備

Azure IoT Edge によって、既存のビジネス ロジックをエッジで稼働するデバイスに移行できます。 [IoT Edge モジュール](iot-edge-modules.md)として実行するためにアプリケーションやワークロードを準備するには、それらをコンテナーとしてビルドする必要があります。 この記事では、IoT Edge ソリューションを正常に作成できるように開発環境を構成する方法について説明します。 開発環境を設定した後、[独自の IoT Edge モジュールの開発](module-development.md)方法についても学習することができます。

すべての IoT Edge ソリューションでは、少なくとも 2 台のマシンについて考慮する必要があります。 1 台目は、IoT Edge デバイス自体で、IoT Edge モジュールを実行します。 もう 1 台は、モジュールのビルド、テスト、配置に使用する開発用マシンです。 この記事では主に、開発用マシンについて説明します。 テスト目的の場合は、これらの 2 台のマシンを同じマシンにすることができます。 開発マシンで IoT Edge デバイスを実行し、モジュールを配置することができます。

## <a name="operating-system"></a>オペレーティング システム

Azure IoT Edge は、特定の[サポートされているオペレーティング システム](support.md)で実行されます。 IoT Edge 開発では、コンテナー エンジンを実行できるほとんどのオペレーティング システムを使用できます。 コンテナーとしてモジュールをビルドし、コンテナー レジストリにプッシュするために、コンテナー エンジンが開発用マシンで必要になります。 

開発用マシンで Azure IoT Edge を実行できない場合は、ローカルでテストおよびデバッグするのに役立つ、この記事の「[テスト ツール](#testing-tools)」を参照してください。 

開発用マシンのオペレーティング システムは、IoT Edge デバイスのオペレーティング システムと必ずしも一致している必要はありません。 ただし、コンテナーのオペレーティング システムは、開発用マシンと IoT Edge デバイスの間で一致している必要があります。 たとえば、Windows マシンでモジュールを開発して、Linux デバイスにそのモジュールを配置することができます。 Windows マシンで、Linux デバイス用のモジュールをビルドするために Linux コンテナーを実行する必要があります。 

## <a name="container-engine"></a>コンテナー エンジン

IoT Edge の主要概念は、コンテナーにパッケージ化することで、リモートからビジネス ロジックやクラウド ロジックをデバイスに配置できるようにするというものです。 コンテナーをビルドするには、開発用マシンでコンテナー エンジンが必要になります。 

運用環境の IoT Edge デバイスでサポートされているコンテナー エンジンは、Moby のみです。 ただし、Docker などの Open Container Initiative と互換性のあるコンテナー エンジンでは、IoT Edge モジュール イメージをビルドできます。 

## <a name="development-tools"></a>開発ツール

Visual Studio と Visual Studio Code の両方に、IoT Edge ソリューションを開発するのに役立つアドオン拡張機能が備えられています。 これらの拡張機能は、新しい IoT Edge シナリオを作成したり、配置したりするのに役立つ言語固有のテンプレートを提供します。 Visual Studio と Visual Studio Code の両方の Azure IoT Edge 拡張機能で、IoT Edge ソリューションのコード作成、ビルド、配置、デバッグを行えます。 複数のモジュールを含む IoT Edge ソリューション全体を作成できます。拡張機能は新しいモジュールを追加するごとに、配置マニフェスト テンプレートを自動的に更新します。 また、この拡張機能を使用することで、Visual Studio または Visual Studio Code から IoT デバイスを管理することもできます。 デバイスにモジュールを配置したり、ステータスを監視したり、メッセージが IoT Hub に到着したらそのメッセージを表示したりします。 両方の拡張機能では、[IoT EdgeHub 開発ツール](#iot-edgehub-dev-tool)を使用して、開発用マシン上のモジュールのローカルでの実行とデバッグを行えます。 

他のエディターまたは CLI から開発する場合、Azure IoT Edge 開発ツールでは、コマンドラインから開発およびテストできるようにコマンドが用意されています。 新しい IoT Edge シナリオの作成、モジュール イメージのビルド、シミュレーターでのモジュールの実行、および IoT Hub に送信されるメッセージの監視を行えます。 

### <a name="visual-studio-code-extension"></a>Visual Studio Code 拡張機能

Visual Studio Code 用の Azure IoT Edge 拡張機能では、C、C#、Java、Node.js、Python などのプログラミング言語でビルドされた IoT Edge モジュールのテンプレートや C# での Azure 関数が提供されます。 

詳細情報およびダウンロード方法については、[Visual Studio Code 用 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) に関するページを参照してください。

IoT Edge 拡張機能に加えて、開発用のその他の拡張機能をインストールするのに役立つ情報もご覧いただけます。 たとえば、[Docker Support for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) を使用して、イメージ、コンテナー、レジストリを管理することができます。 さらに、サポートされている主要言語すべてで、モジュールを開発する際に役立つ Visual Studio Code の拡張機能が用意されています。 

#### <a name="prerequisites"></a>前提条件

一部の言語とサービス用のモジュール テンプレートでは、Visual Studio Code を使用する開発用マシンにプロジェクト フォルダーを作成するために必要な前提条件があります。

| モジュール テンプレート | 前提条件 |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](https://aka.ms/azure-jdks) <li> [JAVA_HOME 環境変数の設定](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.JS](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js モジュール ジェネレーター](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio 2017/2019 拡張機能

Azure IoT Edge Tools for Visual Studio では、C# および C で作成された IoT Edge モジュール テンプレートが提供されます。 

詳細情報およびダウンロードについては、[Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) または [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) に関する記事をご覧ください。

### <a name="iot-edge-dev-tool"></a>IoT Edge 開発ツール

Azure IoT Edge 開発ツールは、コマンドライン機能で IoT Edge 開発を簡略化します。 このツールは、モジュールの開発、デバッグ、テストを行うための CLI コマンドを提供します。 マシンに依存関係を手動でインストールした場合でも、また IoT Edge 開発コンテナーを使用している場合でも、IoT Edge 開発ツールは開発システムで機能します。 

詳細については、[IoT Edge 開発ツール wiki](https://github.com/Azure/iotedgedev/wiki) に関するページを参照してください。

## <a name="testing-tools"></a>テスト ツール

IoT Edge デバイスをシミュレートしたり、モジュールをより効率的にデバッグしたりするのに役立ついくつかのテスト ツールが存在します。 次の表に、ツール間の大まかな比較を示します。各セクションでは、各ツールの詳細を説明します。 

IoT Edge ランタイムは運用環境への配置のみをサポートしますが、以下のツールを使用すると、開発およびテスト目的で IoT Edge デバイスをシミュレートしたり、簡単に作成したりできます。 これらのツールは、相互に排他的ではなく、完全な開発エクスペリエンスを実現するために連携させることができます。 

| ツール | 別名 | サポートされるプラットフォーム | 最適な用途 |
| ---- | ------------- | ------------------- | --------- |
| IoT Edge Hub 開発ツール  | iotedgehubdev | Windows、Linux、MacOS | モジュールをデバッグするためのデバイスのシミュレート。 |
| IoT Edge 開発コンテナー | microsoft/iotedgedev | Windows、Linux、MacOS | 依存関係をインストールせずに開発。 |
| コンテナーの IoT Edge ランタイム | iotedgec | Windows、Linux、MacOS、ARM | ランタイムをサポートしないデバイスでのテスト。 |
| IoT Edge デバイス コンテナー | toolboc/azure-iot-edge-device-container | Windows、Linux、MacOS、ARM | 多数の IoT Edge デバイスが存在する規模の大きいシナリオのテスト。 |

### <a name="iot-edgehub-dev-tool"></a>IoT Edge Hub 開発ツール

Azure IoT EdgeHub 開発ツールは、ローカルでの開発およびデバッグ エクスペリエンスを提供します。 このツールにより、IoT Edge ランタイムなしに IoT Edge モジュールを起動することができ、IoT Edge モジュールとソリューションのローカルでの作成、開発、テスト、実行、デバッグを行えます。 テストするために、イメージをコンテナー レジストリにプッシュしたり、それらをデバイスに配置したりする必要はありません。

IoT Edge Hub 開発ツールは、IoT Edge 開発ツールだけでなく、Visual Studio および Visual Studio Code の拡張機能とも連携して動作するように設計されました。 内部ループ開発と外部ループ テストをサポートしているため、DevOps ツールとも統合されます。 

詳細情報とインストールについては、[Azure IoT EdgeHub 開発ツール](https://pypi.org/project/iotedgehubdev/)に関するページを参照してください。

### <a name="iot-edge-dev-container"></a>IoT Edge 開発コンテナー

Azure IoT Edge 開発コンテナーは、IoT Edge 開発に必要なすべての依存関係が存在する Docker コンテナーです。 このコンテナーにより、C#、Python、Node.js、Java など、開発にどの言語を使用するとしても簡単に開始できます。 インストールする必要があるのは、開発用マシンにコンテナーをプルするための Docker や Moby などのコンテナー エンジンだけです。 

詳細については、[Azure IoT Edge 開発コンテナー](https://hub.docker.com/r/microsoft/iotedgedev/)に関するページを参照してください。

### <a name="iot-edge-runtime-in-a-container"></a>コンテナーの IoT Edge ランタイム

コンテナーの IoT Edge ランタイムは、環境変数としてデバイス接続文字列を受け取る完全なランタイムを提供します。 このコンテナーによって、MacOS のようなランタイムをネイティブでサポートしていないシステムで IoT Edge モジュールとシナリオをテストすることができます。 配置するすべてのモジュールは、ランタイム コンテナーの外部で開始されます。 ランタイムと配置するモジュールが同じコンテナー内に存在するようにしたい場合は、IoT Edge デバイス コンテナーを検討してください。

詳細については、[コンテナーでの Azure IoT Edge の実行](https://github.com/Azure/iotedgedev/tree/master/docker/runtime)に関するページを参照してください。

### <a name="iot-edge-device-container"></a>IoT Edge デバイス コンテナー

IoT Edge デバイス コンテナーは、コンテナー エンジンを使用するすべてのマシンで起動できる、すべて揃った IoT Edge デバイスです。 デバイス コンテナーには、IoT Edge ランタイムとコンテナー エンジン自体が含まれています。 コンテナーの各インスタンスは、完全に機能する自己プロビジョニング IoT Edge デバイスです。 デバイス コンテナーは、モジュールへのネットワーク ルートがある限り、モジュールのリモート デバッグをサポートします。 デバイス コンテナーは、規模の大きいシナリオや Azure Pipelines をテストするため、多数の IoT Edge デバイスをすばやく作成するのに適しています。 Helm 経由での Kubernetes への配置もサポートしています。 

詳細については、[Azure IoT Edge デバイス コンテナー](https://github.com/toolboc/azure-iot-edge-device-container)に関するページを参照してください。

## <a name="devops-tools"></a>DevOps ツール

広範囲に及ぶ運用環境シナリオのために規模の大きいソリューションを開発する準備が整ったら、自動化、監視、合理化されたソフトウェア エンジニアリング プロセスなど、最新の DevOps 原則のメリットを生かすことができます。 IoT Edge には、Azure DevOps、Azure DevOps Projects、Jenkins などの DevOps ツールをサポートする拡張機能が備わっています。 既存のパイプラインをカスタマイズする、または CircleCI、TravisCI などの異なる DevOps ツールを使用する場合は、IoT Edge 開発ツールに含まれる CLI 機能を使用して行います。

詳細情報、ガイドライン、およびサンプルについては、以下のページを参照してください。
* [Azure IoT Edge に対する継続的インテグレーションと継続的配置](how-to-ci-cd.md)
* [Azure DevOps Projects を使用して IoT Edge 用の CI/CD パイプラインを作成する](how-to-devops-project.md)
* [Azure IoT Edge Jenkins プラグイン](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge DevOps GitHub のリポジトリ](https://github.com/toolboc/IoTEdge-DevOps)
