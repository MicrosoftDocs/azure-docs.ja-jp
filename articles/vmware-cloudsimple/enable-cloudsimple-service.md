---
title: CloudSimple サービスによる Azure VMware ソリューションの有効化
description: Azure サブスクリプションの CloudSimple サービスを有効にし、その後 CLoudSimple リソース プロバイダーを登録する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2553aa95d5028c510b4e1a1b7f51a9f410bcea51
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154852"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Azure サブスクリプションで Microsoft.VMwareCloudSimple リソース プロバイダーを登録する

Azure VMware Solution by CloudSimple は、CloudSimple サービスを通じて利用できます。 Microsoft.VMwareCloudSimple サービスをリソース プロバイダーとして登録できます。

## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[すべてのサービス]** を選択します。

3. **サブスクリプション**を検索して選択します。

    ![[サブスクリプション] を選択する](media/cloudsimple-service-select-subscriptions.png)

4. CloudSimple サービスを有効にするサブスクリプションを選択します。

5. サブスクリプションの **[リソース プロバイダー]** をクリックします。

6. **Microsoft.VMwareCloudSimple** を使用してリソース プロバイダーをフィルタ処理します。

7. リソース プロバイダーを選択し、 **[登録]** をクリックします。

    ![リソース プロバイダーの登録](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>次の手順

* [CloudSimple サービスを作成する](create-cloudsimple-service.md)方法を学習する
* [プライベート クラウドの環境を構成する](quickstart-create-private-cloud.md)方法を学習する
