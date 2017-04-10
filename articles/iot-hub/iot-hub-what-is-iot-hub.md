---
title: "Azure IoT Hub の概要 | Microsoft Docs"
description: "Azure IoT Hub サービスの概要: IoT Hub、デバイスの接続、モノのインターネット (IoT) の通信パターン、ゲートウェイ、サービス支援通信パターンの説明"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7adde91586f5fbbffd0aeaf0efb0810cc891ac0b
ms.openlocfilehash: ed204c466c5cfb60e5ba250b9dacb2524ca384eb
ms.lasthandoff: 03/02/2017


---
# <a name="overview-of-the-azure-iot-hub-service"></a>Azure IoT Hub サービスの概要
Azure IoT Hub へようこそ。 この記事では、Azure IoT Hub の概要と、モノのインターネット (IoT) ソリューションを実装したときに、このサービスを使用する理由について説明します。 Azure IoT Hub は、何百万もの IoT デバイスとソリューション バックエンド間で、セキュリティで保護された信頼性のある双方向通信を実現する、完全に管理されたサービスです。 Azure IoT Hub の特長は次のとおりです。

* 一方向メッセージング、ファイル転送、要求/応答メソッドなど、デバイスからクラウドとクラウドからデバイスの複数の通信オプションを提供します。
* 組み込みの宣言型メッセージ ルーティングを他の Azure サービスに提供します。
* デバイス メタデータと同期状態情報用にクエリ実行可能なストアを提供します。
* デバイスごとのセキュリティ キーまたは X.509 証明書を使用して、セキュリティ保護された通信とアクセス制御を実現します。
* デバイス接続イベントおよびデバイス ID 管理イベントの詳細な監視を実現します。
* 最も一般的な言語とプラットフォームのデバイスのライブラリが含まれます。

[IoT Hub と Event Hubs の比較][lnk-compare]に関する記事では、この&2; つのサービスの主な違いについて説明し、IoT ソリューションで IoT Hub を使用する利点を示しています。

Azure と IoT Hub で IoT ソリューションをセキュリティ保護する方法の詳細については、「[モノのインターネットの徹底的なセキュリティ][lnk-security-ground-up]」を参照してください。

![Azure IoT Hub as cloud gateway in internet of things solution][img-architecture]

> [!NOTE]
> IoT アーキテクチャの詳細については、「[Microsoft Azure IoT Reference Architecture (Microsoft Azure IoT リファレンス アーキテクチャ)][lnk-refarch]」を参照してください。
> 
> 

## <a name="iot-device-connectivity-challenges"></a>IoT デバイスの接続の課題
IoT Hub とデバイスのライブラリでは、デバイスをソリューション バック エンドに安定して安全に接続するための課題に対処できます。 IoT デバイスの特性は次のとおりです。

* 多くの場合、人間が操作することのない組み込みシステムです。
* 物理アクセスにコストがかかる遠隔地に配置されている場合があります。
* ソリューション バック エンド経由でしか到達できない場合があります。
* 電源や処理リソースが限られている場合があります。
* ネットワーク接続が断続的に切れたり、遅かったり、高コストである場合があります。
* 専用、カスタム、または業界固有のアプリケーション プロトコルを使用することが必要になる場合があります。
* 一般的なハードウェアおよびソフトウェア プラットフォームを多数使用して作成できます。

上記の要件に加え、IoT ソリューションはスケール、セキュリティ、および信頼性を提供する必要もあります。 そのため、一連の接続要件は、Web コンテナーやメッセージング ブローカーなどの従来のテクノロジを使用すると、実装するのが難しく、時間がかかります。

## <a name="why-use-azure-iot-hub"></a>Azure IoT Hub を使用する理由
Azure IoT Hub では、メッセージング、ファイル転送、要求/応答メソッドなど、[デバイスからクラウド][lnk-d2c-guidance]および[クラウドからデバイス][lnk-c2d-guidance]の豊富な通信オプションに加え、次の方法でデバイスの接続の課題に対処します。

* **デバイス ツイン**。 [デバイス ツイン][lnk-twins]を使用すると、デバイス メタデータと状態情報を保存、同期、照会できます。 デバイス ツインは、デバイスの状態に関する情報 (メタデータ、構成、状態) を格納する JSON ドキュメントです。 IoT Hub は、IoT Hub に接続する各デバイスにデバイス ツインを保持します。 
* **デバイスごとの認証およびセキュリティで保護された接続**。 各デバイスを独自の[セキュリティ キー][lnk-devguide-security]を使用してプロビジョニングし、IoT Hub に接続することができます。 [IoT Hub ID レジストリ][lnk-devguide-identityregistry]には、デバイスの ID とキーがソリューションに保存されます。 ソリューション バックエンドで個々のデバイスを許可リストと拒否リストのいずれかに追加し、デバイスのアクセスを完全に制御できます。
* **宣言型の規則に基づいた、デバイスからクラウドへのメッセージの Azure サービスへのルーティング**。 IoT Hub では、ルーティング規則に基づいたメッセージ ルートを定義し、ハブがデバイスからクラウドへのメッセージを送信する場所を制御できます。 ルーティング規則は、コードの記述を必要としません。また、取り込み後のカスタム メッセージ ディスパッチャーに代わることができます。
* **デバイスの接続操作の監視**。 デバイス ID の管理操作とデバイス接続イベントに関する詳細な操作ログを受信できます。 この監視機能により、IoT ソリューションでは、接続の問題 (不適切な資格情報で接続を試みるデバイス、メッセージを送信する頻度が異常であるデバイス、すべての C2D メッセージを拒否するデバイスなど) を識別することができます。
* **広範なデバイス ライブラリ**。 [Azure IoT device SDK][lnk-device-sdks] は、さまざまな言語とプラットフォーム (多くの Linux ディストリビューション、Windows、リアルタイム オペレーティング システムでは C) で使用でき、サポートされています。 Azure IoT デバイス SDK はまた、C#、Java、JavaScript など管理対象言語に対応しています。
* **IoT プロトコルと機能拡張**。 ソリューションでデバイス ライブラリを使用できない場合、IoT Hub は、デバイスで MQTT v3.1.1、HTTP 1.1、または AMQP 1.0 プロトコルをネイティブに使用できるようにするパブリック プロトコルを公開します。 また、以下の方法で IoT Hub を拡張して、カスタム プロトコルをサポートすることもできます。
  
  * IoT Hub で認識される&3; つのプロトコルのいずれかにカスタム プロトコルを変換するフィールド ゲートウェイを、[Azure IoT Gateway SDK][lnk-gateway-sdk] で作成します。 
  * クラウドで実行されるオープン ソース コンポーネントである [Azure IoT プロトコル ゲートウェイ][protocol-gateway]をカスタマイズします。
