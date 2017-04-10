---
title: "Azure IoT Hub を使用したデバイス管理 | Microsoft Docs"
description: "Azure IoT Hub でのデバイス管理の概要: エンタープライズ デバイスのライフサイクルとデバイス管理パターン (再起動、出荷時の設定にリセット、ファームウェア更新、構成、デバイス ツイン、クエリ、ジョブなど)。"
services: iot-hub
documentationcenter: 
author: bzurcher
manager: timlt
editor: 
ms.assetid: a367e715-55f6-4593-bd68-7863cbf0eb81
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2017
ms.author: briz
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 352c42ddeef5537f9b912ddfe6a72b6b5342835b
ms.lasthandoff: 03/10/2017


---
# <a name="overview-of-device-management-with-iot-hub"></a>IoT Hub を使用したデバイス管理の概要
## <a name="introduction"></a>はじめに
Azure IoT Hub には、デバイスやバックエンドの開発者が堅牢なデバイス管理ソリューションを構築するために使用できる機能や拡張モデルが用意されています。 デバイスは、リソースの制約の大きいセンサーをはじめ、専用マイクロコントローラー、デバイス グループの通信をルーティングする強力なゲートウェイなど、多岐にわたります。  また、用途や IoT オペレーターの要件は業界によってかなり異なります。  このような違いがありながら、IoT Hub によるデバイス管理で提供される機能、パターン、およびコード ライブラリは、多様なデバイスとエンド ユーザーに対応することができます。

優れたエンタープライズ IoT ソリューションを構築するために不可欠な要素は、オペレーターがそのデバイス コレクションを継続的に管理するための手法について、戦略を定めることです。 IoT のオペレーターには、シンプルで信頼性が高く、かつ、より戦略的な観点からその仕事に取り組むことのできるツールとアプリケーションが必要です。 この記事では、次の内容について説明します。

* デバイス管理に対する Azure IoT Hub のアプローチの概要。
* 一般的なデバイス管理の原則の説明。
* デバイスのライフサイクルの説明。
* 一般的なデバイス管理パターンの概要。

## <a name="device-management-principles"></a>デバイス管理の原則
IoT のデバイス管理には特有の課題が伴います。このため、すべてのエンタープライズ クラスのソリューションが、以下の原則に対応する必要があります。

![デバイス管理の原則を示す図][img-dm_principles]

* **スケールとオートメーション**: IoT ソリューションには、日々の作業を自動化して比較的少人数の運用スタッフで何百万台というデバイスを管理できるシンプルなツールが必要です。 日常的な業務としてオペレーターは、多数のデバイスを遠隔から一括で操作し、直接の対応が必要な問題が発生した場合にだけ通知を受け取りたいと考えます。
* **開放性と互換性**: デバイスのエコシステムはきわめて多様です。 数多くのデバイス クラス、プラットフォーム、プロトコルに管理ツールを適合させる必要があります。 オペレーターは、制約が最も厳しいシングル プロセスの組み込みチップから、フル機能の高性能コンピューターまで、さまざまな種類のデバイスをサポートできなければなりません。
* **コンテキスト認識**: IoT の環境は動的で絶えず変化しています。 サービスの信頼性は最優先課題です。 メンテナンスのダウンタイムで重要な業務に支障が生じたり、危険な状況を招いたりすることを確実に避けるために、次を考慮に入れる必要があります。
    * SLA の保守ウィンドウ
    * ネットワークと電源の状態
    * 使用中のコンディション
    * デバイスの位置情報
* **さまざまなスタッフへの情報提供**: IoT 運用スタッフがそれぞれに抱えるワークフローやプロセスに対応することが不可欠となります。 運用スタッフは、社内 IT 部門に課せられた制約に適合しながら、  デバイス運用情報を、監督者やその他の管理職に継続的かつリアルタイムに伝える必要があります。

## <a name="device-lifecycle"></a>デバイスのライフサイクル
すべてのエンタープライズ IoT プロジェクトに共通する一般的なデバイス管理ステージ セットがあります。 Azure IoT では、デバイスのライフサイクルは&5; つのステージで構成されています。

![Azure IoT デバイス のライフサイクルの&5; つのフェーズ: 計画、プロビジョニング、構成、監視、使用中止][img-device_lifecycle]

この&5; つの各ステージにデバイス オペレーター要件がいくつかあり、完全なソリューションを提供するには、これを満たさなければなりません。

* **計画**: オペレーターは、デバイスのメタデータ体系を作成できます。これらのプロパティを使用することで、簡単かつ正確に一連のデバイスを照会して一括管理操作の対象にすることができます。 デバイス ツインを使用すると、このデバイスのメタデータをタグやプロパティの形式で保存できます。
  
    "*参考資料*": [デバイス ツインの使用][lnk-twins-getstarted]、[デバイス ツインについて][lnk-twins-devguide]、[デバイス ツインのプロパティの使用方法][lnk-twin-properties]。
