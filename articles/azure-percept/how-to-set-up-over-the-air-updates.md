---
title: 更新を無線でデプロイするように Azure IoT Hub を設定する
description: Azure Percept DK に更新を無線でデプロイするように Azure IoT Hub を構成する方法について説明します
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f30ae818b9e74ff97d2bc26552ed7d7124b499
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101660535"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Azure Percept DK に更新を無線でデプロイするように Azure IoT Hub を設定する方法
無線での更新を使用して、Azure Percept DK を安全で最新の状態に保ちます。 簡単なわずかな手順で、Device Update for IoT Hub を使用して Azure 環境を設定し、Azure Percept DK に最新の更新をデプロイできます。

## <a name="create-a-device-update-account"></a>Device Update アカウントの作成

1. [Azure portal](https://portal.azure.com) にアクセスし、Azure Percept で使用している Azure アカウントでサインインします 

1. ページの上部にある検索ウィンドウで、「Device Update for IoT Hub」と入力し始めます

1. 検索ウィンドウに表示される **[Device Update for IoT Hub]** を選択します。

1. ページの左上にある **[+ 追加]** ボタンをクリックします。

1. Azure Percept デバイス (これは、オンボードの IoT Hub がある場所です) に関連付けられている Azure サブスクリプションとリソース グループを選択します。

1. Device Update アカウントの名前と場所を指定します

1. 詳細を確認し、 **[確認および作成]** を選択します。
 
1. デプロイが完了したら、 **[リソースに移動]** をクリックします。
 
## <a name="create-a-device-update-instance"></a>デバイス更新インスタンスの作成
ここで、自分の Device Update for IoT Hub アカウント内にインスタンスを作成します。

1. Device Update for IoT Hub リソース内で、 **[Instance Management]\(インスタンス管理\)** の下の **[インスタンス]** をクリックします。
 
1. **[+ 作成]** をクリックし、インスタンス名を指定して、Azure Percept デバイスに関連付けられている IoT Hub (つまり、オンボード エクスペリエンス中に作成されたもの) を選択します。 これは完了するまでに数分かかる場合があります。
 
1. **[作成]**

## <a name="configure-iot-hub"></a>IoT Hub を構成する

1. インスタンス管理の **[インスタンス]** ページで、デバイス更新インスタンスが **[成功]** 状態になるまで待機します。 **[削除]** の横にある **[最新の情報に更新]** アイコンをクリックして、状態を更新します。
 
1. 作成されたインスタンスを選択し、 **[Configure IoT Hub]\(IoT Hub の構成\)** をクリックします。 左側のペインで、 **[I agree to make these changes]\(これらの変更を行うことに同意します\)** を選択し、 **[更新]** をクリックします。
 
1. プロセスが正常に完了するまで待ちます。
 
## <a name="configure-access-control-roles"></a>アクセス制御ロールの構成
最後の手順では、更新を発行およびデプロイするためのアクセス許可をユーザーに付与できます。

1. Device Update for IoT Hub リソース内で、 **[アクセス制御 (IAM)]** をクリックします
 
2. **[+ 追加]** をクリックし、 **[ロールの割り当ての追加]** を選択します
 
3. **[ロール]** で、 **[Device Update Administrator]\(デバイス更新管理者\)** を選択します。 **[アクセスの割り当て先]** で、 **[User, group, or service principle]\(ユーザー、グループ、またはサービス プリンシパル\)** を選択します。 **[選択]** で、自分のアカウントか、更新をデプロイする人のアカウントを選択します。 その後、 **[保存]** をクリックします。 

    > [!TIP]
    > 組織内の他のユーザーにもアクセスを許可する場合は、この手順を繰り返して、それらの各ユーザーを **デバイス更新管理者** にすることができます。

## <a name="next-steps"></a>次のステップ

これで、Device Update for IoT Hub を使用して、[Azure Percept 開発キットを無線で更新](./how-to-update-over-the-air.md)できるようになりました。 Azure Percept デバイスに使用している Azure IoT Hub に移動します。