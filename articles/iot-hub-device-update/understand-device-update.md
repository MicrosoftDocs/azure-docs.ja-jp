---
title: Device Update for Azure IoT Hub の概要 | Microsoft Docs
description: Device Update for IoT Hub は、IoT デバイスの Over-the-Air (OTA) 更新をデプロイできるようにするサービスです。
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 60dfd448a66ca67a241f97570c91f683323a7d6d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232377"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>Device Update for IoT Hub (プレビュー) の概要

Device Update for IoT Hub は、IoT デバイスの Over-the-Air (OTA) 更新をデプロイできるようにするサービスです。

生産性と運用効率のさらなる向上に組織が目を向ける中、モノのインターネット (IoT) ソリューションの導入は続いており、その勢いも増しています。 そのため、これらのソリューションを構成するデバイスにとっては、信頼性とセキュリティを備えた基盤の上に構築され、大規模での接続と管理が容易であることが不可欠になっています。 Device Update for IoT Hub は、小さなセンサーからゲートウェイレベルのデバイスまで、あらゆるものを対象にした Over-the-Air 更新を公開、配布、管理するために顧客が使用できるエンドツーエンドのプラットフォームです。 

IoT に対応したデジタル トランスフォーメーションのベネフィットを最大限に実現するために、顧客には、デバイスを大規模で運用、保守、更新するためのこの機能が必要です。 Device Update for IoT Hub を実装することのベネフィットについて説明します。これには、独自の更新プラットフォームを構築するための追加の開発および保守コストをかけずに、セキュリティの脅威に迅速に対応できること、また事業目標を達成するための新しい機能をデプロイできることが含まれます。

## <a name="support-for-a-wide-range-of-iot-devices"></a>広範な IoT デバイスのサポート


