---
title: Azure で StorSimple デバイス マネージャー サービスをデプロイする | Microsoft Docs
description: サービスの作成、削除、および移行と、サービス登録キーの管理のために必要な手順について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 66244bd8e24ff62be41df72f7a39c0ce0ed13135
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93360701"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>StorSimple 8000 シリーズ デバイスの StorSimple デバイス マネージャー サービスをデプロイする

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>概要

StorSimple デバイス マネージャー サービスは Microsoft Azure で実行され、複数の StorSimple デバイスに接続します。 サービスの作成後は、StorSimple デバイス マネージャー サービスに接続されているすべてのデバイスを、このサービスを使用して 1 か所から集中的に管理できるため、管理上の負担を最小限にできます。

このチュートリアルでは、サービスの作成、削除、および移行と、サービス登録キーの管理のために必要な手順について説明します。 この記事に記載されている内容は、StorSimple 8000 デバイスにのみ適用されます。 StorSimple Virtual Array の詳細については、「[StorSimple Virtual Array の StorSimple デバイス マネージャー サービスをデプロイする](storsimple-virtual-array-manage-service.md)」を参照してください。

> [!NOTE]
> -  Azure portal では、Update 5.0 以降を実行しているデバイスがサポートされます。 デバイスが最新の状態でない場合は、すぐに Update 5 をインストールしてください。 詳細については、「[Update 5 のインストール](storsimple-8000-install-update-5.md)」をご覧ください。 
> - StorSimple Cloud Appliance (8010/8020) を使っている場合は、クラウド アプライアンスを更新できません。 最新バージョンのソフトウェアを使って、Update 5.0 で新しいクラウド アプライアンスを作成してから、作成した新しいクラウド アプライアンスにフェールオーバーします。 
> - Update 4.0 以前を実行しているすべてのデバイスでは、管理機能が制限されます。 

## <a name="create-a-service"></a>サービスの作成
StorSimple デバイス マネージャー サービスを作成するには、以下が必要です。

* エンタープライズ契約によるサブスクリプション
* アクティブな Microsoft Azure ストレージ アカウント
* アクセス管理で使用する課金情報

使用できるのはエンタープライズ契約によるサブスクリプションのみです。 サービスの作成時に既定のストレージ アカウントを生成することもできます。

単一のサービスで複数のデバイスを管理できます。 ただし、1 つのデバイスを複数のサービスに割り当てることはできません。 大企業は、そのようなサービス インスタンスを複数持つことで、さまざまなサブスクリプション、組織、デプロイの場所に対応することができます。 

> [!NOTE]
> StorSimple 8000 シリーズ デバイスと StorSimple Virtual Array の管理には、StorSimple デバイス マネージャー サービスの個別のインスタンスが必要です。

サービスを作成するには、次の手順を実行します。

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


それぞれの StorSimple デバイス マネージャー サービスに対して、以下の属性が存在します。

* **[名前]** – StorSimple Manager デバイス マネージャー サービスの作成時に割り当てられた名前。 **サービス名は、サービスの作成後に変更することはできません。これは、Azure Portal で名前を変更できない他のエンティティ (デバイス、ボリューム、ボリューム コンテナー、バックアップ ポリシーなど) も同様です。**
* **[状態]** – サービスの状態。**アクティブ**、**作成中**、または **オンライン** のいずれか。
* **[場所]** – StorSimple デバイスがデプロイされる地理的な場所。
* **[サブスクリプション]** – サービスに関連付けられている課金サブスクリプション。

## <a name="delete-a-service"></a>サービスの削除

サービスを削除する際は、接続されているデバイスでそのサービスが使用されていないことを先に確認します。 サービスが使用されている場合は、該当するデバイスを非アクティブにします。 非アクティブ化操作によってデバイスとサービス間の接続は切断されますが、クラウド内のデバイスのデータは保持されます。

