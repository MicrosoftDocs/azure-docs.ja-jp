---
author: baanders
description: Azure Digital Twins サンプルにおける DefaultAzureCredential 用のインクルード ファイル - 注意事項
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bec2681c33108417aab1a33932c4f5f4d2ed730f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102473820"
---
>[!NOTE]
> このサンプルでは、ローカル コンピューターで実行された Azure Digital Twins インスタンスに対し、(`Azure.Identity` ライブラリの) [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) を使用してユーザーを認証します。 このサンプルは、この種類の認証を使用してローカル環境内の Azure 資格情報 (ローカルの [Azure CLI](/cli/azure/install-azure-cli) からのログインや、Visual Studio または Visual Studio Code でのログインなど) を検索します。
>
> `DefaultAzureCredential` とその他の認証オプションの使用の詳細については、[*アプリ認証コードを作成する方法*](../articles/digital-twins/how-to-authenticate-client.md)に関するページで確認します。