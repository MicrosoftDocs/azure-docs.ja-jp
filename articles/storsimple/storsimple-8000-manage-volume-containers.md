---
title: StorSimple 8000 シリーズ デバイスのボリューム コンテナーを管理する
description: StorSimple デバイス マネージャー サービスの [ボリューム コンテナー] ページを使用して、ボリューム コンテナーを追加、変更、または削除する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 6f0a0fd48e3717d14a714e42c5566cd7bcf090d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545399"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>StorSimple デバイス マネージャー サービスを使用して StorSimple ボリューム コンテナーを管理する

## <a name="overview"></a>概要
このチュートリアルでは、StorSimple デバイス マネージャー サービスを使用して、StorSimple ボリューム コンテナーを作成、管理する方法について説明します。

Microsoft Azure StorSimple デバイスのボリューム コンテナーには、ストレージ アカウント、暗号化、および帯域幅の使用量の設定を共有する、1 つ以上のボリュームが含まれています。 1 つのデバイスに、すべてのボリュームに対応する複数のボリューム コンテナーを含めることができます。 

また、ボリューム コンテナーには次の属性があります。

* **ボリューム** – ボリューム コンテナー内に含まれる、階層化された、またはローカルに固定された StorSimple ボリューム。 
* **暗号化** – ボリューム コンテナーごとに定義できる暗号化キー。 このキーは、StorSimple デバイスからクラウドに送信されるデータの暗号化に使用します。 ミリタリーグレードの AES-256 ビット キーは、ユーザー入力のキーで使用します。 データをセキュリティで保護するために、クラウド ストレージの暗号化を常に有効にすることをお勧めします。
* **ストレージ アカウント** – データを格納するための Azure Storage アカウント。 ボリューム コンテナーに存在するすべてのボリュームで、このストレージ アカウントが共有されます。 ストレージ アカウントを既存の一覧から選択することもできますが、ボリューム コンテナーを作成した時点で新しいアカウントを作成し、後でそのアカウントのアクセス資格情報を指定することもできます。
* **クラウド帯域幅** – デバイスからクラウドにデータが送信されるときにデバイスが消費する帯域幅。 このコンテナーを作成するときに 1 ～ 1,000 Mbps の値を指定すると、帯域幅の制御を適用できます。 使用可能なすべての帯域幅をデバイスが消費できるようにする場合は、このフィールドを **無制限** に設定します。 帯域幅テンプレートを作成、適用し、スケジュールに基づいて帯域幅を割り当てることもできます。

次の手順は、StorSimple の **[ボリューム コンテナー]** ブレードを使用して、次の一般的な操作を完了する方法について説明するための手順です。

* ボリューム コンテナーを追加する
* ボリューム コンテナーを変更する
* ボリューム コンテナーを削除する

## <a name="add-a-volume-container"></a>ボリューム コンテナーを追加する
ボリューム コンテナーを追加するには、次の手順を実行します。

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>ボリューム コンテナーを変更する
ボリューム コンテナーを変更するには、次の手順を実行します。

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>ボリューム コンテナーを削除する
ボリューム コンテナーには複数のボリュームが含まれています。 ボリューム コンテナーを削除する前に、ボリューム コンテナーに含まれるすべてのボリュームを削除する必要があります。 ボリューム コンテナーを削除するには、次の手順を実行します。

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>次のステップ
* [StorSimple ボリュームの管理の詳細](storsimple-8000-manage-volumes-u2.md) 
* [StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理](storsimple-8000-manager-service-administration.md)の詳細

