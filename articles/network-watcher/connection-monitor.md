---
title: ネットワーク通信の監視 - チュートリアル - Azure Portal | Microsoft Docs
description: Azure Network Watcher の接続モニター機能によって 2 つの仮想マシン間のネットワーク通信を監視する方法を説明します。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 1d8a9cf10bf9b4aab02dd5033ecdd4fdc1f9423e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429249"
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>チュートリアル: Azure portal を使用して 2 つの仮想マシン間のネットワーク通信を監視する

仮想マシン (VM) と別の VM などのエンドポイント間の通信の成功は、組織にとってきわめて重要になることがあります。 場合によっては、通信を切断させる可能性がある構成の変更が導入されることがあります。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 2 つの VM を作成する
> * Network Watcher の接続モニター機能によって VM 間の通信を監視する
> * 接続モニターのメトリックに対するアラートを生成する
> * 2 つの VM 間の通信の問題を診断し、解決方法を学習する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-vms"></a>VM の作成

2 つの VM を作成します。

### <a name="create-the-first-vm"></a>最初の VM を作成する

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[Compute]** を選択し、オペレーティング システムを選択します。 このチュートリアルでは、**[Windows Server 2016 Datacenter]** を使用します。
3. 次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、**[OK]** を選択します。

    |Setting|値|
    |---|---|
    |Name|myVm1|
    |ユーザー名| 任意のユーザー名を入力します。|
    |パスワード| 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
    |サブスクリプション| サブスクリプションを選択します。|
    |リソース グループ| **[新規作成]** を選択し、「**myResourceGroup**と入力します。|
    |Location| **[米国東部]** を選択します。|

4. VM のサイズを選択して、**[選択]** を選択します。
5. **[設定]** で **[拡張機能]** を選択します。 次の図に示すように、**[拡張機能の追加]** を選択して、**[Network Watcher Agent for Windows]** を選択します。

    ![Network Watcher エージェント拡張機能](./media/connection-monitor/nw-agent-extension.png)

6. **[Network Watcher Agent for Windows]** の下の **[作成]** を選択し、**[拡張機能のインストール]** の下の **[OK]** を選択し、**[拡張機能]** の下の **[OK]** を選択します。
7. 残りの **[設定]** は既定値を受け入れて、**[OK]** を選択します。
8. **[概要]** の **[作成]** で **[作成]** を選択して、VM のデプロイを開始します。

### <a name="create-the-second-vm"></a>2 つ目の VM を作成する

