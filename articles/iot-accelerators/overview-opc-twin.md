---
title: OPC Twin とは - Azure | Microsoft Docs
description: OPC Twin の概要
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: d58dc18d5513259d5c01f7ddcc54736796e5c824
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603623"
---
# <a name="what-is-opc-twin"></a>OPC Twin とは

OPC Twin は、Azure IoT Edge と IoT Hub を使用してクラウドと工場のネットワークとを接続するマイクロサービスから成ります。 OPC Twin は、REST API を通じて、産業用デバイスの検出、登録、リモート制御を実現します。 OPC Twin は、OPC Unified Architecture (OPC UA) SDK を必要とせず、プログラミング言語に依存しません。また、サーバーレス ワークフローに組み込むことができます。 この記事では、OPC Twin のいくつかのユース ケースを説明します。

## <a name="discovery-and-control"></a>検出と制御
OPC Twin を使用することで、検出と登録を簡素化できます。

### <a name="simple-discovery-and-registration"></a>シンプルな検出と登録
OPC Twin では、工場のオペレーターが工場のネットワークをスキャンできるので、OPC UA サーバーを検出したり登録したりすることができます。 別の方法として、工場のオペレーターは既知の検出 URL を使用して OPC UA デバイスを手動で登録することもできます。 たとえば、OPC Twin モジュールが備わった IoT Edge ゲートウェイを工場現場に設置した後、すべての OPC UA デバイスを接続したければ、工場のオペレーターがネットワーク スキャンをリモートでトリガーして、すべての OPC UA サーバーを目視で確認することができます。 

### <a name="simple-control"></a>シンプルな制御
OPC Twin を使用すれば、工場のオペレーターがイベントに対応し、臨機応変に手動または自動でクラウドからその工場現場の機械を再構成することができます。 OPC Twin には、OPC UA サーバー上のサービスの呼び出し、そのアドレス空間の参照のほか、変数の読み取り/書き込み、メソッドの実行を行うための REST API が用意されています。 たとえばボイラーでは、温度の KPI を使用して生産ラインを制御します。 OPC Publisher を使用して、温度センサーによってデータの変化が公開されます。 温度がしきい値に達すると、工場のオペレーターにアラートが届きます。 生産ラインの温度が OPC Twin を通じて自動的に下げられます。 温度が下がったことが、工場のオペレーターに通知されます。

## <a name="authentication"></a>Authentication
OPC Twin を使用すると、認証を簡素化できるほか、シンプルな開発者エクスペリエンスを実現できます。

### <a name="simple-authentication"></a>シンプルな認証 
OPC Twin には、Azure Active Directory (AAD) ベースの認証と監査がエンド ツー エンドで使用されています。 たとえば、オペレーターが機械に対してどのような操作を実施したかを調べるアプリケーションを OPC Twin 上に構築することができます。 機械側では、OPC UA の監査を通じて、それを実現できます。 クラウド側では、REST API で不変のクライアント監査ログを保管することと AAD 認証を通じて実現できます。

### <a name="simple-developer-experience"></a>シンプルな開発者エクスペリエンス 
OPC Twin は、REST API を介して、任意のプログラミング言語で記述されたアプリケーションと共に使用できます。 開発者が OPC UA クライアントをソリューションに統合するうえで、OPC UA SDK の知識は必要ありません。 OPC Twin はステートレスのサーバーレス アーキテクチャにシームレスに統合できます。 たとえば、アラームおよびイベント ダッシュボードのアプリケーションを開発するフル スタックの Web 開発者は、OPC Twin を使用して、イベントに反応するロジックを JavaScript または TypeScript で記述できます。C や C#、OPC UA のフル スタック実装の知識は必要ありません。 

## <a name="next-steps"></a>次の手順

ここでは OPC Twin とその用途について説明しました。次に以下の記事を読むことをお勧めします。

> [!div class="nextstepaction"]
> [OPC Vault とは](overview-opc-twin-architecture.md)