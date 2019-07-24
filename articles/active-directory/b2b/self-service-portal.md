---
title: B2B コラボレーションのためのセルフサービス サインアップ ポータル - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションは、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで会社間のリレーションシップをサポートします
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c3ad424e8cab444b2405715eaa468411166ebd9
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674278"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Azure AD B2B コラボレーションのサインアップ用のセルフ サービス ポータル

お客様は、[Azure portal](https://portal.azure.com) とエンド ユーザー向けの[アプリケーション アクセス パネル](https://myapps.microsoft.com)を通して公開される組み込み機能を使用して、さまざまな操作を実行できます。 ただし、ユーザーの組織のニーズに合わせて、B2B ユーザー向けのオンボード ワークフローをカスタマイズすることが必要な場合があります。 これは、[招待 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) を使って行うことができます。

招待する側の組織は、誰が社外のコラボレーターであり、誰が企業のリソースへのアクセスを必要としているのが事前にわからないことがあります。 招待する側の組織によって制御されるポリシーのセットに従って、パートナー企業のユーザーがサインアップするための方法が必要です。 このシナリオは、API を使って実現できます。 それを行う[サンプル プロジェクトが GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) にあります。

この GitHub プロジェクトでは、招待する組織が API を使って、信頼できるパートナーがアクセスできるアプリを決定するルールを作成し、パートナーに対してポリシー ベースのセルフサービス サインアップ機能を提供する方法が示されています。 パートナーのユーザーは、必要に応じてリソースにアクセスできます。 パートナーは安全にこれを行うことができ、招待する側の組織が手動でオンボードする必要はありません。 プロジェクトは、選択した Azure サブスクリプションに簡単にデプロイできます。

## <a name="as-is-code"></a>現況コード

このコードは、Azure Active Directory B2B 招待 API の使用法を示すサンプルとして利用できます。 開発チームまたはパートナーはカスタマイズして、運用シナリオにデプロイする前に確認する必要があります。

## <a name="next-steps"></a>次の手順

* [Azure AD B2B コラボレーションとは](what-is-b2b.md)
* [Azure AD B2B コラボレーションのライセンス](licensing-guidance.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](faq.md)