* **スケール**。 Azure IoT Hub は、デバイスの数百万単位での同時接続、および毎秒数百万単位のイベントに対応できます。

## <a name="gateways"></a>ゲートウェイ
通常、IoT ソリューションのゲートウェイは、クラウドにデプロイされる[プロトコル ゲートウェイ][lnk-gateway]か、デバイスでローカルにデプロイされる[フィールド ゲートウェイ][lnk-field-gateway]のいずれかになります。 プロトコル ゲートウェイは、MQTT から AMQP への変換など、プロトコル変換を実行します。 フィールド ゲートウェイは、現状分析を実行したり、時間の制約がある判断を行って遅延を短縮したりできます。また、デバイス管理サービスを提供したり、セキュリティとプライバシーの制約を強制したりできるほか、プロトコル変換を行うこともできます。 どちらの種類のゲートウェイも、デバイスと IoT Hub 間の仲介役として機能します。

フィールド ゲートウェイは通常、ソリューションにおけるアクセスと情報フローの管理において大きな役割を果たすため、単純なトラフィック ルーティング デバイス (ネットワーク アドレス変換デバイスやファイアウォールなど) とは異なります。

プロトコル ゲートウェイとフィールド ゲートウェイの両方をソリューションに含めることもできます。

## <a name="how-does-iot-hub-work"></a>IoT Hub のしくみ
Azure IoT Hub は、デバイスとソリューション バックエンドの間の対話を仲介する[サービス支援通信][lnk-service-assisted-pattern]パターンを実装しています。 サービス支援通信の目的は、管理システム (IoT Hub など) と、信頼されていない物理領域にデプロイされている特殊用途のデバイス間の信頼できる双方向の通信パスを確立することです。 このパターンでは次の原則が確立されています。

* セキュリティがその他のすべての機能に優先されます。
* デバイスは、要求されていないネットワーク情報を受け入れません。 デバイスは送信のみの方法で、すべての接続とルートを確立します。 ソリューション バックエンドからコマンドを受信する場合、デバイスは定期的に接続を開始して、処理待ちのコマンドがないか確認する必要があります。
* デバイスは、IoT Hub など、ピアリングされている既知のサービスへの接続またはルートの確立のみを行います。
* デバイスとサービス間またはデバイスとゲートウェイ間の通信パスは、アプリケーション プロトコル レイヤーで保護されます。
* システム レベルの承認と認証は、デバイスごとの ID に基づいており、 アクセス資格情報とアクセス許可をほぼ瞬時に取り消すことができます。
* コマンドとデバイスの通知は、デバイスが接続され、それらを受信するまで保持されるので、電源や接続の問題によって散発的に接続されるデバイスの双方向通信が円滑になります。 IoT Hub は、送信されるコマンドのデバイス固有のキューを維持しています。
* アプリケーション ペイロード データは個別に保護され、ゲートウェイを介して特定のサービスに転送されます。

モバイル業界では、サービス支援通信パターンを大規模に使用して、[Windows プッシュ通知サービス][lnk-wns]、[Google Cloud Messaging][lnk-google-messaging]、[Apple Push Notification Service][lnk-apple-push] などのプッシュ通知サービスを実装しています。

IoT Hub は、ExpressRoute のパブリック ピアリング パス経由でサポートされます。

## <a name="next-steps"></a>次のステップ
デバイスからメッセージを送信してそのメッセージを IoT Hub から受信する方法とメッセージ ルートを構成する方法については、「[IoT Hub でのメッセージの送受信][lnk-send-messages]」を参照してください。

IoT Hub では、標準ベースのデバイス管理が可能となっており、リモートからデバイスを管理、構成、更新することができます。詳細については、「[IoT Hub を使用したデバイス管理の概要][lnk-device-management]」を参照してください。

クライアント アプリケーションを各種デバイス ハードウェア プラットフォームやオペレーティング システムに実装するために、Azure IoT device SDK を使用できます。 device SDK には、IoT Hub へのテレメトリの送信とクラウドからデバイスへのメッセージの受信を容易にするライブラリが含まれています。 device SDK を使用すると、さまざまなネットワーク プロトコルのうちのいずれかを選択して IoT Hub と通信することができます。 詳細については、[device SDK に関する情報][lnk-device-sdks]を参照してください。

実際にコードを作成してサンプルを実行するには、[IoT Hub の使用][lnk-get-started]に関するチュートリアルを参照してください。

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "サービス支援通信に関する Clemens Vasters によるブログの投稿"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-send-messages]: iot-hub-devguide-messaging.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-security-ground-up]: iot-hub-security-ground-up.md

