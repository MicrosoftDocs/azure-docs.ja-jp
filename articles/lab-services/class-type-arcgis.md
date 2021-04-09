---
title: Azure Lab Services を使用して ArcMap および ArcGIS Desktop のラボを設定する | Microsoft Docs
description: ArcGIS を使用してクラスのラボを設定する方法について説明します。
author: nicolela
ms.topic: article
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: 530597a72b19afa1e80b5c7640b105d86479b1c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740105"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>ArcMap および ArcGIS Desktop のラボを設定する

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) は、地理情報システム (GIS) の一種です。  ArcGIS は、地図を作成および分析するため、および [Environmental Systems Research Institute](https://www.esri.com/en-us/home) (ESRI) が提供する地理データを操作するために使用されます。  ArcGIS Desktop にはいくつかのアプリケーションが含まれますが、この記事では、ArcMap を使用するためのラボの設定方法について説明します。  [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) は、2D マップの作成、編集、分析に使用されます。

## <a name="lab-configuration"></a>ラボの構成

ArcMap を使用するためのラボの設定を開始するには、Azure サブスクリプションとラボ アカウントが必要です。  Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。  新しいラボ アカウントを作成する方法の詳細については、[ラボ アカウントの設定](tutorial-setup-lab-account.md)に関する記事を参照してください。  既存のラボ アカウントを使用することもできます。

### <a name="lab-account-settings"></a>ラボ アカウントの設定

次の表の説明に従って、ラボ アカウントの設定を有効にします。  Azure Marketplace イメージを有効にする方法の詳細については、「[ラボ作成者が利用できる Azure Marketplace イメージを指定する](./specify-marketplace-images.md)」を参照してください。

| ラボ アカウントの設定 | Instructions |
| ------------------- | ------------ |
|Marketplace イメージ| お使いのラボ アカウント内で使用する Windows 10 Pro または Windows 10 Pro N イメージを有効にします。|

### <a name="licensing-server"></a>ライセンス サーバー

ArcGIS Desktop が提供するライセンスの一種として[同時使用ライセンス](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm)があります。  これを使用するには、ArcGIS License Manager をライセンス サーバーにインストールする必要があります。  ライセンス マネージャーは、同時に実行できるソフトウェアのコピーの数を追跡します。  サーバーでライセンス マネージャーを設定する方法について詳しくは、[ライセンス マネージャー ガイド](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm)に関する記事を参照してください。

通常、このライセンス サーバーは、オンプレミスのネットワークに配置されるか、Azure 仮想ネットワーク内の Azure 仮想マシンでホストされます。  ライセンス サーバーを設定した後は、[ラボ アカウント](./tutorial-setup-lab-account.md)に[仮想ネットワークをピアリング](./how-to-connect-peer-virtual-network.md)する必要があります。  ラボの VM とライセンス サーバーの間で通信できるように、ラボを作成する "前" に、ネットワーク ピアリングを実行する必要があります。

詳細については、[共有リソースとしてのライセンス サーバーの設定](how-to-create-a-lab-with-shared-resource.md)に関する記事を参照してください。

### <a name="lab-settings"></a>ラボの設定

ArcGIS Desktop で使用することをお勧めする仮想マシン (VM) のサイズは、学生が使用するアプリケーション、拡張機能、特定のバージョンによって異なります。  また、VM のサイズは、学生が実行する予定のワークロードによっても異なります。  VM サイズの特定についてヘルプが必要な場合は、[ArcGIS Desktop のシステム要件](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm)に関するページを参照してください。  考えられる VM サイズを特定した後、適切なパフォーマンスを実現するために、学生のワークロードをテストすることをお勧めします。

この記事では、他の ArcGIS Desktop 拡張機能が使用されていないという前提で、バージョン [10.7.1 の ArcMap](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm) に対して [**中** の VM サイズ](administrator-guide.md#vm-sizing)を使用することをお勧めします。  ただし、クラスのニーズによっては、**大**、もしくは **小規模または中規模 GPU (視覚化)** の VM サイズが必要になる場合があります。  たとえば、ArcGIS Desktop に含まれる [Spatial Analyst 拡張機能](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm)は、パフォーマンスを向上させるために GPU をサポートしていますが、GPU の使用は必須ではありません。

| ラボの設定 | 値と説明 |
| ------------ | ------------------ |
|仮想マシンのサイズ| **中間**。  リレーショナル データベース、メモリ内キャッシュ、分析に最も適しています。|  

### <a name="template-machine"></a>テンプレート マシン

このセクションの手順では、テンプレート VM を設定する方法を示します。

1.  テンプレート VM を起動し、RDP を使用してそのマシンに接続します。

2.  ESRI からの手順を使用し、ArcGIS Desktop コンポーネントをダウンロードしてインストールします。  これらの手順には、同時使用ライセンス用のライセンス マネージャーの割り当てが含まれます。 
    - [ArcGIS Desktop のインストールと構成の概要](https://desktop.arcgis.com/en/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  学生用の外部バックアップ ストレージを設定します。  学生が行ったすべての変更がセッション間で保存されるため、学生は割り当てられた VM にファイルを直接保存できます。  ただし、いくつかの理由から、学生が VM から外部のストレージに自分の作業をバックアップすることをお勧めします。
    - クラスとラボが終了した後、学生が自分の作業にアクセスできるようにするため。  
    - 学生の VM が正常な状態ではなくなり、そのイメージを[リセット](how-to-set-virtual-machine-passwords.md#reset-vms)する必要がある場合に備えるため。

    ArcGIS では、各学生は各作業セッションの終了時に次のファイルをバックアップする必要があります。

    - プロジェクトのレイアウト情報が保存される mxd ファイル。
    - ArcGIS によって生成されたすべてのデータが保存されるファイル ジオデータベース。
    - ラスター ファイル、シェープファイル、GeoTIFF など、学生が使用する可能性があるその他のデータ。

    バックアップ ストレージには OneDrive を使用することをお勧めします。  テンプレート VM で OneDrive を設定するには、記事「[OneDrive をインストールして構成する](how-to-prepare-windows-template.md#install-and-configure-onedrive)」の手順に従います。 

4.  最後に、テンプレート VM を[発行](how-to-create-manage-template.md#publish-the-template-vm)して、学生の VM を作成します。

### <a name="auto-shutdown-and-disconnect-settings"></a>自動シャットダウンと切断の設定

ラボの[自動シャットダウンと切断の設定](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)により、学生の VM が使用されていないときにシャットダウンされるようにすることができます。  作業中に VM がシャットダウンされないようにするために、これらの設定は、学生が実行するワークロードの種類に応じて設定する必要があります。  たとえば、 **[仮想マシンがアイドル状態のときにユーザーを切断する]** 設定では、マウスやキーボードの入力が指定した時間検出されなかった場合に、RDP セッションから学生が切断されます。  この設定では、長時間のクエリを実行する場合や、レンダリングを待機する場合など、学生がマウスやキーボードを積極的に使用しないワークロードのために十分な時間を確保する必要があります。

ArcGIS では、これらの設定に対して次の値をお勧めします。
- 仮想マシンがアイドル状態のときにユーザーを切断する
    - アイドル状態が検出されてから 30 分後
- ユーザーが切断したときに仮想マシンをシャットダウンする
    - ユーザーが切断してから 15 分後

## <a name="cost"></a>コスト

このクラスの考えられるコスト見積もりを検討しましょう。 この見積もりには、ライセンス サーバーの実行に伴うコストは含まれません。 クラスの生徒数は 25 人とします。 予定された授業時間は 20 時間です。 また、各学生には、予定された授業時間の他に、宿題や課題を行うための時間として 10 時間が割り当てられます。 ここで選択した仮想マシンのサイズは、42 ラボ ユニットの **中** です。

25 人の学生 \* (予定された 20 時間 + 割り当てられた 10 時間) \* 42 ラボ ユニット * 0.01 USD/時間 = 315.00 USD

>[!IMPORTANT]
> コストの見積もりはあくまでも例です。  価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。  

## <a name="next-steps"></a>次のステップ

次の手順は、すべてのラボの設定で共通です。

- [テンプレートの作成と管理](how-to-create-manage-template.md)
- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)