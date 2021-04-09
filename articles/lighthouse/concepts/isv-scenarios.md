---
title: ISV シナリオにおける Azure Lighthouse
description: ISV は、Azure Lighthouse の機能を使用して、顧客へのオファリングの柔軟性を高めることができます。
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696305"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>ISV シナリオにおける Azure Lighthouse

[Azure Lighthouse](../overview.md) の一般的なシナリオは、顧客の Azure Active Directory (Azure AD) テナントのリソースを管理するサービス プロバイダーです。 Azure Lighthouse の機能は、SaaS ベースのオファリングを使用して、独立系ソフトウェア ベンダー (ISV) がその顧客と共に使用することもできます。 Azure Lighthouse は、サブスクリプションのスコープへのアクセスを必要とする管理されたサービスまたはサポートを提供する ISV で特に役立ちます。

## <a name="managed-service-offers-in-azure-marketplace"></a>Azure Marketplace のマネージド サービス オファー

ISV は、Azure Marketplace にソリューションを既に発行している可能性があります。 マネージド サービスを顧客に提供する場合は、マネージド サービス オファーを発行することによってそれを行うことができます。 これらのオファーによって、オンボード プロセスが効率化され、必要とされる顧客の数に応じてサービスをさらに拡張できます。 Azure Lighthouse では、さまざまな[管理タスクとシナリオ](cross-tenant-management-experience.md#enhanced-services-and-scenarios)がサポートされており、顧客に価値を提供するためにそれらを使用できます。

詳細については、「[Azure Marketplace にマネージド サービス オファーを発行する](../how-to/publish-managed-services-offers.md)」を参照してください。

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>Azure Lighthouse を Azure Managed Applications とともに使用する

[Azure Managed Applications](../../azure-resource-manager/managed-applications/overview.md) は、ISV が顧客にサービスを提供できるもう 1 つの方法です。 Azure Lighthouse を Azure Managed Applications と共に使用することで、拡張されたシナリオを実現できます。

詳細については、「[Azure Lighthouse と Azure Managed Applications](managed-applications.md)」を参照してください。

## <a name="saas-based-multi-tenant-offerings"></a>SaaS ベースのマルチテナント オファリング

その他のシナリオとしては、ISV が独自のテナントのサブスクリプションでリソースをホストし、Azure Lighthouse を使用して、顧客がこれらのリソースにアクセスできるようにします。 その後、顧客は独自のテナントにログインし、必要に応じてこれらのリソースにアクセスできます。 ISV は独自のテナントで自社の IP を維持し、顧客のプランを使用するのではなく、独自のサポート プランを使用して、テナントでホストされているソリューションに関連するチケットを登録することができます。 リソースは ISV のテナント内にあるため、ISV がすべてのアクションを直接実行できます (VM へのログイン、アプリのインストール、メンテナンス タスクの実行など)。

このシナリオでは、顧客は ISV のリソースを管理していませんが、顧客のテナントのユーザーには基本的に "テナント管理" としてのアクセス権が付与されます。 顧客は ISV のテナントに直接アクセスしているため、顧客がソリューションや他の ISV リソースを誤って変更しないように、必要最小限のアクセス許可のみを付与することが重要です。

このアーキテクチャを有効にするには、ISV は顧客の Azure AD テナントにあるユーザー グループのオブジェクト ID とテナント ID を取得する必要があります。 その後、ISV は、このユーザー グループに適切なアクセス許可を付与する ARM テンプレートを作成して、顧客がアクセスするリソースを含む [ISV のサブスクリプションにデプロイ](../how-to/onboard-customer.md)します。

## <a name="next-steps"></a>次のステップ

- [テナント間の管理エクスペリエンス](cross-tenant-management-experience.md)について学習します。
- [Azure の委任されたリソース管理](azure-delegated-resource-management.md)について学習してください。