Device Update for IoT Hub は、[Azure IoT Hub](https://azure.microsoft.com/en-us/services/iot-hub/) との統合を通じて、更新のデプロイを最適化し、運用を能率化するために設計されています。 この統合により、既存のどのソリューションでも Device Update を容易に導入できます。 これにより、ほとんどすべてのデバイスを接続するためのクラウドでホストされたソリューションが提供されます。 Device Update は、Linux や [Azure RTOS](https://azure.microsoft.com/en-us/services/rtos/) (リアルタイム オペレーティング システム) を含む広範な IoT オペレーティング システムをサポートしており、オープンソースを通じて拡張できます。 Device Update for IoT Hub オファリングを、STMicroelectronics、NXP、Renesas、Microchip などの半導体パートナーと共同で開発しています。 主要な半導体評価ボードの[サンプル](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU)をご覧ください。MCU クラスのデバイスへの無線 (OTA) での更新を構成、構築、デプロイする方法のファースト ステップ ガイドが含まれています。 

Device Update Agent Simulator バイナリと Raspberry Pi リファレンス Yocto イメージの両方が提供されます。
Device Update for IoT Hub では、Azure IoT Edge デバイスの更新もサポートしています。 Device Update Agent は、Ubuntu Server 18.04 amd64 プラットフォーム用に提供されます。 上記のどのプラットフォームも実行していない場合、Device Update for IoT Hub では、オープンソースのコードも提供しています。 実行中のディストリビューションにエージェントを移植することができます。

Device Update は IoT プラグ アンド プレイ (PnP) と連携し、必要な PnP インターフェイスをサポートする任意のデバイスを管理できます。 詳細については、[Device Update for IoT Hub と IoT プラグ アンド プレイ](device-update-plug-and-play.md)に関するページを参照してください。

## <a name="support-for-a-wide-range-of-update-artifacts"></a>広範な更新成果物のサポート

Device Update for IoT Hub では、イメージベースとパッケージベースの 2 つの形式の更新がサポートされています。

パッケージベースの更新は、デバイス上の特定のコンポーネントまたはアプリケーションのみを変更するターゲット指定の更新となります。 これにより、帯域幅の消費量が少なくなるほか、更新のダウンロードとインストールにかかる時間が短縮されます。 通常、パッケージ更新を使用すると、更新を適用する際のデバイスのダウンタイムを短縮し、イメージ作成のオーバーヘッドを回避できます。

イメージ更新を使用すると、デバイスの最終状態の信頼性が高くなります。 通常、運用前環境と運用環境の間でイメージ更新の結果をレプリケートする方が簡単です。そうすれば、パッケージやその依存関係の場合と同様の課題が生じることがないからです。
アトミックな性質のため、A/B フェールオーバー モデルを簡単に導入することもできます。

1 つの正解は存在せず、特定のユース ケースに応じて異なった選択をする可能性があります。 Device Update for IoT Hub では、イメージとパッケージの両方の形式の更新がサポートされているため、デバイス環境に適した更新モデルを選択できます。

## <a name="flexible-features-for-updating-devices"></a>デバイスを更新するための柔軟な機能

Device Update for IoT Hub の機能により、次のような強力で柔軟なエクスペリエンスが提供されます。

* Azure IoT Hub と統合された更新管理 UX
* デバイスのグループ化と更新スケジュール制御による段階的な更新のロールアウト
* 自動化とカスタム ポータル エクスペリエンスを可能にするためのプログラム API
* 更新のコンプライアンスとステータスを異種デバイス フリート全体にわたって一目で把握できるビュー
* 回復性があるデバイス更新 (A/B) のサポートによってシームレスなロールバックを実現
* Azure.com ポータルから利用可能なサブスクリプションおよびロールベースのアクセス制御
* オンプレミス コンテンツ キャッシュと Nested Edge のサポートによってクラウドから切断されたデバイスの更新を可能に
* 詳細な更新管理およびレポート ツール 

Device Update for IoT Hub の管理およびデプロイ制御によって、ユーザーは生産性を最大化し、貴重な時間を節約することができます。 Device Update for IoT Hub には、デバイスをグループ化したり、更新をデプロイするデバイスを指定したりする機能が含まれています。 ユーザーは、更新のデプロイのステータスを表示し、各デバイスで更新が正常に適用されていることを確認することもできます。

更新の失敗が発生した場合、ユーザーは Device Update for IoT Hub で、更新の適用に失敗したデバイスを識別することに加えて、関連する失敗の詳細を確認することもできます。 更新に失敗したデバイスを識別できることにより、発生源を手動で特定するのにかかる数え切れないほどの時間を節約できます。

### <a name="best-in-class-security-at-global-scale"></a>グローバル規模でのクラス最高のセキュリティ

Microsoft Azure では、10 億を超える IoT デバイスを世界中でサポートしており、その数は日ごとに急速に増加しています。 Device Update for IoT Hub は、この経験と、Windows Update プラットフォームによって実証された確かな信頼性に基づいて構築されているため、グローバル規模でデバイスをシームレスに更新できます。

Device Update for IoT Hub では、Microsoft Azure のために開発された、クラウドからエッジまでの包括的なセキュリティを使用しているため、顧客は、自力で最初からセキュリティを構築する方法を考案するのに時間を費やす必要はありません。


## <a name="device-update-workflows"></a>Device Update のワークフロー

Device Update の機能は、エージェント統合、インポート、管理の 3 つの領域に分けることができます。

### <a name="device-update-agent"></a>Device Update エージェント

デバイスで更新コマンドを受信すると、要求された更新フェーズ (ダウンロード、インストール、適用のいずれか) が実行されます。 各フェーズの間、IoT Hub を介して Device Update にステータスが返されます。これにより、デプロイの現在のステータスを確認できます。 進行中の更新がない場合、ステータスは "Idle" として返されます。 デプロイはいつでも取り消すことができます。

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="Device Update エージェントのワークフローの図。" lightbox="media/understand-device-update/client-agent-workflow.png":::

デバイス更新エージェントについて詳しくは、[こちら](device-update-agent-overview.md)をご覧ください。 

### <a name="importing"></a>インポート

インポートとは、更新を Device Update に取り込んで、デバイスに展開する仕組みです。 Device Update では、デバイスごとに 1 つの更新のロールアウトをサポートしています。 これは、OS パーティション全体を一度に更新するフルイメージ更新や、デバイス上で更新するすべてのパッケージを記述する apt マニフェストに最適です。 更新を Device Update にインポートするには、まず、更新を記述したインポート マニフェストを作成し、次に更新ファイルとインポート マニフェストをインターネットからアクセス可能な場所にアップロードします。 その後、Azure portal または [Device Update Import REST API](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) を使用して、更新インポートの非同期プロセスを開始できます。 Device Update によってファイルがアップロードされて処理され、IoT デバイスへの配布に使用できるようになります。

機密性の高いコンテンツの場合、Azure Blob Storage のアドホック SAS などの共有アクセス署名 (SAS) を使用してダウンロードを保護します。 [SAS の詳細](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="Device Update for IoT Hub のインポート ワークフローの図。" lightbox="media/understand-device-update/import-update.png":::

更新のインポートについて詳しくは、[こちら](import-concepts.md)をご覧ください。 

### <a name="grouping-and-deployment"></a>グループ化とデプロイ

更新をインポートした後、お使いのデバイスおよびデバイス クラス用の互換性のある更新を表示できます。

Device Update では、IoT Hub のタグを介して **グループ** の概念をサポートしています。 最初に更新をテスト グループにデプロイするのは、本番ロールアウト中の問題のリスクを軽減するための優れた方法です。

Device Update では、デプロイは、適切なコンテンツを、互換性のあるデバイスの特定のセットに紐付けする方法です。 Device Update は、各デバイスにコマンドを送信し、更新をダウンロードしてインストールするよう指示し、ステータスを取得するプロセスを調整します。

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="Device Update for IoT Hub のグループ化とデプロイのワークフローの図。" lightbox="media/understand-device-update/manage-deploy-updates.png":::

デプロイの概念について詳しくは、[こちら](device-update-compliance.md)をご覧ください

デバイス更新グループについて詳しくは、[こちら](device-update-groups.md)をご覧ください


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Device Update アカウントとインスタンスの作成](create-device-update-account.md)