[最初の VM を作成する](#create-the-first-vm)の手順を、次を変更して再度実行します。

|手順|Setting|値|
|---|---|---|
| 1 | いずれかのバージョンの **Ubuntu Server** を選択する |                                                                         |
| 3 | Name                                  | myVm2                                                                   |
| 3 | 認証の種類                   | SSH 公開キーを貼り付けるか、**[パスワード]** を選択して、パスワードを入力します。 |
| 3 | リソース グループ                        | **[既存のものを使用]** を選択し、**[myResourceGroup]** を選択します。                 |
| 6 | Extensions                            | **Linux 用ネットワーク エージェント**                                             |

VM のデプロイには数分かかります。 残りの手順を続行する前に、VM がデプロイを完了するまで待ちます。

## <a name="create-a-connection-monitor"></a>接続モニターを作成する

接続モニターを作成して、*myVm1* から *myVm2* への TCP ポート 22 経由の通信を監視します。

1. ポータルの左側にある **[その他のサービス]** を選びます。
2. **[フィルター]** ボックスに「*network watcher*」の入力を始めます。 検索結果に **[Network Watcher]** が表示されたら、それを選択します。
3. **[監視]** の **[接続モニター]** を選択します。
4. **[+ 追加]** を選択します。
5. 監視する接続の情報を入力するか選択して、**[追加]** を選択します。 次の図に示す例で、監視される接続は、ポート 22 経由の *myVm1* VM から *myVm2* VM への接続です。

    | Setting                  | 値               |
    | ---------                | ---------           |
    | Name                     | myVm1-myVm2(22)     |
    | ソース                   |                     |
    | 仮想マシン          | myVm1               |
    | 宛先              |                     |
    | 仮想マシンを選択する |                     |
    | 仮想マシン          | myVm2               |
    | ポート                     | 22                  |

    ![接続モニターを追加する](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>接続モニターを表示する

1. [接続モニターを作成する](#create-a-connection-monitor)の手順 1 ~ 3 を完了して、接続監視を表示します。 次の図に示すように、既存の接続モニターの一覧が表示されます。

    ![接続モニター](./media/connection-monitor/connection-monitors.png)

2. 前の図に示すように、**myVm1-myVm2(22)** という名前のモニターを選択して、次の図に示すように、モニターの詳細を表示します。

    ![モニターの詳細](./media/connection-monitor/vm-monitor.png)

    次の情報をメモしておきます。

    | Item                     | 値                      | 詳細                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Status                   | 到達可能                  | エンドポイントが到達可能かどうかを知ることができます。|
    | 平均 往復時間          | 接続を作成するまでの往復時間 (ミリ秒) を知ることができます。 接続モニターは、60 秒ごとに接続をプローブするため、時間経過に伴う待機時間を監視できます。                                         |
    | Hops                     | 接続モニターにより、2 つのエンドポイント間のホップを知ることができます。 この例では、接続は、同じ仮想ネットワーク内の 2 つの VM 間であるため、10.0.0.5 IP アドレスへのホップが 1 つだけあります。 既存のシステムまたはカスタム ルートで、VPN ゲートウェイまたはまたはネットワーク仮想アプライアンスなどを経由して VM 間のトラフィックをルーティングする場合、追加のホップが一覧表示されます。                                                                                                                         |
    | 状態                   | 各エンドポイントの緑のチェック マークにより、各エンドポイントが正常であることを確認できます。    ||

## <a name="generate-alerts"></a>アラートの作成

アラートは Azure Monitor のアラート ルールによって作成され、保存済みのクエリまたはカスタム ログ検索を一定の間隔で自動的に実行できます。 生成されたアラートで、第三者への通知や別のプロセスの呼び出しなど、1 つまたは複数のアクションを自動的に実行できます。 アラート ルールを設定する際、対象となるリソースによって、アラートの生成に使用できる一連のメトリックが決まります。

1. Azure portal で **Monitor** サービスを選択し、**[アラート]** > **[新しいアラート ルール]** の順に選択します。
2. **[ターゲットの選択]** をクリックして、対象となるリソースを選択します。 **[サブスクリプション]** を選択し、**リソースの種類**を設定して、使用したい接続モニターをフィルターで絞り込みます。

    ![ターゲットが選択された状態のアラート画面](./media/connection-monitor/set-alert-rule.png)
1. 対象となるリソースを選択したら、**[条件の追加]** を選択します。Network Watcher には、[アラートの作成に使用できるメトリック](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#metrics-and-dimensions-supported)があります。 **[使用可能なシグナル]** を ProbesFailedPercent と AverageRoundtripMs の各メトリックに設定します。

    ![シグナルが選択された状態のアラート ページ](./media/connection-monitor/set-alert-signals.png)
1. アラート ルール名、説明、重大度などのアラートの詳細を指定します。 アラートにアクション グループを追加して、アラートへの対応を自動化したりカスタマイズしたりすることもできます。

## <a name="view-a-problem"></a>問題を表示する

既定で、Azure は、同じ仮想ネットワーク内の VM 間のすべてのポートで通信を許可します。 時間が経過し、組織のだれかが Azure の既定の規則をオーバーライドし、誤って通信エラーを引き起こすことがあります。 次の手順を実行して、通信の問題を作成してから、再度接続モニターを表示します。

1. ポータル上部の [検索] ボックスに「*myResourceGroup*」と入力します。 検索結果に **myResourceGroup** リソース グループが表示されたら、それを選択します。
2. **myVm2-ns** ネットワーク セキュリティ グループを選択します。
3. 次の図に示すように、**[受信セキュリティ規則]** を選択し、**[追加]** を選択します。

    ![受信セキュリティ規則](./media/connection-monitor/inbound-security-rules.png)

4. 仮想ネットワーク内のすべての VM 間の通信を許可する既定の規則は、**AllowVnetInBound** という名前の規則です。 **AllowVnetInBound** 規則よりも高い優先順位 (小さい数値) で、ポート 22 経由の受信を拒否する規則を作成します。 次の情報を選択するか、入力し、それ以外の情報は既定値を受け入れて、**[作成]** を選択します。

    | Setting                 | 値          |
    | ---                     | ---            |
    | 宛先ポート範囲 | 22             |
    | Action                  | 拒否           |
    | 優先順位                | 100            |
    | Name                    | DenySshInbound |

5. 接続モニターは、60 秒間隔でプローブするため、数分待ってから、ポータルの左側の **[Network Watcher]**、**[接続モニター]** の順に選択し、**[myVm1-myVm2(22)]** モニターを再度選択します。 次の図に示すように、違う結果になります。

    ![モニター詳細のエラー](./media/connection-monitor/vm-monitor-fault.png)

    **myvm2529** ネットワーク インターフェイスの状態列に赤の感嘆符アイコンがあることが確認できます。

6. 状態が変更された理由については、前の図の 10.0.0.5 を選択します。 接続モニターによって、通信エラーの理由が通知されます: "*次のネットワーク セキュリティ グループの規則によってトラフィックがブロックされました: UserRule_DenySshInbound*"。

    手順 4. で作成したセキュリティの規則をだれかが実装したことを知らなかった場合、接続モニターからこの規則が通信の問題を引き起こしていることがわかります。 その後、規則を変更、オーバーライド、または削除して、VM 間の通信を復元できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループとそれに含まれるすべてのリソースが不要になったら、それらを削除します。

1. ポータル上部の **[検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「*myResourceGroup*」と入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、2 つの VM 間の接続を監視する方法について説明しました。 ネットワーク セキュリティ グループの規則によって、VM への通信が妨げられていたことがわかりました。 接続モニターが返すことができるすべてのさまざまな応答については、[応答の種類](network-watcher-connectivity-overview.md#response)に関するページを参照してください。 VM、完全修飾ドメイン名、URI (Uniform Resource Identifier)、または IP アドレス間の接続を監視することもできます。

何らかの時点で、仮想ネットワーク内のリソースが Azure 仮想ネットワーク ゲートウェイによって接続されている他のネットワーク内のリソースと通信できないことに気付く場合があります。 次のチュートリアルに進み、仮想ネットワーク ゲートウェイに関する問題を診断する方法について学習します。

> [!div class="nextstepaction"]
> [ネットワーク間の通信に関する問題を診断する](diagnose-communication-problem-between-networks.md)
