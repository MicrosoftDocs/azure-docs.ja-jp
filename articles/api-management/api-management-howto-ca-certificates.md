---
title: カスタム CA 証明書を追加する - Azure API Management | Microsoft Docs
description: Azure API Management でカスタム CA 証明書を追加する方法について説明します。
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
ms.openlocfilehash: 21d5869f2bcdfb6383b6ef89869d8098135ea7ee
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073605"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Azure API Management でカスタム CA 証明書を追加する方法

Azure API Management を使用すると、信頼できるルート証明書ストアと中間証明書ストア内のマシンに CA 証明書をインストールできます。 サービスにカスタム CA 証明書が必要な場合、この機能を使用する必要があります。

この記事では、Azure portal の Azure API Management サービス インスタンスの CA 証明書を管理する方法について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>CA 証明書をアップロードする

![CA 証明書を追加する](media/api-management-howto-ca-certificates/00.png)

新しい CA 証明書をアップロードするには、次の手順を実行します。 API Management サービス インスタンスをまだ作成していない場合は、[API Management サービス インスタンスの作成](get-started-create-service-instance.md)に関するチュートリアルを参照してください。

1. Azure portal で Azure API Management サービス インスタンスに移動します。

2. メニューから **[CA 証明書]** を選択します。

3. **[+ 追加]** ボタンをクリックします。  

    ![CA 証明書を追加する](media/api-management-howto-ca-certificates/01.png)  

4. 証明書を参照し、証明書ストアを指定します。 公開キーのみが必要なので、パスワードは必要ありません。

    ![CA 証明書を追加する](media/api-management-howto-ca-certificates/02.png)  

5. **[Save]** をクリックします。 この操作には数分かかることがあります。

    ![CA 証明書を追加する](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> `New-AzApiManagementSystemCertificate` Powershell コマンドを使用して CA 証明書をアップロードできます。

## <a name="step1a"> </a>クライアント証明書の削除

証明書を削除するには、コンテキスト メニューの **[...]** をクリックし、証明書の横にある **[削除]** を選択します。

![CA 証明書を削除する](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
