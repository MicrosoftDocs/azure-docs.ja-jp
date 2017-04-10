---
title: "StorSimple ボリューム コンテナーの管理 | Microsoft Docs"
description: "StorSimple Manager サービスの [ボリューム コンテナー] ページを使用して、ボリューム コンテナーを追加、変更、または削除する方法について説明します。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 1c64ce75-1fd3-4d3b-9304-d4dc0fc2b069
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/24/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4fb0f4e61ec98546e7044bf760d24a1ba932fe5b


---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-volume-containers"></a>StorSimple Manager サービスを使用して StorSimple ボリューム コンテナーを管理する
## <a name="overview"></a>概要
このチュートリアルでは、StorSimple Manager サービスを使用して、StorSimple ボリューム コンテナーを作成、管理する方法について説明します。

Microsoft Azure StorSimple デバイスのボリューム コンテナーには、ストレージ アカウント、暗号化、および帯域幅の使用量の設定を共有する、1 つ以上のボリュームが含まれています。 1 つのデバイスに、すべてのボリュームに対応する複数のボリューム コンテナーを含めることができます。 

また、ボリューム コンテナーには次の属性があります。

* **ボリューム** – ボリューム コンテナー内に含まれる、階層化された、またはローカルに固定された StorSimple ボリューム。 ボリューム コンテナーには、最大で 256 台の StorSimple ボリュームを含めることができます。
* **暗号化** – ボリューム コンテナーごとに定義できる暗号化キー。 このキーは、StorSimple デバイスからクラウドに送信されるデータの暗号化に使用します。 ミリタリーグレードの AES-256 ビット キーは、ユーザー入力のキーで使用します。 データをセキュリティで保護するために、クラウド ストレージの暗号化を常に有効にすることをお勧めします。
* **ストレージ アカウント** – クラウド ストレージのサービス プロバイダーにリンクされるストレージ アカウント。 ボリューム コンテナーに存在するすべてのボリュームで、このストレージ アカウントが共有されます。 ストレージ アカウントを既存の一覧から選択することもできますが、ボリューム コンテナーを作成した時点で新しいアカウントを作成し、後でそのアカウントのアクセス資格情報を指定することもできます。
* **クラウドの帯域幅** – デバイスのデータがクラウドに送信されるときに、デバイスが使用する帯域幅。 このコンテナーを定義するときに 1 ～ 1,000 Mbps の値を指定すると、帯域幅の制御を適用できます。 デバイスで使用できる帯域幅をすべて使用する場合は、このフィールドを [無制限] に設定します。 帯域幅テンプレートを作成、適用し、スケジュールに基づいて帯域幅を割り当てることもできます。

![[ボリューム コンテナー] ページ](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

次の手順は、StorSimple の **[ボリューム コンテナー]** ページを使用して、次の一般的な操作を完了する方法について説明するための手順です。

* ボリューム コンテナーを追加する 
* ボリューム コンテナーを変更する 
* ボリューム コンテナーを削除する 

## <a name="add-a-volume-container"></a>ボリューム コンテナーを追加する
ボリューム コンテナーを追加するには、次の手順を実行します。

[!INCLUDE [storsimple-add-volume-container](../../includes/storsimple-add-volume-container.md)]

## <a name="modify-a-volume-container"></a>ボリューム コンテナーを変更する
ボリューム コンテナーを変更するには、次の手順を実行します。

[!INCLUDE [storsimple-modify-volume-container](../../includes/storsimple-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>ボリューム コンテナーを削除する
ボリューム コンテナーには複数のボリュームが含まれています。 ボリューム コンテナーを削除する前に、ボリューム コンテナーに含まれるすべてのボリュームを削除する必要があります。 ボリューム コンテナーを削除するには、次の手順を実行します。

[!INCLUDE [storsimple-delete-volume-container](../../includes/storsimple-delete-volume-container.md)]

## <a name="next-steps"></a>次のステップ
* [StorSimple ボリュームの管理の詳細](storsimple-manage-volumes.md) 
* [StorSimple Manager サービスを使用した StorSimple デバイスの管理の詳細](storsimple-manager-service-administration.md)




<!--HONumber=Nov16_HO3-->


