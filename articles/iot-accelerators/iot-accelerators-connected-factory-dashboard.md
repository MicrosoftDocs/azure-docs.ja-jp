---
title: 接続済みファクトリ ダッシュボードを使用する - Azure | Microsoft Docs
description: 接続済みファクトリ ダッシュボードの機能の使用方法について説明します。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 82045ddcc204fe369b7330fe9b3c2a8cadd56d88
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067926"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>接続済みファクトリ ソリューション アクセラレータ ダッシュボードの機能を使用する

[工業用 IoT デバイスを管理するクラウドベースのソリューションをデプロイする](quickstart-connected-factory-deploy.md)クイック スタートでは、ダッシュボードを操作してアラームに対応する方法を示しました。 このハウツー ガイドでは、工業用 IoT デバイスの監視と管理に使用できるその他のダッシュボード機能について説明します。

## <a name="apply-filters"></a>フィルターを適用する

ダッシュボードの **[ファクトリの場所]** パネルまたは **[アラーム]** パネルに表示される情報は、フィルター処理することができます。

1. [ファクトリの場所] パネルか [アラーム] パネルで**じょうご**アイコンをクリックすると、使用可能なフィルターの一覧が表示されます。

1. フィルター パネルが表示されます。

    [![接続済みファクトリ ソリューション アクセラレータのフィルター](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. 必要なフィルターを選択し、 **[適用]** をクリックします。 フィルター フィールドにフリー テキストを入力することもできます。

1. フィルターが適用されます。 追加のじょうごアイコンは、フィルターが適用されたことを示します。

    [![接続済みファクトリ ソリューション アクセラレータの適用されたフィルター](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > アクティブなフィルターは、一覧の内容をフィルター処理するだけで、表示されている OEE と KPI の値には影響しません。

1. フィルターをクリアするには、じょうごアイコンをクリックし、フィルター パネルで **[クリア]** をクリックします。

## <a name="browse-an-opc-ua-server"></a>OPC UA サーバーを参照する

ソリューション アクセラレータをデプロイすると、シミュレートされた一連の OPC UA サーバーが自動的にプロビジョニングされ、ダッシュボードから参照できるようになります。 シミュレートされたサーバーを使用することで、実際にサーバーをデプロイしなくても、ソリューション アクセラレータを簡単にテストできます。

1. ダッシュボードのナビゲーション バーにある**ブラウザー アイコン**をクリックします。

    [![接続済みファクトリ ソリューション アクセラレータのサーバー ブラウザー](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. ソリューション アクセラレータにデプロイされているサーバーを示すリストから、いずれかのサーバーを選択します。

    [![接続済みファクトリ ソリューション アクセラレータのサーバー リスト](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. **[接続]** をクリックすると、セキュリティ ダイアログが表示されます。 シミュレーションの場合は、そのまま **[続行]** をクリックして問題ありません。

1. サーバー ツリーのノードを展開するには、目的のノードをクリックします。 テレメトリを発行しているノードの横には、チェック マークが付いています。

    [![接続済みファクトリ ソリューション アクセラレータのサーバー ツリー](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. アイテムを右クリックすると、そのアイテムの読み取り、書き込み、発行、呼び出しを行うことができます。 使用できる操作は、付与されているアクセス許可やノードの属性によって異なります。 読み取りオプションを選択すると、特定のノードの値を示すコンテキスト パネルが表示されます。 書き込みオプションを選択すると、コンテキスト パネルが開き、新しい値を入力できます。 呼び出しオプションを選択すると、ノードが表示され、呼び出しに使用するパラメーターを入力できます。

## <a name="publish-a-node"></a>ノードを発行する

"*シミュレートされた OPC UA サーバー*" を参照しているときに、新しいノードを発行することもできます。 発行したノードからテレメトリを収集して、ソリューションで分析できます。 *シミュレートされた OPC UA サーバー*を使用することで、実際にデバイスをデプロイしなくても、ソリューション アクセラレータを簡単にテストできます。

1. OPC UA サーバー ブラウザー ツリーで、発行するノードを参照します。

1. ノードを右クリックします。 **[発行]** をクリックします。

    [![接続済みファクトリ ソリューション アクセラレータのノードの発行](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. コンテキスト パネルに、正常に発行されたことを示すメッセージが表示されます。 ノードはステーション レベル ビューに、横にチェック マークが付いた状態で表示されます。

    [![接続済みファクトリ ソリューション アクセラレータの発行の成功](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>コマンドと制御

接続済みファクトリでは、コマンドを使用して、クラウドから直接産業デバイスを制御できます。 この機能を使用して、デバイスで生成されたアラームに対処できます。 たとえば、圧力解放バルブを開くコマンドをデバイスに送信できます。 使用可能なコマンドは、OPC UA サーバー ブラウザー ツリーの **StationCommands** で確認できます。 このシナリオでは、ミュンヘンにある生産ラインの組立ステーションで圧力解放バルブを開きます。 コマンドと制御の機能を使用するには、ソリューション アクセラレータのデプロイに対する**管理者**ロールが必要です。

1. OPC UA サーバー ブラウザー ツリーで、ミュンヘン、生産ライン 0、アセンブリ ステーションの **StationCommands** ノードを参照します。

1. 使用するコマンドを選択します。 **OpenPressureReleaseValve** ノードを右クリックします。 **[呼び出し]** をクリックします。

    [![接続済みファクトリ ソリューション アクセラレータの呼び出しコマンド](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. コンテキスト パネルに、呼び出そうとしているメソッドとパラメーターの詳細が表示されます。 **[呼び出し]** をクリックします。

    [![接続済みファクトリ ソリューション アクセラレータの呼び出しのパラメーター](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. コンテキスト パネルが更新され、メソッド呼び出しが成功したことが表示されます。 呼び出しの結果として更新された圧力ノードの値を読み取ることで、呼び出しが成功したことを確認できます。

    [![接続済みファクトリ ソリューション アクセラレータの呼び出しの成功](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>バックグラウンド処理

ソリューション アクセラレータをデプロイすると、デプロイ プロセスにより、選択した Azure サブスクリプションに複数のリソースが作成されます。 これらのリソースを Azure [portal](https://portal.azure.com) で表示できます。 デプロイ プロセスにより、お使いのソリューション アクセラレータ用に選択した名前に基づいた名前で、 **リソース グループ** が作成されます。

[![接続済みファクトリ ソリューション アクセラレータのリソース グループ](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

各リソースの設定を表示するには、リソース グループ内のリソースのリストでそれを選択します。

ソリューション アクセラレータのソース コードは、[azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory) GitHub リポジトリで確認できます。

完了したら、ソリューション アクセラレータは、[azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) サイトで Azure サブスクリプションから削除できます。 このサイトでは、ソリューション アクセラレータを作成したときにプロビジョニングされていたすべてのリソースを簡単に削除することができます。

> [!NOTE]
> ソリューション アクセラレータに関連するすべての要素を確実に削除するには、[azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) サイトで削除してください。 ポータルでリソース グループを削除しないようにしてください。

## <a name="next-steps"></a>次の手順

これで、実際のソリューション アクセラレータをデプロイできました。引き続き IoT ソリューション アクセラレータの概要について学習するには、次の記事を参照してください。

* [コネクテッド ファクトリ ソリューション アクセラレータの構成](iot-accelerators-connected-factory-configure.md)
* [azureiotsolutions.com サイトでのアクセス許可](iot-accelerators-permissions.md)
