---
title: Microsoft コマーシャル マーケットプレースの SaaS Fulfillment API
description: Microsoft AppSource および Azure Marketplace で SaaS オファーを統合できるようにする Fulfillment API について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2e6381afb19018822f6f37171a5ca4b3d929b42e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88037526"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>Microsoft コマーシャル マーケットプレースの SaaS Fulfillment API

SaaS Fulfillment API を使用すると、発行元 (独立系ソフトウェア ベンダー (ISV) とも呼ばれます) は、SaaS アプリケーションを Microsoft AppSource、Azure Marketplace、Azure portal と統合することができます。 これらの API により、ISV アプリケーションは、すべての商取引が有効になっているチャネル (直接、パートナー主導 (再販業者)、フィールド主導) に参加できるようになります。  これらの API との統合は、パートナー センターで取引可能な SaaS オファーを作成して公開するための要件です。

ISV と Microsoft の両方で同じサブスクリプションの状態を維持するために、ISV は次の API フローを SaaS サービス コードに追加することで実装する必要があります。

* ランディング ページ フロー:Microsoft は、発行元の SaaS オファーがマーケットプレースの顧客によって購入されたことを発行元に通知します。
* アクティベーション フロー:発行元は、新しく購入した SaaS アカウントが発行元側で構成されていることを Microsoft に通知します。
* 更新フロー:購入したプランの変更、または購入したシート数の変更。
* 中断および再開のフロー:顧客の支払い方法が無効になった場合に、購入した SaaS オファーを中断します。 支払い方法に関する問題が解決されると、中断されたオファーを再開できます。
* Webhook フロー:Microsoft は、Microsoft 側の顧客によってトリガーされた SaaS サブスクリプションの変更と解約について、発行元に通知します。

購入した SaaS サブスクリプションを解約する場合、統合は、Microsoft 側の顧客で行うことができるため、省略可能です。

SaaS Fulfillment API との統合を正しく行うことは、次を保証するために非常に重要です。

* 発行元の SaaS オファーを購入したエンド カスタマーが、Microsoft から正しく請求されること。
* エンド カスタマーが、マーケットプレースで購入した SaaS サブスクリプションを購入、構成、使用、および管理する際に適切なユーザー エクスペリエンスが得られていること。

これらの API を使用すると、発行元のオファーをすべてのコマース対応チャネルに参加させることができます。

* 直接
* パートナー主導 (再販業者、CSP)
* フィールド主導

リセラー (CSP) シナリオでは、CSP がエンド ユーザーに代わって SaaS オファーを購入します。 顧客は SaaS オファーを使用することが見込まれますが、CSP は次のことを行うエンティティです。

* 顧客に請求する
* サブスクリプション プランまたは購入したシート数を変更する
* サブスクリプションを解約する

発行元は、このシナリオのために API 呼び出しフローを異なる方法で実装する必要はありません。

CSP の詳細については、 https://partner.microsoft.com/licensing を参照してください。

>[!Warning]
>この API の最新バージョンはバージョン 2 であり、すべての新しい SaaS オファーではこれを使用する必要があります。 この API のバージョン 1 は非推奨であり、既存のオファーをサポートするために保持されています。

>[!Note]
>SaaS Fulfillment API は、発行元のバックエンド サービスから呼び出すことのみを目的としています。 発行元の Web ページから直接 API と統合することはサポートされていません。 サービス間認証フローのみを使用する必要があります。

## <a name="next-steps"></a>次のステップ

SaaS アプリケーションをまだ登録していない場合は、「[Azure AD アプリケーションの登録](./pc-saas-registration.md)」で説明されているように、[Azure portal](https://ms.portal.azure.com) で登録を行います。  その後、このインターフェイスの最新バージョンを使用して開発を行います。[SaaS Fulfillment API バージョン 2](./pc-saas-fulfillment-api-v2.md)
