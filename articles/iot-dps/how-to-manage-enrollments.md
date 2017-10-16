---
title: "Azure IoT Hub 用にデバイス登録を管理する方法 | Microsoft Docs"
description: "Azure Portal で DPS サービス 用にデバイス登録を管理する方法"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c230e73f83d8acd0f142e037f70a80c9e0e4107e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-device-enrollments-in-the-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service でデバイス登録を管理する方法

"*デバイス登録*" では、1 つのデバイスまたはデバイス グループのレコードを作成して、ある時点で Azure IoT Hub Device Provisioning Service に登録できるようにします。 登録レコードには、必要な IoT ハブを含む、目的のデバイス初期構成がその登録の一部として含まれます。 この記事では、プロビジョニング サービス用にデバイス登録を管理する方法を説明します。


## <a name="create-a-device-enrollment"></a>デバイス登録を作成する

プロビジョニング サービスにデバイスを登録する方法には次の 2 つがあります。

1. **登録グループ**は、同じルート CA によって署名された X.509 証明書の構成証明メカニズムを共有するデバイス グループのエントリです。 必要な初期構成を共有する多数のデバイスがある場合や、すべてのデバイスが同じテナントに配置される場合は、登録グループを使用することをお勧めします。 "*登録グループ*" として X.509 構成証明メカニズムを使用するデバイスのみを登録することができます。 

    次の手順を使用して、任意のデバイス グループ用に、ポータルで登録グループを作成することができます。

    1. Azure Portal にログインし、左側のメニューにある **[すべてのリソース]** をクリックします。
    2. リソースの一覧から、デバイスを登録する Device Provisioning Service をクリックします。
    3. 目的のプロビジョニング サービスで、**[Manage enrollments]\(登録の管理\)** をクリックし、**[Enrollment Groups]\(登録グループ\)** タブを選択します。
    4. 上部にある **[追加]** をクリックし、登録リストのエントリに必要な情報を入力します。 デバイス グループのルート証明書をアップロードします。 
    5. [ **Save**] をクリックします。 登録グループの作成に成功すると、**[Enrollment Groups]\(登録グループ\)** タブにグループ名が表示されます。 

        ![ポータルでの登録グループ](./media/how-to-manage-enrollments/group-enrollment.png)

    
2. **個別登録**は、登録する単一のデバイスのエントリです。 個別登録では、構成証明メカニズムとして X.509 証明書または (実際の TPM または仮想 TPM の) SAS トークンを使用できます。 固有の初期構成を必要とするデバイスや、TPM または仮想 TPM を介した SAS トークンのみを構成証明メカニズムとして使用できるデバイスには、個別登録を使用することをお勧めします。 個別登録では、必要な IoT ハブ デバイス ID が指定されている場合があります。

    個別登録は、次の手順を使用してポータルで作成できます。 

    1. Azure Portal にログインし、左側のメニューにある **[すべてのリソース]** をクリックします。
    2. リソースの一覧から、デバイスを登録する Device Provisioning Service をクリックします。
    3. 目的のプロビジョニング サービスで、**[Manage enrollments]\(登録の管理\)** をクリックし、**[Individual Enrollments]\(個別登録)** タブを選択します。
    4. 上部にある **[追加]** をクリックします。 
    5. デバイスのセキュリティ メカニズムを選択し、登録リストのエントリに必要な情報を入力します。 デバイスに X.509 が実装されている場合は、署名証明書をアップロードします。 
    6. [ **Save**] をクリックします。 登録グループの作成に成功すると、**[Individual Enrollments]\(個別登録)** タブにデバイスが表示されます。 

        ![ポータルでの個別登録](./media/how-to-manage-enrollments/individual-enrollment.png)


## <a name="update-an-enrollment-entry"></a>登録エントリを更新する
既存の登録エントリは、次の手順を使用して、ポータルで更新することができます。

1. Azure Portal で目的の Device Provisioning Service を開き、**[Manage enrollments]\(登録の管理\)** をクリックします。 
2. 変更する登録エントリに移動します。 エントリをクリックすると、デバイス登録に関する概要情報が表示されます。 
3. このページで、デバイスのリンク先となる IoT ハブやデバイス ID など、セキュリティの種類および資格情報以外の項目を変更することができます。 初期のデバイス ツインの状態を変更することもできます。 
4. 完了したら、**[保存]** をクリックしてデバイス登録を更新します。 

    ![ポータルで登録を更新する](./media/how-to-manage-enrollments/update-enrollment.png)


## <a name="remove-a-device-enrollment"></a>デバイス登録を削除する
デバイスをどの IoT ハブにもプロビジョニングする必要がない場合は、ポータルで次の手順を使用して、関連する登録エントリを削除できます。

1. Azure Portal で目的の Device Provisioning Service を開き、**[Manage enrollments]\(登録の管理\)** をクリックします。 
2. 削除する登録エントリを見つけて選択します。 
3. 上部にある **[削除]** をクリックして、確認を求められたら **[はい]** を選択します。 
5. アクションが完了すると、デバイス登録の一覧からエントリが削除されたことがわかります。 
 
    ![ポータルで登録を削除する](./media/how-to-manage-enrollments/remove-enrollment.png)



