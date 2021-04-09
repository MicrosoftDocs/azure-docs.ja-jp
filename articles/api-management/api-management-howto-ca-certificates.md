---
title: カスタム CA 証明書を追加する - Azure API Management | Microsoft Docs
description: Azure API Management でカスタム CA 証明書を追加する方法について説明します。 証明書を削除する手順についても確認できます。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 124bc053aa2c6e59e205bb6f33a9a96190799499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93102039"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Azure API Management でカスタム CA 証明書を追加する方法

Azure API Management を使用すると、信頼できるルート証明書ストアと中間証明書ストア内のマシンに CA 証明書をインストールできます。 サービスにカスタム CA 証明書が必要な場合、この機能を使用する必要があります。

この記事では、Azure portal の Azure API Management サービス インスタンスの CA 証明書を管理する方法について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>CA 証明書をアップロードする

![CA 証明書を追加する](media/api-management-howto-ca-certificates/00.png)

新しい CA 証明書をアップロードするには、次の手順を実行します。 API Management サービス インスタンスをまだ作成していない場合は、[API Management サービス インスタンスの作成](get-started-create-service-instance.md)に関するチュートリアルを参照してください。

1. Azure portal で Azure API Management サービス インスタンスに移動します。

2. メニューから **[CA 証明書]** を選択します。

3. **[+ 追加]** ボタンをクリックします。  

    ![CA 証明書を追加するための [+ 追加] ボタンを示すスクリーンショット。](media/api-management-howto-ca-certificates/01.png)  

4. 証明書を参照し、証明書ストアを指定します。 公開キーのみが必要なので、パスワードは必要ありません。

    ![証明書を参照する方法を示すスクリーンショット。](media/api-management-howto-ca-certificates/02.png)  

5. **[保存]** をクリックします。 この操作には数分かかることがあります。

    ![証明書を保存する方法を示すスクリーンショット。](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> `New-AzApiManagementSystemCertificate` Powershell コマンドを使用して CA 証明書をアップロードできます。

## <a name="delete-a-client-certificate"></a>クライアント証明書の削除<a name="step1a"> </a>

証明書を削除するには、コンテキスト メニューの **[...]** をクリックし、証明書の横にある **[削除]** を選択します。

![CA 証明書を削除する](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
