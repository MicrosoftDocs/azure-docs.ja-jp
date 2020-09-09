---
title: インクルード ファイル
description: インクルード ファイル
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "67181609"
---
## <a name="view-device-telemetry"></a>デバイス テレメトリを表示する

デバイスから送信されたテレメトリは、ソリューションの **[デバイス エクスプローラー]** ページで表示できます。

1. プロビジョニングしたデバイスを、 **[デバイス エクスプローラー]** ページのデバイスの一覧から選択します。 パネルには、デバイス テレメトリのプロットなど、デバイスに関する情報が表示されます。

    ![デバイスの詳細を確認する](media/iot-suite-visualize-connecting/devicesdetail.png)

1. **[気圧]** を選択して、テレメトリの表示を変更します。

    ![気圧テレメトリを表示する](media/iot-suite-visualize-connecting/devicespressure.png)

1. デバイスに関する診断情報を表示するには、下へスクロールして、 **[診断]** に移動します。

    ![デバイスの診断を表示する](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>デバイスを操作する

デバイスでメソッドを呼び出すには、リモート監視ソリューションの **[デバイス エクスプローラー]** ページを使用します。 たとえば、リモート監視ソリューションで、**Chiller** デバイスが **Reboot** メソッドを実装しています。

1. **[デバイス]** を選択して、ソリューションの **[デバイス エクスプローラー]** ページに移動します。

1. プロビジョニングしたデバイスを、 **[デバイス エクスプローラー]** ページのデバイスの一覧から選択します。

    ![実際のデバイスを選択する](media/iot-suite-visualize-connecting/devicesselect.png)

1. デバイスで呼び出すことができるメソッドの一覧を表示するには、 **[ジョブ]** を選択してから、 **[メソッド]** を選択します。 複数のデバイスで実行するジョブのスケジュールを設定するために、一覧から複数のデバイスを選択することができます。 **[ジョブ]** パネルには、選択したすべてのデバイスに共通のメソッドの型が表示されます。

1. **[再起動]** を選択し、ジョブ名を **RebootPhysicalChiller** に変更して、 **[適用]** を選択します。

    ![ファームウェア更新のスケジュール](media/iot-suite-visualize-connecting/deviceschedule.png)

1. シミュレートされたデバイスがメソッドを処理しているとき、デバイス コードを実行しているコンソールに一連のメッセージが表示されます。

> [!NOTE]
> ソリューションでジョブの状態を追跡するには、 **[View Job Status]\(ジョブ状態の表示\)** を選択します。

## <a name="next-steps"></a>次のステップ

[「リモート監視の構成済みソリューションのカスタマイズ](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md)」の記事では、ソリューション アクセラレータをカスタマイズする方法をいくつか説明します。