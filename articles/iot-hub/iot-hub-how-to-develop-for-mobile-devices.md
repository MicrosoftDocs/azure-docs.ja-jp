---
title: Azure IoT SDK を利用したモバイル デバイス向け開発 | Microsoft Docs
description: ディベロッパー ガイド - Azure IoT Hub SDK を利用してモバイル デバイス向け開発を行う方法を学びます｡
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 5256a58a2b68584888abcac915392d8e389e9772
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054539"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Azure IoT SDK を利用したモバイル デバイス向け開発

モノのインターネット (IoT) のモノは､センサーやマイクロコントローラ､スマート デバイス､インダストリアル ゲートウェイ､モバイル デバイスまで､それぞれに異なる機能を持つ広範囲のデバイスを意味します｡  モバイル デバイスは IoT デバイスになります｡device-to-cloud テレメトリを送信し､クラウドによって管理されます｡  バックエンド サービス アプリケーションを実行するデバイスにもなります｡この場合は､その他の IoT デバイスを管理します｡  どちらの場合も、[Azure IoT Hub SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) を利用して、モバイル デバイスで機能するアプリケーションを開発できます。  

## <a name="develop-for-native-ios-platform"></a>ネイティブ iOS プラットフォーム向け開発

Azure IoT Hub SDK は､Azure IoT Hub C SDK を介してネイティブ iOS プラットフォーム サポートを提供します｡  これは iOS SDK と考えることができ､Swift や Objective C Xcode プロジェクトに組み込むことができます｡  iOS 上で C SDK を利用する方法は 2 通りあります｡

*  XCode プロジェクトで CocoaPod ライブラリを直接に利用する｡  
* C SDK 用のソース コードをダウンロードし、MacOS 向け [build instruction](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) に従って iOS プラットフォーム向けにビルドする。  

さまざまなプラットフォームに移植しやすいよう､Azure IoT Hub C SDK は C99 で作成されています｡  移植プロセスでは、プラットフォーム固有のコンポーネント用の thin adoption layer の作成が必要です。[iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx) 用はこちらにあります。  iOS プラットフォーム上では､サポートされている Azure IoT Hub プリミティブや､SDK固有の機能であるネットワークの信頼性のための再試行ポリシーをはじめとする C SDK の機能を利用することができます｡  また iOS SDK 用のインターフェイスは､Azure IoT Hub C SDK 用のインターフェイスに似ています｡  

iOS デバイス上でデバイス アプリケーションやサービス アプリケーションを開発する手順については､以下の文書で順を追って説明しています｡

* [クイック スタート:デバイスから IoT ハブへの利用統計情報の送信](quickstart-send-telemetry-ios.md)  
* [IoT ハブを利用したクラウドからデバイスへのメッセージの送信](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Azure IoT Hub の CocoaPod ライブラリを利用した開発

Azure IoT Hub SDK では､iOS 開発用の一群の Objective-C CocoaPod ライブラリを公開しています｡  最新の CocoaPod ライブラリの一覧は、[CocoaPods for Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md) で見ることができます。  該当するライブラリを XCode プロジェクトに組み込んでから､IoT Hub 関連のコードを作成する方法は 2 通りあります｡

* Objective C 関数:プロジェクトを Objective-C で作成している場合は､Azure IoT Hub C SDK から直接に API を呼び出すことができます。  プロジェクトを Swift で作成している場合は､関数を作成する前に `@objc func` を呼び出し､C または Objective-C コードを使って Azure IoT Hub に関係するすべてのロジックの作成に進むことができます｡  両方のケースの一連のデモ サンプルが[サンプル リポジトリ](https://github.com/Azure-Samples/azure-iot-samples-ios)にあります。  

* C サンプルの組み込み:C デバイス アプリケーションを作成している場合は､XCode プロジェクトから直接にサンプルを参照できます。
    * XCode から XCode プロジェクトに sample.c ファイルを追加します｡  
    * 依存関係にヘッダー ファイルを追加します｡  ヘッダー ファイルは、[サンプル レポジトリ](https://github.com/Azure-Samples/azure-iot-samples-ios)にサンプルが含まれています。 詳細については､[Objective-C](https://developer.apple.com/documentation/objectivec) に関する Apple のドキュメント ページをご覧ください｡

## <a name="develop-for-android-platform"></a>Android プラットフォーム向け開発
Azure IoT Hub Java SDK では、Android プラットフォームをサポートします。  テスト済みの具体的な API バージョンについては、[プラットフォーム サポート ページ](iot-hub-device-sdk-platform-support.md)にアクセスして最新の更新プログラムを確認してください。

Gradle および Android Studio を使用して Android デバイス上でデバイス アプリケーションやサービス アプリケーションを開発する手順については､以下の文書で順を追って説明しています。

* [クイック スタート:デバイスから IoT ハブへの利用統計情報の送信](quickstart-send-telemetry-android.md)  
* [クイック スタート:IoT ハブに接続されたデバイスを制御する](quickstart-control-device-android.md) 

## <a name="next-steps"></a>次の手順

* [IoT Hub REST API リファレンス](https://docs.microsoft.com/rest/api/iothub/)
* [Azure IoT C SDK ソース コード](https://github.com/Azure/azure-iot-sdk-c)
