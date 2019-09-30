---
title: 用語集 - IoT プラグ アンド プレイ プレビュー | Microsoft Docs
description: 概念 - IoT プラグ アンド プレイ プレビューに関連する一般用語の用語集。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/29/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 7b37912c58312644059f3990b4776514e081a4bb
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858832"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>IoT プラグ アンド プレイ プレビューの用語集

IoT プラグ アンド プレイに関する記事で使用される一般的な用語の定義です。

## <a name="azure-certified-for-iot-portal"></a>Azure Certified for IoT ポータル

[Azure Certified for IoT ポータル](https://aka.ms/ACFI) Web サイトを使用すると、次のことができます。

- [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)の[認定プロセス](#device-certification)を完了する。
- [デバイス機能モデル](#device-capability-model)を検索する。
- デバイス機能モデルを[パブリック モデル リポジトリ](#public-model-repository)に公開する。

## <a name="azure-cli"></a>Azure CLI

Azure CLI は、Azure リソースを管理するためのクロスプラットフォーム コマンドライン ツールです。 Azure CLI の Azure IoT 拡張機能は、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)との対話とテストに使用されるコマンドライン ツールです。 拡張機能は、次のことに使用できます。

- IoT プラグ アンド プレイ デバイスに接続する。
- デバイスが送信する[テレメトリ](#telemetry)を表示する。
- デバイスの[プロパティ](#properties)を操作する。
- デバイスの[コマンド](#commands)を呼び出す。
- [モデル リポジトリ](#model-repository)、[インターフェイス](#interface)、[デバイス機能モデル](#device-capability-model)を管理する。

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central は、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)の接続、監視、管理を簡単にするフル マネージド SaaS (サービスとしてのソフトウェア) ソリューションです。 [デバイス機能モデル](#device-capability-model)を使用すると、お使いのデバイスを監視および管理するよう自動的に IoT Central アプリケーションを構成できます。

## <a name="azure-iot-certification-service"></a>Azure IoT 認定サービス

Azure IoT 認定サービスにより、[Azure Certified for IoT ポータル](#azure-certified-for-iot-portal)を通じて認定を受けるために [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)を送信すると、一連の認定テストが実行されます。 [Certified for IoT デバイス カタログ](#certified-for-iot-device-catalog)にデバイスを追加するには、デバイスが認定されている必要があります。

## <a name="azure-iot-tools-extension"></a>Azure IoT Tools 拡張機能

Azure IoT Tools とは、[Visual Studio Code](#visual-studio-code) の拡張機能のコレクションで、IoT Hub との対話や IoT デバイスの開発に役立ちます。 IoT プラグ アンド プレイ デバイスの開発では、次のことに役立ちます。

- [デバイス機能モデル](#device-capability-model)と[インターフェイス](#interface)を作成する。
- [モデル リポジトリ](#model-repository)に公開する。
- デバイス アプリケーションを実装するためのスケルトン コードを生成する。

## <a name="azure-iot-explorer-tool"></a>Azure IoT エクスプローラー ツール

Azure IoT エクスプローラーとは、お使いの [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)と対話してテストするために使用できるグラフィカル ツールです。 このツールをローカル コンピューターにインストールすると、次のことに使用できます。

- お使いの [IoT ハブ](#azure-iot-hub)に接続されているデバイスを表示する。
- IoT プラグ アンド プレイ デバイスに接続する。
- デバイスが送信する[テレメトリ](#telemetry)を表示する。
- デバイスの[プロパティ](#properties)を操作する。
- デバイスの[コマンド](#commands)を呼び出す。

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub は、クラウド内でホストされているマネージド サービスであり、IoT アプリケーションとそれが管理するデバイスの間の双方向通信に対する中央メッセージ ハブとして機能します。 [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)は IoT ハブに接続できます。 An IoT ソリューションで IoT ハブを使用すると、次のことが可能になります。

- デバイスがクラウドベースのソリューションにテレメトリを送信する。
- クラウドベースのソリューションが、接続されているデバイスを管理する。

## <a name="azure-iot-device-sdk"></a>Azure IoT device SDK

IoT プラグ アンド プレイ デバイスのクライアント アプリケーションの構築に使用できる、多言語に対応したデバイス SDK が用意されています。 [デバイス認定](#device-certification)の要件の 1 つは、デバイス クライアント コードに Azure IoT device SDK のいずれかが使用されていることです。

## <a name="certified-for-iot-device-catalog"></a>Certified for IoT デバイス カタログ

[Certified for IoT デバイス カタログ](https://catalog.azureiotsolutions.com/)には、[デバイス認定](#device-certification)テストに合格した [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)が一覧表示されています。 カタログにある IoT プラグ アンド プレイ デバイスの[デバイス機能モデル](#device-capability-model)は、パブリック モデル リポジトリで公開されます。

## <a name="commands"></a>command

[インターフェイス](#interface)で定義されたコマンドは、[デジタル ツイン](#digital-twin)で実行できるメソッドを表します。 たとえば、デバイスを再起動するコマンドがあります。

## <a name="common-interface"></a>共通インターフェイス

すべての [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)には、いくつかの共通[インターフェイス](#interface)を実装することが想定されています。 たとえば、デバイス情報インターフェイスでは、デバイスのハードウェアとオペレーティング システムに関する情報を定義します。 [デバイス認定](#device-certification)では、お使いのデバイスに複数の共通インターフェイスが実装されていることが求められます。 共通インターフェイスの定義は、パブリック モデル リポジトリから取得できます。

## <a name="company-model-repository"></a>会社モデル リポジトリ

組織では、会社の[モデル リポジトリ](#model-repository)を[デバイス機能モデル](#device-capability-model)や[インターフェイス](#interface)のプライベート ストアとして使用できます。

## <a name="connection-string"></a>接続文字列

接続文字列は、エンドポイントに接続するために必要な情報をカプセル化します。 通常、接続文字列には、エンドポイントのアドレスとセキュリティ情報が含まれますが、接続文字列の形式はサービス間で異なります。 IoT Hub サービスに関連付けられた接続文字列には次の 2 種類があります。

- デバイス接続文字列を使用すると、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)は、IoT ハブ上のデバイス向けエンドポイントに接続できます。 デバイス上のクライアント コードでは、この接続文字列を使用して、IoT ハブとのセキュリティで保護された接続を確立します。
- IoT Hub 接続文字列を使用すると、バックエンドのソリューションやツールは IoT ハブ上のサービス向けエンドポイントに安全に接続できます。 これらのソリューションやツールは、その IoT ハブと、それに接続されているデバイスを管理します。

## <a name="device-capability-model"></a>デバイス機能モデル

デバイス機能モデルは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)を記述し、そのデバイスによって実装される[インターフェイス](#interface)のセットを定義します。 一般的に、デバイス機能モデルは物理デバイス、製品、または SKU に対応します。 デバイス機能モデルを定義するには、[デジタル ツイン定義言語](#digital-twin-definition-language)を使用します。

## <a name="device-certification"></a>デバイス認定

デバイス認定は、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)を認定するプロセスです。これにより、IoT プラグ アンド プレイ デバイスは [Certified for IoT デバイス カタログ](#certified-for-iot-device-catalog)に追加され、その[デバイス機能モデル](#device-capability-model)と[インターフェイス](#interface)は[パブリック モデル リポジトリ](#public-model-repository)に追加されます。

## <a name="device-developer"></a>デバイス開発者

デバイス開発者は、[デバイス機能モデル](#device-capability-model)、[インターフェイス](#interface)、[Azure IoT device SDK](#azure-iot-device-sdk) を使用して、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)で実行するコードを実装します。

## <a name="device-modeling"></a>デバイス モデリング

[デバイス開発者](#device-developer)は、[デジタル ツイン定義言語](#digital-twin-definition-language)を使用して、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)の機能をモデル化します。 このモデルは、モデル リポジトリを使用して共有できます。 デバイス開発者は、このモデルからスケルトン デバイス コードを生成できます。 [ソリューション開発者](#solution-developer)は、このモデルから IoT ソリューションを構成できます。

## <a name="device-provisioning-service"></a>Device Provisioning Service

[Azure IoT Central](#azure-iot-central) では、すべてのデバイスの登録と接続の管理に Device Provisioning Service が使用されます。 詳細については、[Azure IoT Central のデバイス接続機能](../iot-central/concepts-connectivity-pnp.md)に関するページを参照してください。 また、Device Provisioning Service を使用すると、お使いの IoT ハブベースの IoT ソリューションへのデバイスの登録と接続を管理することもできます。 詳細については、「[Azure IoT Hub Device Provisioning Service を使用したデバイスのプロビジョニング](../iot-dps/about-iot-dps.md)」を参照してください。

## <a name="device-registration"></a>デバイス登録

[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)から IoT ソリューションに接続できるようにするには、そのソリューションに登録しておく必要があります。 [Azure IoT Central](#azure-iot-central) では、デバイス登録の管理に [Device Provisioning Service](#device-provisioning-service) が使用されます。 カスタム IoT ソリューションでは、Azure portal またはプログラムを使用してデバイスを IoT ハブに登録できます。

## <a name="device-first"></a>デバイス優先

[Azure IoT Central](#azure-iot-central) では、デバイス優先の登録と接続のシナリオがサポートされています。 このシナリオでは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)が事前に登録していなくても IoT Central アプリケーションに接続できます。 登録は、デバイスから初めてアプリケーションに接続するときに自動的に行われます。

## <a name="digital-twin"></a>デジタル ツイン

デジタル ツインは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)のモデルです。 デジタル ツインは、[デジタル ツイン定義言語](#digital-twin-definition-language)を使用してモデル化されています。 [Azure IoT device SDK](#azure-iot-device-sdk) を使用すると、実行時にデジタル ツインと対話できます。 たとえば、デバイス上でデジタル ツインのプロパティ値を設定すると、この SDK によりこの変更がクラウド内の IoT ソリューションに通知されます。

## <a name="digital-twin-change-events"></a>デジタル ツインの変更イベント

[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)が [IoT ハブ](#azure-iot-hub)に接続されると、このハブはそのルーティング機能を使用して、デジタル ツインの変更に関する通知を送信できます。 たとえば、デバイス上で[プロパティ](#properties)値が変更されるたびに、IoT Hub は通知を Service Bus キューなどのエンドポイントに送信できます。

## <a name="digital-twin-definition-language"></a>デジタル ツイン定義言語

[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)用のモデルとインターフェイスを記述する言語です。 [デジタル ツイン](#digital-twin)の機能を記述したり、IoT プラットフォームと IoT ソリューションでエンティティのセマンティクスを活用できるようにしたりするには、[デジタル ツイン定義言語](https://aka.ms/DTDL)を使用します。

## <a name="digital-twin-route"></a>デジタル ツイン ルート

[デジタル ツインの変更イベント](#digital-twin-change-events)を Service Bus キューなどのエンドポイントに配信するために [IoT ハブ](#azure-iot-hub)で設定されたルート。

## <a name="interface"></a>インターフェイス

インターフェイスは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)または[デジタル ツイン](#digital-twin)によって実装される関連機能を記述します。 インターフェイスは、さまざまな[デバイス機能モデル](#device-capability-model)で再利用できます。

## <a name="iot-hub-query-language"></a>IoT Hub クエリ言語

IoT Hub クエリ言語は、複数の目的で使用されます。 たとえば、この言語を使用すると、お使いの IoT ハブに[登録されているデバイス](#device-registration)を検索したり、[デジタル ツインのルーティング](#digital-twin-route)の動作を調整したりできます。

## <a name="iot-plug-and-play-device"></a>IoT プラグ アンド プレイ デバイス

IoT プラグ アンド プレイ デバイスは、データの収集や他のデバイスの制御を行い、[デバイス機能モデル](#device-capability-model)を実装するソフトウェアまたはファームウェアを実行する、一般的には小規模なスタンドアロンのコンピューティング デバイスです。  環境監視デバイス (スマート農業灌漑システムのコントローラー) は、IoT プラグ アンド プレイ デバイスの一例です。 IoT プラグ アンド プレイ デバイスに対する命令と制御を行い、そのデバイスからのデータを受信する、クラウドでホストされる IoT ソリューションを作成できます。 [Azure Certified for IoT デバイス カタログ](#certified-for-iot-device-catalog)には、使用可能な IoT プラグ アンド プレイ デバイスが一覧表示されます。 カタログ内の各 IoT プラグ アンド プレイ デバイスは検証済みであり、[デバイス機能モデル](#device-capability-model)を備えています。

## <a name="microsoft-partner-center"></a>Microsoft パートナー センター

[Microsoft パートナー センター](https://docs.microsoft.com/partner-center/)とは、組織がその Microsoft とのエンドツーエンドの関係を管理する場所です。 [Azure Certified for IoT ポータル](#azure-certified-for-iot-portal)でお使いの [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)の認定を受けるには、Microsoft パートナー センターのアカウントが必要です。

## <a name="model-discovery"></a>モデル検出

[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)から IoT ソリューションに接続すると、そのソリューションは、[デバイス機能モデル](#device-capability-model)を検索することで、デバイスの機能を検出できます。 デバイスがその機能モデルをソリューションに送信することも、そのソリューションが[モデル リポジトリ](#model-repository)内でデバイス機能モデルを見つけることもできます。

## <a name="model-repository"></a>モデル リポジトリ

モデル リポジトリには、[デバイス機能モデル](#device-capability-model)と[インターフェイス](#interface)が格納されます。 [パブリック モデル リポジトリ](#public-model-repository)の数は 1 つです。 組織は、独自の組織モデル リポジトリを作成できます。

## <a name="model-repository-rest-api"></a>モデル リポジトリの REST API

モデル リポジトリの管理と対話のための API です。 たとえば、この API を使用すると、[デバイス機能モデル](#device-capability-model)を追加したり、機能モデルを検索したりできます。

## <a name="properties"></a>properties

プロパティは、デジタル ツインの何らかの状態を表す[インターフェイス](#interface)で定義されたデータ フィールドです。 プロパティは、読み取り専用または書き込み可能として宣言できます。 シリアル番号などの読み取り専用プロパティは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)自体で実行されるコードによって設定されます。  アラームのしきい値などの書き込み可能なプロパティは、通常、クラウドベースの IoT ソリューションから設定されます。

## <a name="public-model-repository"></a>パブリック モデル リポジトリ

[認定デバイス](#device-certification)の[デバイス機能モデル](#device-capability-model)と[インターフェイス](#interface)が格納されるパブリック モデル リポジトリは 1 つあります。 このパブリック モデル リポジトリには、[共通インターフェイス](#common-interface)の定義も格納されます。

## <a name="registration-id"></a>登録 ID

登録 ID は、[Device Provisioning Service](#device-provisioning-service) 内のデバイスを一意に識別します。 この ID は、[IoT ハブ](#azure-iot-hub)内のデバイスの一意識別子であるデバイス ID とは異なります。

## <a name="scope-id"></a>スコープ ID

スコープ ID は、[Device Provisioning Service](#device-provisioning-service) インスタンスを一意に識別します。

## <a name="shared-access-signature"></a>共有アクセス署名

Shared Access Signature は、SHA-256 セキュア ハッシュまたは URI に基づいた認証メカニズムです。 Shared Access Signature 認証には、共有アクセス ポリシーと Shared Access Signature (多くの場合トークンと呼ばれます) という 2 つのコンポーネントがあります。 [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)は、[IoT ハブ](#azure-iot-hub)による認証に Shared Access Signature を使用します。

## <a name="solution-developer"></a>ソリューション開発者

ソリューション開発者は、ソリューション バックエンドを作成します。 一般的に、ソリューション開発者は、[IoT Hub](#azure-iot-hub) や[モデル リポジトリ](#model-repository)などの Azure リソースを操作したり、[IoT Central](#azure-iot-central) を使用したりします。

## <a name="telemetry"></a>テレメトリ

[インターフェイス](#interface)で定義されているテレメトリ フィールドは、測定値を表します。 これらの測定値は、通常、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)からデータ ストリームとして送信される、センサーの読み取り値などの値です。

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code とは、複数のプラットフォームに対応した最新のコード エディターです。 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) パックなどに含まれている拡張機能を使用すると、幅広い開発シナリオに対応するためにエディターをカスタマイズできます。
