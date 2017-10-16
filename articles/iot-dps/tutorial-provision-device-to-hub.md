---
title: "Azure IoT Hub Device Provisioning Service を使用してデバイスをプロビジョニングする | Microsoft Docs"
description: "Azure IoT Hub Device Provisioning Service を使用して、1 つの IoT ハブにデバイスをプロビジョニングします。"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: bf50699d2dc67294d554ba15713254a8b88d8ade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service を使用した IoT ハブへのデバイスのプロビジョニング

前のチュートリアルでは、Device Provisioning Service に接続するデバイスを設定する方法を説明しました。 このチュートリアルでは、このサービスで "**_登録リスト_**" を使用して、1 つの IoT ハブにデバイスをプロビジョニングする方法について説明します。 このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * デバイスを登録する
> * デバイスを起動する
> * デバイスが登録されていることを確認する

## <a name="prerequisites"></a>前提条件

以降の手順に進む前に、チュートリアル「[Azure IoT Hub Device Provisioning Service を使用してプロビジョニングするデバイスの設定](./tutorial-set-up-device.md)」の説明に従って、デバイスと "*ハードウェア セキュリティ モジュール*" を構成してください。


<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>デバイスを登録する

この手順では、Device Provisioning Service にデバイスの一意のセキュリティ アーティファクトを追加する必要があります。 追加するセキュリティ アーティファクトは次のとおりです。

- TPM ベースのデバイスの場合:
    - 各 TPM チップまたはシミュレーションに固有の "*保証キー*"。 詳細については、「[TPM 保証キーとは](https://technet.microsoft.com/library/cc770443.aspx)」をご覧ください。
    - 名前空間/スコープ内でデバイスを一意に識別するために使用する "*登録 ID*"。 これは、デバイス ID と同じである場合もあれば、異なる場合もあります。 この ID はすべてのデバイスで必須です。 TPM ベースのデバイスでは、登録 ID が TPM 自体から派生している場合があります (TPM 保証キーの SHA-256 ハッシュなど)。

    ![ポータルに表示された TPM の登録情報](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)

- X.509 ベースのデバイスの場合:
    - *.pem* ファイルまたは *.cer* ファイルの形式で、[X.509 チップまたはシミュレーションに発行された証明書](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx)。 個別登録では、X.509 システムの "*署名者証明書*" を使用する必要があります。登録グループでは、"*ルート証明書*" を使用する必要があります。

    ![ポータルに表示された X.509 の登録情報](./media/tutorial-provision-device-to-hub/x509-device-enrollment.png)


デバイスを Device Provisioning Service に登録するには、次の 2 つの方法があります。

- **Enrollment Groups\(登録グループ\)**: これは、特定の構成証明メカニズムを共有するデバイスのグループを表します。 必要な初期構成を共有する多数のデバイスがある場合や、すべてのデバイスが同じテナントに配置される場合は、登録グループを使用することをお勧めします。

    ![ポータルに表示された X.509 の登録グループ](./media/tutorial-provision-device-to-hub/x509-enrollment-groups.png)

- **Individual Enrollments\(個別登録\)**: Device Provisioning Service に登録できる 1 つのデバイスのエントリを表します。 個別登録では、構成証明メカニズムとして X.509 証明書または (実際の TPM または仮想 TPM の) SAS トークンを使用できます。 固有の初期構成を必要とするデバイスや、TPM または仮想 TPM を介した SAS トークンのみを構成証明メカニズムとして使用できるデバイスには、個別登録を使用することをお勧めします。 個別登録では、必要な IoT ハブ デバイス ID が指定されている場合があります。

ポータルでデバイスを登録する手順は次のとおりです。

1. デバイスの HSM のセキュリティ アーティファクトを書き留めます。 開発環境では、前のチュートリアルの「[セキュリティ アーティファクトを抽出する](./tutorial-set-up-device.md#extractsecurity)」に記載された API を使用することが必要な場合があります。

1. Azure Portal にログインし、左側のメニューの **[すべてのリソース]** をクリックして、Device Provisioning Service を開きます。

1. Device Provisioning Service の概要ブレードで、**[Manage enrollments]\(登録の管理\)** を選択します。 デバイスの設定に従って、**[Individual Enrollments]/(個別登録\)** タブまたは **[Enrollment Groups]\(登録グループ\)** タブを選択します。 上部にある **[追加]** をクリックします。 ID 構成証明 "*メカニズム*" として **[TPM]** または **[X.509]** を選択し、前述の適切なセキュリティ アーティファクトを入力します。 新しい **IoT ハブ デバイス ID** を入力できます。 作業が完了したら、**[保存]** をクリックします。 

1. デバイスが正常に登録されると、ポータルに次のように表示されます。

    ![ポータルで正常に完了した TPM 登録](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)


## <a name="start-the-device"></a>デバイスを起動する

この時点で、次のようにデバイスの登録の準備が整っています。

1. デバイスまたはデバイス グループが Device Provisioning Service に登録されている。 
2. Device Provisioning Service クライアント SDK を使用して HSM チップが構成され、アプリケーションを介してアクセス可能な状態であり、デバイスの準備ができている。

デバイスを起動して、クライアント アプリケーションが Device Provisioning Service への登録を開始できるようにします。  


## <a name="verify-the-device-is-registered"></a>デバイスが登録されていることを確認する

デバイスが起動すると、次のアクションが実行されます。 詳細については、TPM シミュレーター サンプル アプリケーションの [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c) を参照してください。 

1. デバイスが Device Provisioning Service に登録要求を送信します。
2. TPM デバイスの場合、Device Provisioning Service が登録チャレンジを送信し、デバイスがこれに応答します。 
3. 登録が成功すると、Device Provisioning Service は、IoT ハブの URI、デバイス ID、暗号化されたキーをデバイスに送信します。 
4. デバイス上の IoT Hub クライアント アプリケーションがハブに接続します。 
5. ハブに正常に接続されると、IoT ハブの **Device Explorer** にデバイスが表示されます。 

    ![ポータルに表示されたハブへの成功した接続](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>次のステップ
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * デバイスを登録する
> * デバイスを起動する
> * デバイスが登録されていることを確認する

次のチュートリアルに進み、負荷分散されたハブに複数のデバイスをプロビジョニングする方法を学習してください。 

> [!div class="nextstepaction"]
> [負荷分散された IoT ハブにデバイスをプロビジョニングする](./tutorial-provision-multiple-hubs.md)
