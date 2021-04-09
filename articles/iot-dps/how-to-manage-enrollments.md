---
title: Azure portal で Azure IoT Hub Device Provisioning Service に対するデバイスの登録を管理する
description: Azure portal 上でデバイス プロビジョニング サービス (DPS) 用にデバイス登録を管理する方法
author: wesmc7777
ms.author: wesmc
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: 6ec146a05df1b896f8ca594d29cf13341b70765a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010964"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Azure portal でデバイス登録を管理する方法

"*デバイス登録*" では、1 つのデバイスまたはデバイス グループのレコードを作成して、ある時点で Azure IoT Hub Device Provisioning Service に登録できるようにします。 登録レコードには、デバイスの初期構成がその登録の一部として含まれます。 構成には、デバイスが割り当てられる IoT ハブ、またはハブのセットからハブを構成する割り当てポリシーが含まれます。 この記事では、プロビジョニング サービス用にデバイス登録を管理する方法を説明します。


## <a name="create-a-device-enrollment"></a>デバイス登録を作成する

プロビジョニング サービスにデバイスを登録する方法には次の 2 つがあります。

* **登録グループ** は、共通の構成証明メカニズムを共有するデバイスのグループに対するエントリです。 初期構成を共有するデバイスが多数ある場合や、デバイスがすべて同じテナントに属する予定の場合は、登録グループを使用することをお勧めします。 [対称キー](concepts-symmetric-key-attestation.md)または [X.509 証明書](concepts-x509-attestation.md)を使用しているデバイスがサポートされます。 

    対称キーで登録グループを作成して使用する詳細な手順については、[対称キーによるデバイスのプロビジョニング](how-to-legacy-device-symm-key.md)に関するチュートリアルを参照してください。

    ポータルでデバイスのグループ用の登録グループを作成するには、次の手順のようにします。

    1. Azure Portal にログインし、左側のメニューにある **[すべてのリソース]** をクリックします。  
    1. リソースの一覧から、デバイスを登録する Device Provisioning Service をクリックします。  
    1. プロビジョニング サービスで、 **[登録を管理します]** をクリックし、上部の **[登録グループの追加]** ボタンをクリックします。  
     
        ![ポータルでの登録グループ](./media/how-to-manage-enrollments/add-group-enrollment.png)
        
    1. [登録グループの追加] パネルが表示されたら、登録の情報を入力して、 **[保存]** をクリックします。  
     
        [![ポータルで登録グループを追加する](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
        
        | フィールド | 説明 |
        | :--- | :--- |
        | **グループ名** | デバイスのグループに必要な名前。 |
        | **構成証明の種類** | デバイスで使用する構成証明の方法に応じて、構成証明の種類で **[証明書]** または **[対称キー]** をクリックします。 |
        | **証明書の種類** | 証明書の構成証明を使用する場合に使用できます。 デバイス証明書への署名に使用された証明書に基づいて、 **[CA 証明書]** または **[中間証明書]** を選択します。 |
        | **プライマリ証明書** | ルート CA 証明書を使用してデバイス証明書に署名している場合、そのルート CA 証明書は[所有証明](how-to-verify-certificates.md)が完了している必要があります。 その後で、デバイス グループの **プライマリ証明書** として選択できます。<br><br>中間証明書を使用してデバイス証明書に署名している場合は、アップロード ボタンが使用できるようになり、中間証明書をアップロードできます。 中間証明書に署名した証明書についても、[所有証明](how-to-verify-certificates.md)が完了している必要があります。 |

        
    

* **[個別の登録]** は、IoT ハブに割り当てることができる単一のデバイス用のエントリです。 [対称キー](concepts-symmetric-key-attestation.md)、[X.509 証明書](concepts-x509-attestation.md)、[TPM 構成証明](concepts-tpm-attestation.md)を使用しているデバイスがサポートされます。 

    個別登録は、次の手順を使用してポータルで作成できます。

    1. Azure Portal にログインし、左側のメニューにある **[すべてのリソース]** をクリックします。
    1. リソースの一覧から、デバイスを登録する Device Provisioning Service をクリックします。
    1. プロビジョニング サービスで、 **[登録を管理します]** をクリックし、上部の **[個別登録の追加]** ボタンをクリックします。   

       [![ポータルで個別登録を追加する](./media/how-to-manage-enrollments/add-individual-enrollment.png)](./media/how-to-manage-enrollments/add-individual-enrollment.png#lightbox)

    1. [登録の追加] パネルが表示されたら、個々のデバイスの登録情報を入力して、 **[保存]** をクリックします。 
     
        [![ポータルでの個別登録](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)
    
        | フィールド | 説明 |
        | :--- | :--- |
        | **メカニズム** | デバイスで使用する構成証明の方法に応じて、使用する構成証明メカニズムを **[X.509]** 、 **[TPM]** 、 **[対称キー]** から選択します。 |
        | 構成証明に関する設定 | 対称キーまたは X.509 証明書で個別登録を作成して使用する詳細な手順については、[対称デバイスのプロビジョニング](quick-create-simulated-device-symmetric-key-java.md#create-a-device-enrollment)または [X.509 証明書デバイスのプロビジョニング](quick-create-simulated-device-x509-java.md#create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry)に関するクイックスタートを参照してください。<br><br>TPM 構成証明で個別登録を作成して使用する詳細な手順については、[シミュレートされた TPM デバイスのプロビジョニング](quick-create-simulated-device-tpm-java.md#create-a-device-enrollment-entry)に関するサンプルのいずれかを参照してください。|
        | **IoT Hub のデバイス ID** |  この ID はデバイスを表します。 デバイス ID の規則に従う必要があります。 詳細については、「[デバイス ID のプロパティ](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)」を参照してください。<br><br>X.509 証明書を使用する場合、このテキストは、登録用にアップロードするデバイス証明書のサブジェクト名である必要があります。 そのサブジェクト名は、デバイス ID の規則に準拠している必要があります。|
            


## <a name="update-an-enrollment-entry"></a>登録エントリを更新する
既存の登録エントリは、次の手順を使用して、ポータルで更新できます。

1. Azure Portal で目的の Device Provisioning Service を開き、**[Manage enrollments]\(登録の管理\)** をクリックします。 
1. 変更する登録エントリに移動します。 エントリをクリックすると、デバイス登録に関する概要情報が表示されます。 
1. このページで、デバイスのリンク先となる IoT ハブやデバイス ID など、セキュリティの種類および資格情報以外の項目を変更することができます。 初期のデバイス ツインの状態を変更することもできます。 
1. 完了したら、**[保存]** をクリックしてデバイス登録を更新します。 

    ![ポータルで登録を更新する](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>デバイス登録を削除する
デバイスをどの IoT ハブにもプロビジョニングする必要がない場合は、ポータルで次の手順を使用して、関連する登録エントリを削除できます。

1. Azure Portal で目的の Device Provisioning Service を開き、**[Manage enrollments]\(登録の管理\)** をクリックします。 
1. 削除する登録エントリを見つけて選択します。 
1. 上部にある **[削除]** をクリックして、確認を求められたら **[はい]** を選択します。 
1. アクションが完了すると、デバイス登録の一覧からエントリが削除されたことがわかります。 
 
    ![ポータルで登録を削除する](./media/how-to-manage-enrollments/remove-enrollment.png)