* **プロビジョニング**: IoT Hub に対して新しいデバイスを安全にプロビジョニングし、デバイスの機能をオペレーターがすぐに検出できるようにします。  IoT Hub ID レジストリを使用して、柔軟性に優れたデバイス ID と資格情報を作成し、ジョブを使用してこの操作を一括で実行します。 デバイス ツインのデバイス プロパティを使ってその機能や状態を報告できるように、デバイスを構築します。
  
    "*参考資料*": [デバイス ID の管理][lnk-identity-registry]、[デバイス ID の一括管理][lnk-bulk-identity]、[デバイス ツインのプロパティの使用方法][lnk-twin-properties]。
* **構成**: 正常性とセキュリティを維持すると共に、デバイスに対する一括構成変更とファームウェア更新を効率化します。 こうしたデバイス管理操作は、Desired プロパティ、またはダイレクト メソッドとブロードキャスト ジョブを使用して一括で実行します。
  
    "*参考資料*": [ダイレクト メソッドの使用][lnk-c2d-methods]、[デバイスでダイレクト メソッドを呼び出す][lnk-methods-devguide]、[デバイス ツインのプロパティの使用方法][lnk-twin-properties]、[ジョブのスケジュールとブロードキャスト][lnk-jobs]、[複数デバイスでのジョブのスケジュール][lnk-jobs-devguide]。
* **監視**: デバイス コレクション全体の正常性や、進行中の操作の状態を監視して、対処が必要な問題があればオペレーターに通知します。  デバイス ツインを適用して、デバイスが稼働状況と更新操作の状態をリアルタイムで報告できるようにします。 デバイス ツインのクエリを使用して緊急を要する問題を報告するための強力なダッシュボード レポートを作成します。
  
    "*参考資料*": [デバイス ツインのプロパティの使用方法][lnk-twin-properties]、[デバイス ツインとジョブの IoT Hub クエリ言語][lnk-query-language]。
* **使用中止**: 故障後やアップグレード サイクルの終了後、またはサービス有効期間の経過後に、デバイスを交換またはデバイスの使用を停止します。  デバイス ツインを使用して、物理デバイスが交換されている場合は、デバイス情報を保持します。使用中止になっている場合はアーカイブされます。 IoT Hub ID レジストリを使用して、デバイス ID と資格情報を安全に失効します。
  
    "*参考資料*": [デバイス ツインのプロパティの使用方法][lnk-twin-properties]、[デバイス ID の管理][lnk-identity-registry]。

## <a name="device-management-patterns"></a>デバイス管理パターン
以下に示したのは、IoT Hub によって実現される一連のデバイス管理パターンです。  [デバイス管理のチュートリアル][lnk-get-started]では、このパターンを実際のシナリオに合わせて拡張する方法と、こうしたコア テンプレートに基づいて新しいパターンをデザインする方法について詳しく説明しています。

* **再起動** - バックエンド アプリが、再起動を開始したことを、ダイレクト メソッドを通じてデバイスに伝えます。  デバイスは、報告されるプロパティを使用して、その再起動状態を更新します。
  
    ![デバイス管理の再起動パターンを示す図][img-reboot_pattern]
* **出荷時の設定にリセット** - バックエンド アプリが、出荷時の設定へのリセットを開始したことを、ダイレクト メソッドを通じてデバイスに伝えます。  デバイスは、報告されるプロパティを使用して、そのデバイスの出荷時の設定へのリセット状態を更新します。
  
    ![デバイス管理の出荷時の設定へのリセット パターンを示す図][img-facreset_pattern]
* **構成** - バックエンド アプリが、必要なプロパティを使用して、デバイス上で実行されているソフトウェアを構成します。  デバイスは、報告されるプロパティを使用して、その構成状態を更新します。
  
    ![デバイス管理の構成パターンを示す図][img-config_pattern]
* **ファームウェア更新** - バックエンド アプリが、ファームウェア更新を開始したことを、ダイレクト メソッドを通じてデバイスに伝えます。  デバイスは、複数のステップから成るプロセスを開始してファームウェア イメージをダウンロードし、それを適用して、最後に IoT Hub サービスに再接続します。  この複数のステップから成るプロセスの間、デバイスは、報告されるプロパティを使用して、デバイスの進行状況と状態を更新します。
  
    ![デバイス管理のファームウェア更新パターンを示す図][img-fwupdate_pattern]
* **進行状況とステータスの報告** - ソリューション バックエンドは、一連のデバイス対してデバイス ツイン クエリを実行し、デバイス上で実行されている操作のステータスと進行状況を報告します。
  
    ![デバイス管理の進行状況とステータスの報告パターンを示す図][img-report_progress_pattern]

## <a name="next-steps"></a>次のステップ
IoT Hub でデバイス管理用に提供される機能、パターン、およびコード ライブラリを使用することで、デバイスのライフサイクル ステージごとのエンタープライズ IoT オペレーター要件を満たす IoT アプリケーションを作成できます。

IoT Hub のデバイス管理機能についての学習を続けるには、[デバイス管理の概要][lnk-get-started]に関するチュートリアルをご覧ください。

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-node-node-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md

