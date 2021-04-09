---
title: Azure IoT Hub Device Provisioning Service で X.509 CA 証明書を確認する
description: Azure IoT Hub Device Provisioning Service (DPS) で X.509 CA 証明書の所有証明を行う方法
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0326eef86b42fa8b06c336bbf2b1c6f2f9df0bcf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730356"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>デバイス プロビジョニング サービスで X.509 CA 証明書の所有証明を行う方法

検証済みの X.509 証明機関 (CA) 証明書は、アップロードされてプロビジョニング サービスに登録され、サービスで所有証明を行われた CA 証明書です。 

所有証明では次の手順が実行されます。
1. X.509 CA 証明書に対してプロビジョニング サービスによって生成された一意の確認コードを取得します。 これは Azure Portal から行うことができます。
2. サブジェクトとして確認コードを含む X.509 検証証明書を作成し、X.509 CA 証明書に関連付けられている秘密キーで証明書に署名します。
3. 署名された検証証明書をサービスにアップロードします。 サービスは、検証対象の CA 証明書のパブリック部分を使って検証証明書を検証し、ユーザーが CA 証明書の秘密キーを所有していることを証明します。

検証済みの証明書は、登録グループを使うときに重要な役割を果たします。 証明書の所有権の検証は、証明書のアップロード者が証明書の秘密キーを所有していることを確認することで、追加のセキュリティ レイヤーを提供します。 検証は、悪意のあるアクターがユーザーのトラフィックをスニッフィングして中間証明書を抽出し、その証明書を使って、独自のプロビジョニング サービスに登録グループを作成し、効果的にデバイスをハイジャックすることを防ぎます。 証明書チェーンのルートまたは中間証明書の所有権を証明することにより、ユーザーはその登録グループの一部として登録されるデバイスのリーフ証明書を生成する権限があることを証明します。 この理由により、登録グループで構成されるルートまたは中間証明書は、検証済みの証明書であるか、またはサービスで認証するときにデバイスが存在する証明書チェーンの検証済み証明書にロールアップする必要があります。 X.509 証明書の構成証明の詳細については、[X.509 証明書](concepts-x509-attestation.md)に関する記事および「[X.509 証明書を使用してプロビジョニング サービスへのデバイスのアクセスを制御する](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)」を参照してください。

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>X.509 証明書のパブリック部分を登録して確認コードを取得する

プロビジョニング サービスに CA 証明書を登録し、所有証明の間に使用できる確認コードを取得するには、以下の手順のようにします。 

1. Azure Portal でプロビジョニング サービスに移動し、左側のメニューから **[証明書]** を開きます。 
2. **[追加]** をクリックして新しい証明書を追加します。
3. 証明書のわかりやすい表示名を入力します。 X.509 証明書のパブリック部分を表す .cer または .pem ファイルを参照します。 **[アップロード]** をクリックします。
4. 証明書が正常にアップロードされたことを示す通知が表示されたら、 **[保存]** をクリックします。

    ![証明書のアップロード](./media/how-to-verify-certificates/add-new-cert.png)  

   証明書が、 **[証明書エクスプローラー]** の一覧に表示されます。 この証明書の **[状態]** が *[未確認]* になっていることに注意してください。

5. 前の手順で追加した証明書をクリックします。

6. **[証明書の詳細]** で、 **[確認コードを生成します]** をクリックします。

7. プロビジョニング サービスにより、証明書の所有権の検証に使うことができる **確認コード** が作成されます。 このコードをクリップボードにコピーします。 

   ![証明書の確認](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>確認コードにデジタル署名して検証証明書を作成する

次に、X.509 CA 証明書に関連付けられた秘密キーを使って、"*確認コード*" に署名する必要があります。これにより、署名が生成されます。 これは[所有証明](https://tools.ietf.org/html/rfc5280#section-3.1)と呼ばれ、署名された検証証明書になります。

Microsoft では、署名された検証証明書の作成に役立つツールとサンプルが提供されています。 

- **Azure IoT Hub C SDK** は、開発用の CA 証明書とリーフ証明書を作成し、確認コードを使って所有証明を実行するための、PowerShell (Windows) スクリプトと Bash (Linux) スクリプトを提供します。 システムに関連する[ファイル](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)を作業フォルダーにダウンロードし、[CA 証明書の管理の readme](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) の説明に従って、CA 証明書で所有証明を実行します。 
- **Azure IoT Hub C# SDK** には [グループ証明書の検証のサンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample)が含まれており、所有証明に使うことができます。
 
> [!IMPORTANT]
> 所有証明の実行に加えて、前に示した PowerShell スクリプトと Bash スクリプトでは、デバイスの認証とプロビジョニングに使うことができるルート証明書、中間証明書、およびリーフ証明書も作成できます。 これらの証明書は開発にのみ使ってください。 運用環境では使わないでください。 

ドキュメントと SDK で提供されている PowerShell スクリプトおよび Bash スクリプトは、[OpenSSL](https://www.openssl.org/) に依存します。 また、OpenSSL または他のサードパーティ製ツールを使って、所有証明を行うこともできます。 SDK で提供されているツールの使用例については、「[X.509 証明書チェーンを作成する](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain)」を参照してください。 


## <a name="upload-the-signed-verification-certificate"></a>署名された検証証明書をアップロードする

1. 結果の検証証明書としての署名を、ポータルのプロビジョニング サービスにアップロードします。 Azure Portal の **[証明書の詳細]** で、 **[.pem または .cer の検証証明書ファイル]** フィールドの隣の _[エクスプローラー]_ アイコンを使って、署名された検証証明書をシステムからアップロードします。

2. 証明書が正常にアップロードされたら、 **[確認]** をクリックします。 **[証明書エクスプローラー]** の一覧で、証明書の **[状態]** が **_確認済み_** に変わります。 自動的に更新されない場合は、 **[更新]** をクリックしてください。

   ![証明書のアップロードの確認](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>次のステップ

- ポータルを使って登録グループを作成する方法について詳しくは、「[Azure Portal でデバイス登録を管理する方法](how-to-manage-enrollments.md)」をご覧ください。
- サービス SDK を使って登録グループを作成する方法について詳しくは、「[Azure デバイス プロビジョニング サービス SDK でデバイスの登録を管理する方法](./quick-enroll-device-x509-java.md)」をご覧ください。