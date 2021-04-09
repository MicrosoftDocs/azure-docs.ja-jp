---
title: Azure Stack Edge Pro GPU コンピューティング管理 | Microsoft Docs
description: Azure portal を使用して、Azure Stack Edge Pro GPU のコンピューティング設定 (トリガー、モジュール) の管理、コンピューティング構成の表示、構成の削除などを行う方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 057f80049b13a265925f2d6f24a008d1e1e04c96
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102637240"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU でコンピューティングを管理する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイス上の IoT Edge サービスを介してコンピューティングを管理する方法について説明します。 コンピューティングの管理は、Azure portal またはローカル Web UI から行うことができます。 Azure portal を使用してモジュール、トリガー、IoT Edge 構成を管理し、ローカル Web UI を使用してコンピューティング ネットワーク設定を管理します。



## <a name="manage-triggers"></a>トリガーの管理

イベントとは、クラウド環境内またはデバイス上で発生し、対応が必要になる可能性のある事象のことを指します。 たとえば、共有内でファイルが作成されると、それはイベントです。 トリガーは、イベントを発生させます。 Azure Stack Edge Pro では、トリガーをファイル イベントやスケジュールへの応答にすることができます。

- **ファイル**:これらのトリガーは、ファイルの作成やファイルの変更などのファイル イベントへの応答です。
- **Scheduled**: これらのトリガーは、開始日、開始時刻、および繰り返し間隔を指定して定義できるスケジュールへの応答です。


### <a name="add-a-trigger"></a>トリガーの追加

トリガーを作成するには、Azure portal で次の手順を実行します。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[IoT Edge]** に移動します。 **[トリガー]** に移動し、コマンド バーで **[+ トリガーの追加]** を選択します。

    ![[トリガーの追加] の選択](media/azure-stack-edge-gpu-manage-compute/add-trigger-1-m.png)

2. **[トリガーの追加]** ブレードで、トリガーの一意の名前を指定します。
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. トリガーの **[種類]** を選択します。 トリガーをファイル イベントへの応答にする場合は、**[ファイル]** を選択します。 定義した時間にトリガーを開始し、指定の間隔で繰り返し実行する場合は、**[Scheduled]\(スケジュール済み\)** を選択します。 選択内容に応じた一連のオプションが表示されます。

    - **[File trigger]\(ファイル トリガー\)** - ドロップダウン リストから、マウントされた共有を選択します。 この共有でファイル イベントが発生すると、トリガーによって Azure 関数が呼び出されます。

        ![SMB 共有を追加する](media/azure-stack-edge-gpu-manage-compute/add-file-trigger.png)

    - **[スケジュール済みのトリガー]** - 開始日時と、繰り返しの間隔を時間、分、または秒で指定します。 また、トピックの名前も入力します。 トピックにより、デバイスに展開されているモジュールにトリガーを柔軟にルーティングすることができます。

        ルートの文字列は、`"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"` のようになります。

        ![NFS 共有を追加する](media/azure-stack-edge-gpu-manage-compute/add-scheduled-trigger.png)

