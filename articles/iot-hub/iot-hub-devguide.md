---
title: "Azure IoT Hub の開発者ガイド | Microsoft Docs"
description: "Azure IoT Hub 開発者ガイドには、エンドポイント、セキュリティ、ID レジストリ、デバイス管理、ダイレクト メソッド、デバイス ツイン、ファイルのアップロード、ジョブ、IoT Hub のクエリ言語、およびメッセージングの説明が含まれています。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: d534ff9d-2de5-4995-bb2d-84a02693cb2e
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1915044f252984f6d68498837e13c817242542cf
ms.openlocfilehash: 4f12a4b3ba9c1d0b7ed10cf5d766bcea60205d24


---
# <a name="azure-iot-hub-developer-guide"></a>Azure IoT Hub 開発者ガイド
Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、完全に管理されたサービスです。

Azure IoT Hub は、以下を提供します。

* デバイスごとのセキュリティ資格情報とアクセス制御を使用した、通信のセキュリティ保護。
* デバイスとクラウドの間における複数の超大規模通信オプション。
* デバイスごとの状態情報とメタデータのクエリ可能なストレージ。
* 最も一般的な言語とプラットフォームのデバイスのライブラリを使用した、簡単なデバイスの接続。

この IoT Hub 開発者ガイドには、次の記事が含まれています。

* [IoT Hub でのメッセージの送受信][devguide-messaging]: IoT Hub が公開している (デバイスとクラウドの双方向の) メッセージング機能について説明します。 この記事には、メッセージ ルーティング、メッセージ形式、サポートされる通信プロトコル、およびプロトコルで使用されるポート番号などのトピックに関する情報も含まれています。
* [Device-to-cloud communication guidance (デバイスからクラウドへの通信に関するガイダンス)][lnk-d2c-guidance]: デバイスからクラウドへのメッセージ、デバイス ツインの報告されるプロパティ、およびファイルのアップロードのいずれかを選択するのに役立ちます。
* [device-to-cloud 通信に関するガイダンス][lnk-c2d-guidance]: ダイレクト メソッド、デバイス ツインの必要なプロパティ、およびクラウドからデバイスへのメッセージのいずれかを選択するのに役立ちます。
* [デバイスからのファイルのアップロード][devguide-upload]: デバイスからファイルをアップロードする方法について説明します。 この記事には、アップロード プロセスで送信可能な通知などのトピックに関する情報も含まれています。
* [IoT Hub でのデバイス ID の管理][devguide-identities]: 各 IoT ハブの ID レジストリに保存される情報と、レジストリにアクセスして変更する方法について説明します。
* [IoT Hub へのアクセスの制御][devguide-security]: IoT Hub の機能へのアクセスを許可するために使用する、デバイスとクラウド コンポーネントの両方のセキュリティ モデルについて説明します。 この記事には、トークンと X.509 証明書の使用方法および付与できるアクセス許可の詳細情報が含まれています。
* [デバイス ツインを使用した状態と構成の同期化][devguide-device-twins]: "*デバイス ツイン*" の概念と、デバイス ツインを使用したデバイスの同期化などの機能について説明します。 この記事には、デバイス ツインに保存されるデータに関する情報が含まれています。
* [デバイスでのダイレクト メソッドの呼び出し][devguide-directmethods]: ダイレクト メソッドのライフサイクルと、バックエンド アプリからデバイスのメソッドを呼び出してデバイスでダイレクト メソッドを処理する方法について説明します。
* [Schedule jobs on multiple devices (複数デバイスで実行されるジョブのスケジュール設定)][devguide-jobs]: 複数のデバイスで実行するジョブのスケジュールを設定する方法について説明します。 この記事では、ダイレクト メソッドの実行やデバイス ツインを使用したデバイスの更新などのタスクを実行するジョブを送信する方法について説明します。 ジョブの状態を照会する方法についても説明します。
* [Reference - IoT Hub endpoints (リファレンス - IoT Hub エンドポイント)][devguide-endpoints]: 各 IoT ハブがランタイムと管理の操作のために公開する、さまざまなエンドポイントについて説明します。 また、この記事では、IoT ハブでの追加エンドポイントの作成方法、および標準的ではないシナリオでフィールド ゲートウェイを使用して IoT Hub エンドポイントへのデバイス接続を有効にする方法についても説明しています。
* [Reference - IoT Hub query language for device twins and jobs (リファレンス - デバイス ツインとジョブの IoT Hub クエリ言語)][devguide-query]: ハブからデバイス ツインとジョブに関する情報を取得できるようにする IoT Hub クエリ言語について説明します。
* [Reference - quotas and throttling (リファレンス - クォータとスロットル)][devguide-quotas]: IoT Hub サービスに設定されるクォータと、クォータを超えたときに発生する可能性があるスロットル動作の概要を示します。
* [リファレンス - 価格][devguide-pricing]: IoT Hub の各種 SKU に関する全般的な情報と価格の概要と、各種の IoT Hub 機能が IoT Hub によってどのようにメッセージとして測定されるかの詳細について説明します。
* [リファレンス - デバイス SDK とサービス SDK][devguide-sdks]: IoT Hub と対話するデバイスとサービス アプリの両方を開発する際に使用できる Azure IoT SDK の一覧を示します。 この記事には、オンライン API ドキュメントへのリンクが含まれています。
* [リファレンス - IoT Hub の MQTT のサポート][devguide-mqtt]: IoT Hub での MQTT プロトコルのサポート方法について詳しく説明します。 この記事では、Azure IoT SDK に組み込まれている MQTT プロトコルのサポートと、MQTT プロトコルを直接使用する方法について説明します。
* [用語集][devguide-glossary]: IoT Hub に関連する一般的な用語の一覧です。

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[devguide-pricing]: iot-hub-devguide-pricing.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md




<!--HONumber=Jan17_HO5-->


