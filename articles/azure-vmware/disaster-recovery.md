---
title: 仮想マシンのディザスター リカバリーを完了する
description: この記事では、Azure VMware Solution を使用して仮想マシンのディザスター リカバリーを完了する方法について説明します
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 76a417b9ba00c4c0e6e958e5a04d19aecfe24563
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752276"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Azure VMware Solution を使用して仮想マシンのディザスター リカバリーを完了する

この記事では、Azure VMware Solution のプライベート クラウドを復旧またはターゲット サイトとして使用して、VMware Hybrid Cloud Extension (HCX) ソリューションで仮想マシンのディザスター リカバリーを完了するプロセスについて説明します。

VMware HCX には、レプリケーション ポリシーのきめ細かい制御および細分性を提供するさまざまな操作が用意されています。 使用可能な操作は次のとおりです。

- 反転 - 障害が発生した後。 反転により、サイト B はソース サイトに、サイト A は保護されている VM が現在稼働している場所になります。

- 一時停止 - 選択した仮想マシンに関連付けられている現在のレプリケーション ポリシーを一時停止します。

- 再開 - 選択した仮想マシンに関連付けられている現在のレプリケーション ポリシーを再開します。

- 削除 - 選択した仮想マシンに関連付けられている現在のレプリケーション ポリシーを削除します。

- Sync Now (今すぐ同期) - 保護されている VM にソース仮想マシンを境界を越えて同期します。

このガイドでは、次のレプリケーション シナリオを対象にしています。

- 1 つの VM または VM のグループを保護する。

- 1 つの VM または VM のグループのテスト復旧を完了する。

- 1 つの VM または VM のグループを復旧させる。

- 1 つの VM または VM のグループの保護を反転させる。

## <a name="protect-virtual-machines"></a>仮想マシンの保護

ソース サイトで **vSphere クライアント**にログインし、**HCX プラグイン**にアクセスします。

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="vSphere の HCX オプション" border="true":::

**[ディザスター リカバリー]** 領域に入り、 **[PROTECT VMS]\(VM の保護\)** をクリックします。

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="[PROTECT VMS]\(VM の保護\) の選択" border="true":::

開いたウィンドウで、ソースおよびリモートの各サイトを選択します。この場合のリモート サイトは、Azure VMware Solution のプライベート クラウドである必要があります。

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="VM の保護のウィンドウ" border="true":::

必要に応じて、既定のレプリケーション オプションを選択します。

- **Enable Compression (圧縮を有効にする):** 低スループットのシナリオにお勧めします。

- **Enable Quiescence (休止を有効にする):** 一貫性のあるコピーがリモート サイトに同期されるように、VM を一時停止します。

- **同期先ストレージ:** 保護されている VM のリモート データストアを選択します。 Azure VMware Solution のプライベート クラウドでは、この選択は vSAN データストアである必要があります。

- **コンピューティング コンテナー:** リモート vSphere クラスターまたはリソース プールです。

- **同期先フォルダー:** リモート同期先フォルダーです。この設定はオプションであり、フォルダーが選択されていない場合、 VM は選択したクラスターのすぐ下に配置されます。

- **RPO:** この値は、ソース仮想マシンと保護されている仮想マシン間の同期間隔であり、5 分から 24 時間までの任意の時間に設定できます。

- **スナップショットの間隔:** スナップショット間の間隔です。

- **スナップショットの数:** 構成されたスナップショット間隔内のスナップショットの合計数です。

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="仮想マシンの保護のオプション" border="true":::

一覧から 1 つ以上の仮想マシンを選択し、必要に応じて仮想マシンにレプリケーション オプションを構成します。

既定では、仮想マシンは、既定のレプリケーション オプションで構成されたグローバル設定ポリシーを継承します。 選択した VM の各ネットワーク インターフェイスに対して、リモート **ネットワーク ポート グループ**を構成し、 **[完了]** を選択して、保護プロセスを開始します。

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="ネットワーク インターフェイスのオプション" border="true":::

次の図に示すように、同じディザスター リカバリー領域で、選択した各仮想マシンのプロセスを監視できます。

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="保護の進行状況の監視" border="true":::

VM が保護されると、 **[Snapshots]\(スナップショット\)** タブにさまざまなスナップショットが表示されます。

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="スナップショットの一覧" border="true":::

