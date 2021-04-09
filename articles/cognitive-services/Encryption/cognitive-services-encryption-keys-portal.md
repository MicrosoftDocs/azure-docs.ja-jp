---
title: Cognitive Services 用のカスタマー マネージド キー
titleSuffix: Cognitive Services
description: Azure portal を使用して、Azure Key Vault でカスタマー マネージド キーを構成する方法について説明します。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、および取り消しを行うことができます。
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 88a723abc606a527232b7c1949f35c1fedfdba50
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706845"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Cognitive Services 用に Azure Key Vault でカスタマー マネージド キーを構成する

Cognitive Services 用に Azure Key Vault でカスタマー マネージド キーを有効にするプロセスは、製品ごとに異なります。 サービス固有の手順については、以下のリンクを使用してください。

## <a name="vision"></a>視覚

* [Custom Vision による保存データの暗号化](../custom-vision-service/encrypt-data-at-rest.md)
* [Face サービスによる保存データの暗号化](../face/encrypt-data-at-rest.md)
* [Form Recognizer による保存データの暗号化](../form-recognizer/encrypt-data-at-rest.md)

## <a name="language"></a>Language

* [Language Understanding サービスでの保存データの暗号化](../LUIS/encrypt-data-at-rest.md)
* [QnA Maker での保存データの暗号化](../QnAMaker/encrypt-data-at-rest.md)
* [Translator による保存データの暗号化](../translator/encrypt-data-at-rest.md)

## <a name="decision"></a>決定

* [Content Moderator による保存データの暗号化](../Content-Moderator/encrypt-data-at-rest.md)
* [Personalizer による保存データの暗号化](../personalizer/encrypt-data-at-rest.md)

## <a name="next-steps"></a>次のステップ

* [Azure Key Vault とは](../../key-vault/general/overview.md)
* [Cognitive Services カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)