> [!IMPORTANT]
> いったんサービスを削除すると、その操作を元に戻すことはできません。 このサービスを使用していたデバイスは、別のサービスで使用する前に工場出荷時の既定値にリセットする必要があります。 このとき、デバイス上のローカル データや構成は失われます。

サービスを削除するには次の手順を実行します。

### <a name="to-delete-a-service"></a>サービスを削除するには

1. 削除するサービスを検索します。 **[リソース]** アイコンをクリックし、検索する適切な用語を入力します。 検索結果で、削除するサービスをクリックします。

    ![削除するサービスを検索する](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. この操作で、StorSimple デバイス マネージャー サービスのブレードに移動します。 **[削除]** をクリックします。

    ![サービスの削除](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. 確認通知が表示されたら、 **はい** をクリックします。 サービスが削除されるまで数分かかる場合があります。

    ![削除の確定](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>サービス登録キーを取得する

正常にサービスを作成した後、そのサービスに StorSimple デバイスを登録します。 初めて StorSimple デバイスを登録する場合は、サービス登録キーが必要です。 既存の StorSimple サービスに追加のデバイスを登録するには、登録キーとサービス データ暗号化キー (最初のデバイスの登録中に生成される) の両方が必要です。 サービス データ暗号化キーの詳細については、「 [StorSimple セキュリティ](storsimple-8000-security.md)」をご覧ください。 StorSimple デバイス マネージャーのブレードで **[キー]** にアクセスすることで、登録キーを取得できます。

次の手順を実行して、サービス登録キーを取得します。

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

サービス登録キーは、安全な場所に保管してください。 このキーは、サービスに追加のデバイスを登録するときにサービス データ暗号化キーと共に必要です。 サービス登録キーを取得したら、StorSimple 用 Windows PowerShell インターフェイスを介してデバイスを構成する必要があります。

この登録キーを使用する方法については、「[手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)」を参照してください。

## <a name="regenerate-the-service-registration-key"></a>サービス登録キーを再生成する
キー ローテーションを実行する必要があるとき、またはサービス管理者の一覧が変更されたときは、サービス登録キーを再生成する必要があります。 キーを再生成した場合、新しいキーはその後のデバイスの登録に対してのみ使用されます。 既に登録されているデバイスは、再生成の影響を受けません

次の手順を実行して、サービス登録キーを再生成します。

### <a name="to-regenerate-the-service-registration-key"></a>サービス登録キーを再生成するには
1. **[StorSimple デバイス マネージャー]** ブレードで、 **[管理]&gt;** **[キー]** の順に移動します。
    
    ![[キー] ブレードに移動する](./media/storsimple-8000-manage-service/regenregkey2.png)

2. **[キー]** ブレードで、 **[再生成]** をクリックします。

    ![[再生成] をクリック](./media/storsimple-8000-manage-service/regenregkey3.png)
3. **[サービス登録キーの再生成]** ブレードで、キーを再生成するときに必要な操作を確認します。 これ以降にこのサービスに登録されるデバイスはすべて、新しい登録キーを使用します。 **[再生成]** をクリックして確定します。 登録が完了すると、その旨が通知されます。

    ![再生成を確認する](./media/storsimple-8000-manage-service/regenregkey4.png)

4. 新しいサービス登録キーが表示されます。

5. このキーをコピーし、このサービスに新しいデバイスを登録するときのために保存します。



## <a name="change-the-service-data-encryption-key"></a>サービス データ暗号化キーの変更
サービス データ暗号化キーを使用して、StorSimple Manager サービスから StorSimple デバイスに送信される、顧客の機密データ (ストレージ アカウントの資格情報など) を暗号化します。 IT 組織にストレージ デバイスに関するキー ローテーション ポリシーがある場合は、これらのキーを定期的に変更する必要があります。 キーの変更プロセスは、StorSimple Manager サービスが 1 つのデバイスを管理しているか、または複数のデバイスを管理しているかによって多少異なります。 詳細については、「[StorSimple のセキュリティとデータの保護](storsimple-8000-security.md)」を参照してください。

サービス データ暗号化キーの変更は、次の 3 つの手順からなるプロセスです。

1. Azure Resource Manager 用の Windows PowerShell スクリプトを使用して、サービス データ暗号化キーを変更するデバイスを承認します。
2. StorSimple 用 Windows PowerShell を使用して、サービス データ暗号化キーの変更を開始します。
3. StorSimple デバイスを複数使用している場合は、他のすべてのデバイスでサービス データ暗号化キーを更新します。

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>手順 1:Windows PowerShell スクリプトを使用してサービス データ暗号化キーを変更するデバイスを承認する
通常、デバイスの管理者は、サービス データ暗号化キーを変更する場合、デバイスの承認をサービス管理者に依頼します。 その後、サービス管理者は、キーの変更をデバイスに承認します。

この手順は、Azure Resource Manager ベースのスクリプトを使用して実行します。 サービス管理者は、承認するのに適したデバイスを選択できます。 選択したデバイスは、サービス データ暗号化キー変更プロセスを開始できるようになります。 

スクリプトの使用の詳細については、「[Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)」を参照してください

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>サービス データ暗号化キーの変更を許可できるデバイス
デバイスは、サービス データ暗号化キーの変更開始の承認に先立ち、次の条件を満たす必要があります。

* デバイスがサービス データ暗号化キーの変更の承認の対象となるようにオンラインである必要があります。
* キーの変更が開始されなかった場合は、30 分経過後にもう一度同じデバイスを承認できます。
* 別のデバイスを承認するには、既に承認済みのデバイスによってキーの変更が開始されていないことが条件となります。 新しいデバイスが承認された後に、以前のデバイスが変更を開始することはできません。
* サービス データ暗号化キーのロールオーバーの実行中に、デバイスを承認することはできません。
* サービスに登録されているデバイスの中に、暗号化をロールオーバーしたデバイスと、ロールオーバしていないデバイスがある場合、デバイスを承認できます。 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>手順 2:StorSimple 用 Windows PowerShell を使用してサービス データ暗号化キーの変更を開始する
この手順は、承認済みの StorSimple デバイスの StorSimple 用 Windows PowerShell インターフェイスで実行されます。

> [!NOTE]
> キーのロールオーバーが完了するまで、StorSimple Manager サービスの Azure Portal では操作を行うことができません。


デバイスのシリアル コンソールを使用して Windows PowerShell インターフェイスに接続している場合は、次の手順を実行します。

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>サービス データ暗号化キーの変更を開始するには
1. オプション 1 を選択して、フル アクセスでログオンします。
2. コマンド プロンプトに、次のコマンドを入力します。
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. コマンドレットが正常に完了すると、新しいサービス データ暗号化キーが表示されます。 このキーをコピーし、このプロセスの手順 3. で使用するために保存します。 このキーは、StorSimple Manager サービスに登録されている残りのすべてのデバイスの更新に使用されます。
   
   > [!NOTE]
   > このプロセスは、StorSimple デバイスを承認してから 4 時間以内に開始する必要があります。
   > 
   > 
   
   4 時間を経過すると、この新しいキーはサービスに送信され、サービスに登録されているすべてのデバイスにプッシュされます。 そのようになると、サービスのダッシュボードにアラートが表示されます。 このサービスにより、登録済みのデバイス上でのすべての操作が無効になります。その後、デバイスの管理者は、他のデバイスのサービス データ暗号化キーを更新する必要があります。 ただし、I/O (データをクラウドに送信するホスト) は中断されません。
   
   サービスに登録されているデバイスが 1 台の場合は、ロールオーバー プロセスが完了し、次の手順をスキップできます。 サービスに登録されているデバイスが複数ある場合は、手順 3. に進みます。

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>手順 3:他の StorSimple デバイス上のサービス データ暗号化キーを更新する
StorSimple Manager サービスに登録されているデバイスが複数ある場合は、StorSimple デバイスの Windows PowerShell インターフェイスで次の手順を実行する必要があります。 手順 2. で取得したキーを使用して、StorSimple Manager サービスに登録されている残りのすべての StorSimple デバイスを更新する必要があります。

デバイスのサービス データ暗号化を更新するには、次の手順を実行します。

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>物理デバイス上のサービス データ暗号化キーを更新するには
1. StorSimple 用 Windows PowerShell を使用して、コンソールに接続します。 オプション 1 を選択して、フル アクセスでログオンします。
2. コマンド プロンプトに「`Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`」と入力します。
3. 「[手順 2. StorSimple 用 Windows PowerShell を使用してサービス データ暗号化キーの変更を開始する](#to-initiate-the-service-data-encryption-key-change)」で取得したサービス データ暗号化キーを指定します。

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>すべての 8010/8020 クラウド アプライアンス上のサービス データ暗号化キーを更新するには
1. [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell スクリプトをダウンロードして設定します。 
2. PowerShell を開き、コマンド プロンプトに次のように入力します。`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

このスクリプトは、デバイス マネージャーにあるすべての 8010/8020 クラウド アプライアンスで、サービス データ暗号化キーが設定されていることを確認します。

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Update 5.0 より前のバージョンを実行しているデバイスでサポートされる操作
Azure Portal では、Update 5.0 以降を実行している StorSimple デバイスのみがサポートされます。 古いバージョンを実行しているデバイスのサポートは限定的です。 Azure Portal に移行した後は、次の表を使用して、Update 5.0 以前のバージョンを実行しているデバイス上でどの操作がサポートされているかを把握してください。

| Operation                                                                                                                       | サポートされています      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| デバイスの登録                                                                                                               | はい            |
| 全般、ネットワーク、セキュリティなどのデバイス設定の指定                                                                | はい            |
| 更新プログラムのスキャン、ダウンロード、およびインストール                                                                                             | はい            |
| デバイスの非アクティブ化                                                                                                               | はい            |
| デバイスの削除                                                                                                                   | はい            |
| ボリューム コンテナーの作成、変更、および削除                                                                                   | いいえ             |
| ボリュームの作成、変更、および削除                                                                                             | いいえ             |
| バックアップ ポリシーの作成、変更、および削除                                                                                      | いいえ             |
| 手動バックアップの取得                                                                                                            | いいえ             |
| スケジュールされたバックアップの作成                                                                                                         | 適用なし |
| バックアップセットからの復元                                                                                                        | いいえ             |
| Update 3.0 以降を実行しているデバイスへの複製 <br> ソース デバイスは Update 3.0 より前のバージョンを実行しています。                                | はい            |
| Update 3.0 より前のバージョンを実行しているデバイスへの複製                                                                          | いいえ             |
| ソース デバイスとしてのフェールオーバー <br> (Update 3.0 より前のバージョンを実行しているデバイスから、Update 3.0 以降を実行しているデバイスに)                                                               | はい            |
| ターゲット デバイスとしてのフェールオーバー <br> (Update 3.0 より前のソフトウェア バージョンを実行しているデバイスに)                                                                                   | いいえ             |
| アラートのクリア                                                                                                                  | はい            |
| バックアップ ポリシー、バックアップ カタログ、ボリューム、ボリューム コンテナー、監視チャート、ジョブ、およびクラシック ポータルで作成されたアラートの表示 | はい            |
| デバイス コントローラーのオンとオフ                                                                                              | はい            |


## <a name="next-steps"></a>次のステップ
* [StorSimple デプロイのプロセスの詳細](storsimple-8000-deployment-walkthrough-u2.md)
* [StorSimple ストレージ アカウントの管理の詳細](storsimple-8000-manage-storage-accounts.md)
* [StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理方法](storsimple-8000-manager-service-administration.md)
