---
title: Azure API Management でのプロトコルと暗号の管理 |Microsoft Docs
description: Azure API Management でプロトコル (TLS、SSL) および暗号 (DES) を管理する方法について説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: f7c7fdd06480ce3da70c86d38ab0685b9b3aaaf2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072403"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Azure API Management でプロトコルと暗号を管理する

Azure API Management では、3DES 暗号と同様にクライアントとバックエンドの両側の複数のバージョンの TLS プロトコルをサポートしています。

このガイドでは、Azure API Management インスタンスのプロトコルと暗号の構成を管理する方法について説明します。

![APIM でプロトコルと暗号を管理する](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、以下が必要です。

* API Management インスタンス

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>TLS プロトコルと 3DES 暗号を管理する方法

1. Azure portal で **API Management インスタンス**に移動します。
2. メニューから **[プロトコル設定]** を選択します。  
3. プロトコルまたは暗号を有効または無効にします。
4. **[Save]** をクリックします。 変更は 1 時間以内に適用されます。  

## <a name="next-steps"></a>次の手順

* [TLS (トランスポート層セキュリティ)](https://docs.microsoft.com/dotnet/framework/network-programming/tls)の詳細を参照してください。
* API Management についてのその他の [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=api-management) をご覧ください。