4. **[追加]** を選択し、トリガーを作成します。 トリガーの作成が進行中であることを示す通知が表示されます。 トリガーが作成されると、ブレードが更新されて、新しいトリガーが反映されます。
 
    ![更新されたトリガーの一覧](media/azure-stack-edge-gpu-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>トリガーを削除する

トリガーを削除するには、Azure portal で次の手順を実行します。

1. トリガーの一覧から、削除するトリガーを選択します。

    ![トリガーの選択](media/azure-stack-edge-gpu-manage-compute/delete-trigger-1.png)

2. 右クリックして **[削除]** を選択します。

    ![[削除] の選択](media/azure-stack-edge-gpu-manage-compute/delete-trigger-2.png)

3. 確認を求められたら、 **[はい]** をクリックします。

    ![削除の確定](media/azure-stack-edge-gpu-manage-compute/delete-trigger-3.png)

トリガーの一覧が更新され、削除が反映されます。

## <a name="manage-iot-edge-configuration"></a>IoT Edge 構成の管理

Azure portal を使用して、Azure Stack Edge Pro のコンピューティング構成の表示、既存のコンピューティング構成の削除、または IoT デバイスと IoT Edge デバイスのアクセス キーと同期するようコンピューティング構成の更新を行います。

### <a name="view-iot-edge-configuration"></a>IoT Edge 構成の表示

デバイスの IoT Edge 構成を表示するには、Azure portal で次の手順を実行します。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[IoT Edge]** に移動します。 デバイスで IoT Edge サービスが有効になると、概要ページで IoT Edge サービスが正常に実行されていることが示されます。

    ![[View compute]\(コンピューティングの表示\) の選択](media/azure-stack-edge-gpu-manage-compute/view-compute-1.png)

2. **[プロパティ]** に移動して、デバイスの IoT Edge 構成を表示します。 コンピューティングを構成したときに、IoT Hub リソースを作成しました。 その IoT Hub リソースの下に、IoT デバイスと IoT Edge デバイスが構成されています。 IoT Edge デバイスでの実行がサポートされているのは Linux モジュールのみです。

    ![構成の表示](media/azure-stack-edge-gpu-manage-compute/view-compute-2.png)


### <a name="remove-iot-edge-service"></a>IoT Edge サービスの削除

デバイスの既存の IoT Edge 構成を削除するには、Azure portal で次の手順を実行します。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[IoT Edge]** に移動します。 **[概要]** に移動し、コマンド バーで **[削除]** を選択します。

    ![[Remove compute]\(コンピューティングの削除\) の選択](media/azure-stack-edge-gpu-manage-compute/remove-compute-1.png)

2. IoT Edge サービスを削除した場合、その操作は元に戻すことや取り消すことができません。 作成したモジュールとトリガーも削除されます。 IoT Edge を再使用する必要がある場合は、デバイスを再構成する必要があります。 確認を求められたら、 **[OK]** を選択します。

    ![[Remove compute]\(コンピューティングの削除\) の選択 2](media/azure-stack-edge-gpu-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>IoT デバイスと IoT Edge デバイスのアクセス キーの同期

Azure Stack Edge Pro でコンピューティングを構成すると、IoT デバイスと IoT Edge デバイスが作成されます。 これらのデバイスには、対称のアクセス キーが自動的に割り当てられます。 セキュリティのベスト プラクティスとして、これらのキーは IoT Hub サービスを通じて定期的にローテーションされます。

これらのキーをローテーションするには、作成した IoT Hub サービスに移動し、IoT デバイスまたは IoT Edge デバイスを選択します。 各デバイスに、プライマリ アクセス キーとセカンダリ アクセス キーがあります。 プライマリ アクセス キーをセカンダリ アクセス キーに割り当てて、プライマリ アクセス キーを再生成します。

IoT デバイスと IoT Edge デバイスのキーをローテーションしたら、Azure Stack Edge Pro 上の構成を更新して最新のアクセス キーを取得する必要があります。 この同期により、デバイスで IoT デバイスと IoT Edge デバイスの最新のキーを取得することができます。 Azure Stack Edge Pro では、プライマリ アクセス キーのみが使用されます。

デバイスのアクセス キーを同期するには、Azure portal で次の手順を実行します。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[IoT Edge コンピューティング]** に移動します。 **[概要]** に移動し、コマンド バーで **[構成の更新]** を選択します。

    ![[構成の更新] を選択](media/azure-stack-edge-gpu-manage-compute/refresh-configuration-1.png)

2. 確認を求められたら、 **[はい]** を選択します。

    ![確認を求められたら [はい] を選択](media/azure-stack-edge-gpu-manage-compute/refresh-configuration-2.png)

3. 同期が完了したら、ダイアログを閉じます。

## <a name="change-external-service-ips-for-containers"></a>コンテナーの外部サービス IP を変更する

Kubernetes 外部サービス IP は、Kubernetes クラスターの外部に公開されるサービスにアクセスするために使用されます。 デバイスがアクティブ化された後、ローカル UI にアクセスすることにより、デバイスのコンテナー化されたワークロード用の外部サービス IP を設定または変更できます。


1. デバイスのローカル UI で、 **[コンピューティング]** に移動します。
1. ネットワークがコンピューティング用に構成されているポートを選択します。 開かれたブレードで、Kubernetes 外部サービス IP を指定 (新規) または変更 (既存の場合) します。 これらの IP は、Kubernetes クラスターの外部に公開する必要があるすべてのサービスに使用されます。 
    - デバイス上で実行され、IoT Edge モジュールによって使用される `edgehub` サービス用に、少なくとも 1 つのサービス IP が必要です。 
    - デプロイする追加の IoT Edge モジュールまたはコンテナーごとに、IP が必要になります。 
    - これらは、静的な連続する IP です。

    ![Kubernetes サービス IP を変更する](media/azure-stack-edge-gpu-manage-compute/change-service-ips-1.png)

1. **[適用]** を選択します。 IP を適用した後で、デバイスを再起動する必要はありません。 新しい IP はすぐに有効になります。


## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro のトラブルシューティング](azure-stack-edge-gpu-troubleshoot.md)について学習します。
