---
title: Azure IoT Edge for Linux on Windows とは | Microsoft Docs
description: Windows 10 デバイスで Linux IoT Edge モジュールを実行する方法の概要
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 330eaf5c12372347917e9f3a4aeafb6a2088c592
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492576"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Azure IoT Edge for Linux on Windows (プレビュー)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge for Linux on Windows を使用すると、Windows IoT デプロイで Windows アプリケーションと共に、コンテナー化された Linux ワークロードを実行できます。 エッジ デバイスで Windows IoT を活用している企業は、Linux で構築されるクラウドネイティブの分析ソリューションを利用できるようになりました。

IoT Edge for Linux on Windows は、Windows デバイスで Linux 仮想マシンを実行することで機能します。 Linux 仮想マシンは、IoT Edge ランタイムと共にプレインストールされています。 デバイスにデプロイされているすべての IoT Edge モジュールは、仮想マシン内で実行されます。 一方、Windows ホスト デバイスで実行されている Windows アプリケーションは、Linux 仮想マシンで実行されているモジュールと通信できます。

今すぐプレビューで[作業を開始してください](how-to-install-iot-edge-on-windows.md)。

>[!NOTE]
>IoT Edge に関する背景と目標に基づいて Azure IoT Edge for Linux on Windows を改善するために、[製品アンケート](https://aka.ms/AzEFLOW-Registration)にご協力ください。 また、このアンケートを使用して、今後の Azure IoT Edge for Linux on Windows のお知らせにサインアップすることもできます。

## <a name="components"></a>コンポーネント

IoT Edge for Linux on Windows では次のコンポーネントを使用することで、Linux と Windows のワークロードを相互に同時実行でき、また、これらがシームレスに通信できるようになります。

* **Azure IoT Edge を実行している Linux 仮想マシン**:Microsoft のファースト パーティ [Mariner](https://github.com/microsoft/CBL-Mariner) オペレーティング システムに基づく Linux 仮想マシンは、IoT Edge ランタイムを使用して構築され、IoT Edge ワークロード用の第 1 層対応環境として検証されています。

* **Windows Admin Center**:Windows Admin Center の IoT Edge 拡張機能を使用すると、Linux 仮想マシンでの IoT Edge のインストール、構成、診断を簡単に行うことができます。 Windows Admin Center では、ローカル デバイスに IoT Edge for Linux on Windows をデプロイしたり、ターゲット デバイスに接続してリモートで管理したりすることができます。

* **Microsoft Update**:Microsoft Update との統合により、Windows ランタイム コンポーネント、Mariner Linux VM、および IoT Edge を最新の状態に保つことができます。

![Windows と Linux VM は並行して実行され、Windows Admin Center によって両方のコンポーネントが制御されます。](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Windows プロセスと Linux 仮想マシンの間の双方向通信では、Windows プロセスにより、Linux コンテナーで実行されるワークロードのユーザー インターフェイスまたはハードウェア プロキシを提供できます。

## <a name="samples"></a>サンプル

IoT Edge for Linux on Windows では、Linux コンポーネントと Windows コンポーネントの間の相互運用性が重視されています。

Windows アプリケーションと IoT Edge モジュールの間の通信方法を示すサンプルについては、[Windows 10 IoT のサンプル](https://github.com/microsoft/Windows-IoT-Samples)を参照してください。

## <a name="public-preview"></a>パブリック プレビュー

IoT Edge for Linux on Windows は、現在、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)段階にあります。 インストールと管理のプロセスは、一般公開される機能とは異なることがあります。

## <a name="support"></a>サポート

IoT Edge サポートとフィードバック チャネルを使用して、IoT Edge for Linux on Windows についてサポートを受けることができます。

**バグ報告** - バグは、IoT Edge オープンソース プロジェクトの [問題ページ](https://github.com/azure/iotedge/issues)で報告できます。 Azure IoT Edge for Linux on Windows に関するバグは、 [iotedge-eflow の問題ページ](https://github.com/azure/iotedge-eflow/issues)で報告できます。

**Microsoft カスタマー サポート チーム** – [サポート プラン](https://azure.microsoft.com/support/plans/)に加入しているユーザーは、[Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) から直接サポート チケットを作成することで、Microsoft カスタマー サポート チームとやり取りをすることができます。

**機能の要望** – Azure IoT Edge 製品はその製品の [ユーザーの声のページ](https://feedback.azure.com/forums/907045-azure-iot-edge)を介して機能の要望を追跡します。

## <a name="next-steps"></a>次のステップ

詳細と操作中のサンプルについては、[IoT Edge for Linux on Windows 10 IoT Enterprise](https://aka.ms/EFLOWPPC9) に関する動画をご覧ください。

「[Azure IoT Edge for Linux on a Window デバイスのインストールとプロビジョニング](how-to-install-iot-edge-on-windows.md)」の手順に従って、IoT Edge for Linux on Windows でデバイスを設定します。
