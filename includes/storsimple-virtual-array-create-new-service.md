---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: b7bdeeedaac65f67a3224e824c19e8cad794682b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507615"
---
#### <a name="to-create-a-new-service"></a>新しいサービスを作成するには

1.  Microsoft アカウントの資格情報を使用して、この URL (<https://portal.azure.com/>) から Azure Portal にサインインします。 Government ポータルでデバイスをデプロイする場合、<https://portal.azure.us/> でサインインします。

2.  Azure Portal で、 **[+ リソースの作成]** &gt; **[ストレージ]** &gt; **[StorSimple 仮想シリーズ]** の順にクリックします。

    ![新しいサービスの作成](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  表示された **[StorSimple デバイス マネージャー]** ブレードで、次の操作を実行します。

    1.  サービスの一意の**リソース名**を指定します。 リソース名は、サービスの識別に使用できるフレンドリ名です。 名前の長さは 2 ～ 50 文字とし、文字、数字、ハイフンを含めることができます。 名前の最初と最後は、文字か数字とする必要があります。

    2.  **[サブスクリプション]** ボックスの一覧で、サブスクリプションを選択します。 サブスクリプションは、課金アカウントにリンクされます。 このフィールドは、保有するサブスクリプションが 1 つだけの場合は表示されません。

    3.  **リソース グループ**については、既存のグループを選択するか、新しく作成します。 詳細については、[Azure のリソース グループ](/azure/azure-resource-manager/management/manage-resource-groups-portal)に関する記事をご覧ください。

    4.  サービスの **[場所]** を指定します。 どのリージョンでどのサービスを使用できるかについては、「[Azure リージョン](https://azure.microsoft.com/regions/#services)」を参照してください。 一般的に、デバイスをデプロイする地理的リージョンに最も近い**場所**を選択します。 あるいは次も考慮できます。

        -   Azure 内の既存のワークロードも StorSimple デバイスと一緒にデプロイする場合、そのデータセンターを使用する必要があります。

        -   StorSimple デバイス マネージャーと Azure Storage は別々の場所に置くことができます。 その場合、StorSimple デバイス マネージャーと Azure ストレージ アカウントを別々に作成する必要があります。 Azure ストレージ アカウントを作成するには、Azure portal で Azure Storage に移動し、「[ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)」に記載されている手順を行います。 このアカウントを作成したら、「[サービスの新しいストレージ アカウントを構成する](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service)」に記載されている手順に従って、StorSimple デバイス マネージャー サービスにアカウントを追加します。

        -   Government ポータルに仮想デバイスをデプロイする場合、米国アイオワ州と米国バージニア州で StorSimple デバイス マネージャー サービスを使用できます。

    5.  **[新しい Azure ストレージ アカウントを作成する]** をオンにすると、サービスの作成時にストレージ アカウントが自動的に作成されます。 **ストレージ アカウント名**を指定します。 別の場所でデータが必要になる場合、このボックスをオフにします。

    6.  ダッシュボードにこのサービスへのクイック リンクが必要な場合、 **[ダッシュボードにピン留めする]** チェック ボックスをオンにします。

    7.  **[作成]** をクリックして、StorSimple デバイス マネージャーを作成します。

        ![新しいサービスの作成](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

**[サービス]** ランディング ページが表示されます。 サービスの作成には数分かかります。 サービスが正常に作成されると、適宜、通知が表示され、サービスの状態が **"アクティブ"** に変わります。