黄色の三角形は、スナップショットと仮想マシンがテスト復旧操作でテストされていないことを意味します。

電源がオフになっている VM と電源が入っている VM には、重要な違いがあります。
上のスクリーンショットは、電源が入っている仮想マシンの同期プロセスを示しています。 同期プロセスが開始し、VM の完全コピーである最初のスナップショットが終了するまで続きました。その後は、構成された間隔で次のものが完了します。

電源がオフになっている VM の場合は、コピーが同期されてから、VM が非アクティブと表示され、保護操作が完了済みと表示されます。

仮想マシンの電源がオンになると、リモート サイトへの同期プロセスが開始されます。

## <a name="complete-a-test-recover-of-virtual-machines"></a>仮想マシンのテスト復旧を完了する

Azure VMware Solution のプライベート クラウドであるリモート サイトで **vSphere クライアント**にログインします。 **HCX プラグイン**内の [ディザスター リカバリー] 領域で、任意の VM の縦方向の省略記号を選択して、操作メニューを表示します。 **[Test Recover VM]\(VM のテスト復旧\)** を選択します。

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="[Test Recover VM]\(VM のテスト復旧\) の選択" border="true":::

新しいウィンドウで、テストのオプションを選択します。 仮想マシンのさまざまな状態をテストするために使用するスナップショットを選択します。

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="スナップショットを選択して [テスト] をクリックする" border="true":::

**[テスト]** をクリックすると、復旧操作が開始されます。

テスト復旧操作が完了すると、新しい VM を Azure VMware Solution プライベート クラウドの vCenter で確認できます。

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="復旧操作の確認" border="true":::

VM やその上で実行されているアプリケーションでテストを行った後、最後にテスト インスタンスを削除するためのクリーンアップを実行します。

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="テスト インスタンスのクリーンアップ" border="true":::

## <a name="recover-virtual-machines"></a>仮想マシンを復旧する

Azure VMware Solution のプライベート クラウドであるリモート サイトで **vSphere クライアント**にログインし、**HCX プラグイン**にアクセスします。

復旧シナリオでは、仮想マシンのグループがこの例に使用されます。

復旧する仮想マシンを一覧から選択し、 **[アクション]** メニューを開いて、 **[Recover VMs]\(VM を復旧する\)** を選択します。

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="仮想マシンの復旧" border="true":::

各インスタンスの復旧オプションを構成し、 **[Recover]\(復旧\)** をクリックして復旧操作を開始します。

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="仮想マシンの復旧の確認" border="true":::

復旧操作が完了すると、新しい VM がリモート vCenter Server インベントリに表示されます。

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>仮想マシンでレプリケーションの反転を完了する

Azure VMware Solution のプライベート クラウドで **vSphere クライアント**にログインし、**HCX プラグイン**にアクセスします。
レプリケーションの反転を開始する前に、ソース サイトの元の仮想マシンの電源をオフにしておく必要があります。 仮想マシンの電源がオフになっていない場合、この操作は失敗します。

ソース サイトにレプリケートする仮想マシンを一覧から選択し、 **[アクション]** メニューを開いて、 **[反転]** を選択します。 ポップアップ ウィンドウで **[反転]** をクリックして、レプリケーションを開始します。

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="保護操作の下の [反転] アクションの選択" border="true":::

レプリケーションは、各仮想マシンの詳細セクションで監視できます。

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="反転アクションの結果の確認" border="true":::

## <a name="disaster-recovery-plan-automation"></a>ディザスター リカバリー プランの自動化

現在、VMware HCX には、ディザスター リカバリー プランを作成して自動化するための組み込みメカニズムがありません。 この機能は HCX には存在しません。 ただし、ディザスター リカバリー操作用の API を含む REST API のセットが用意されています。

API 仕様は、URL の HCX Manager 内でアクセスできます。

これらの API は、ディザスター リカバリーの次の操作を対象としています。

- 保護

- 復旧

- テスト復旧

- 計画された復旧

- Reverse

- クエリ

- テストのクリーンアップ

- Pause

- Resume

- 保護の削除

- 再構成

JSON での復旧操作ペイロードの例を次に示します。

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

これらの API を使用すると、お客様はカスタム メカニズムを構築して、ディザスター リカバリー プランの作成と実行を自動化できます。
