---
title: Azure Data Box Edge のコンピューティングの管理 | Microsoft Docs
description: Azure portal を使用して、Azure Data Box Edge のコンピューティング設定 (トリガー、モジュール) の管理、コンピューティング構成の表示、構成の削除などを行う方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: 58c4f42859f735a81a3e3edc801daff5d26194a0
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997650"
---
# <a name="manage-compute-on-your-azure-data-box-edge"></a>Azure Data Box Edge のコンピューティングの管理

この記事では、Azure Data Box Edge のコンピューティングを管理する方法について説明します。 コンピューティングの管理は、Azure portal またはローカル Web UI から行うことができます。 Azure portal を使用してモジュール、トリガー、およびコンピューティング構成を管理し、ローカル Web UI を使用してコンピューティング設定を管理します。

> [!IMPORTANT]
> Edge はプレビュー段階です。 このソリューションを注文して展開する前に、[Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。


この記事では、次のことについて説明します。

> [!div class="checklist"]
> * トリガーの管理
> * コンピューティング構成の管理


## <a name="manage-triggers"></a>トリガーの管理

イベントとは、クラウド環境内またはデバイス上で発生し、対応が必要になる可能性のある事象のことを指します。 たとえば、共有内でファイルが作成されると、それはイベントです。 トリガーは、イベントを発生させます。 Data Box Edge では、トリガーをファイル イベントやスケジュールへの応答にすることができます。

- **ファイル**:これらのトリガーは、ファイルの作成やファイルの変更などのファイル イベントへの応答です。
- **Scheduled**: これらのトリガーは、開始日、開始時刻、および繰り返し間隔を指定して定義できるスケジュールへの応答です。


### <a name="add-a-trigger"></a>トリガーの追加

トリガーを作成するには、Azure portal で次の手順を実行します。

1. Azure portal で Data Box Edge リソースに移動し、**[Edge コンピューティング] > [トリガー]** に移動します。 コマンド バーの **[+ トリガーの追加]** を選択します。

    ![[トリガーの追加] の選択](media/data-box-edge-manage-compute/add-trigger-1.png)

2. **[トリガーの追加]** ブレードで、トリガーの一意の名前を指定します。
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. トリガーの **[種類]** を選択します。 トリガーをファイル イベントへの応答にする場合は、**[ファイル]** を選択します。 定義した時間にトリガーを開始し、指定の間隔で繰り返し実行する場合は、**[Scheduled]\(スケジュール済み\)** を選択します。 選択内容に応じた一連のオプションが表示されます。

    - **[File trigger]\(ファイル トリガー\)** - ドロップダウン リストから、マウントされた共有を選択します。 この共有でファイル イベントが発生すると、トリガーによって Azure 関数が呼び出されます。

        ![SMB 共有を追加する](media/data-box-edge-manage-compute/add-file-trigger.png)

    - **[スケジュール済みのトリガー]** - 開始日時と、繰り返しの間隔を時間、分、または秒で指定します。 また、トピックの名前も入力します。 トピックにより、デバイスに展開されているモジュールにトリガーを柔軟にルーティングすることができます。

        ルートの文字列は、`"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"` のようになります。

        ![NFS 共有を追加する](media/data-box-edge-manage-compute/add-scheduled-trigger.png)

4. **[追加]** を選択し、トリガーを作成します。 トリガーの作成が進行中であることを示す通知が表示されます。 トリガーが作成されると、ブレードが更新されて、新しいトリガーが反映されます。
 
    ![更新されたトリガーの一覧](media/data-box-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>トリガーを削除する

トリガーを削除するには、Azure portal で次の手順を実行します。

1. トリガーの一覧から、削除するトリガーを選択します。

    ![トリガーの選択](media/data-box-edge-manage-compute/add-trigger-1.png)

2. 右クリックしてから、**[削除]** を選択します。

    ![[削除] の選択](media/data-box-edge-manage-compute/add-trigger-1.png)

3. 確認を求められたら、 **[はい]** をクリックします。

    ![削除の確定](media/data-box-edge-manage-compute/add-trigger-1.png)

トリガーの一覧が更新され、削除が反映されます。

## <a name="manage-compute-configuration"></a>コンピューティング構成の管理

Azure portal を使用して、Data Box Edge のコンピューティング構成の表示、既存のコンピューティング構成の削除、または IoT デバイスと IoT Edge デバイスのアクセス キーと同期するようコンピューティング構成の更新を行います。

### <a name="view-compute-configuration"></a>コンピューティング構成の表示

デバイスのコンピューティング構成を表示するには、Azure portal で次の手順を実行します。

1. Azure portal で Data Box Edge リソースに移動し、**[Edge コンピューティング] > [モジュール]** に移動します。 コマンド バーの **[View compute]\(コンピューティングの表示\)** を選択します。

    ![[View compute]\(コンピューティングの表示\) の選択](media/data-box-edge-manage-compute/view-compute-1.png)

2. デバイスのコンピューティング構成をメモします。 コンピューティングを構成したときに、IoT Hub リソースを作成しました。 その IoT Hub リソースの下に、IoT デバイスと IoT Edge デバイスが構成されています。 IoT Edge デバイスでの実行がサポートされているのは Linux モジュールのみです。

    ![構成の表示](media/data-box-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>コンピューティング構成の削除

デバイスの既存の Edge コンピューティング構成を削除するには、Azure portal で次の手順を実行します。

1. Azure portal で Data Box Edge リソースに移動し、**[Edge コンピューティング] > [Get started]\(開始\)** に移動します。 コマンド バーの **[Remove compute]\(コンピューティングの削除\)** を選択します。

    ![[Remove compute]\(コンピューティングの削除\) の選択](media/data-box-edge-manage-compute/remove-compute-1.png)

2. コンピューティング構成を削除した場合、コンピューティングをもう一度使用するときはデバイスを再構成する必要があります。 確認を求められたら、**[はい]** を選択します。

    ![[Remove compute]\(コンピューティングの削除\) の選択](media/data-box-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>IoT デバイスと IoT Edge デバイスのアクセス キーの同期

Data Box Edge でコンピューティングを構成すると、IoT デバイスと IoT Edge デバイスが作成されます。 これらのデバイスには、対称のアクセス キーが自動的に割り当てられます。 セキュリティのベスト プラクティスとして、これらのキーは IoT Hub サービスを通じて定期的にローテーションされます。

これらのキーをローテーションするには、作成した IoT Hub サービスに移動し、IoT デバイスまたは IoT Edge デバイスを選択します。 各デバイスに、プライマリ アクセス キーとセカンダリ アクセス キーがあります。 プライマリ アクセス キーをセカンダリ アクセス キーに割り当てて、プライマリ アクセス キーを再生成します。

IoT デバイスと IoT Edge デバイスのキーをローテーションしたら、Data Box Edge 上の構成を更新して最新のアクセス キーを取得する必要があります。 この同期により、デバイスで IoT デバイスと IoT Edge デバイスの最新のキーを取得することができます。 Data Box Edge では、プライマリ アクセス キーのみが使用されます。

デバイスのアクセス キーを同期するには、Azure portal で次の手順を実行します。

1. Azure portal で Data Box Edge リソースに移動し、**[Edge コンピューティング] > [Get started]\(開始\)** に移動します。 コマンド バーの **[構成の更新]** を選択します。

    ![[構成の更新] を選択](media/data-box-edge-manage-compute/refresh-configuration-1.png)

2. 確認を求められたら、**[はい]** を選択します。

     ![確認を求められたら [はい] を選択](media/data-box-edge-manage-compute/refresh-configuration-2.png)

3. 同期が完了したら、ダイアログを閉じます。

## <a name="enable-a-network-interface-for-compute"></a>コンピューティング用のネットワーク インターフェイスの有効化

Data Box Edge デバイス上で実行されているモジュールへのアクセスが必要な場合があります。 モジュールに外部からアクセスするには、デバイス上のネットワーク インターフェイスに IP アドレスを割り当てる必要があります。 ローカル Web UI から、これらのコンピューティング設定を管理できます。

コンピューティング設定を構成するには、ローカル Web UI で次の手順を実行します。

1. ローカル Web UI で、**[構成] > [コンピューティング設定]** の順に移動します。  

2. デバイス上のコンピューティング モジュールへの接続に使用するネットワーク インターフェイスを**有効**にします。 

    - 静的 IP アドレスを使用する場合は、ネットワーク インターフェイスの IP アドレスを入力します。
    - DHCP を使用している場合は、IP アドレスが自動的に割り当てられます。

3. **[適用]** を選択して、設定を適用します。

    ![コンピューティング設定を有効にする](media/data-box-edge-manage-compute/compute-settings-1.png)


## <a name="next-steps"></a>次の手順

- [Azure portal を使用してユーザーを管理する](data-box-edge-manage-users.md)方法について学習します。
