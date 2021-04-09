---
title: Azure Information Protection を Azure Sentinel に接続する
description: Azure Information Protection データ コネクタを構成することにより、Azure Information Protection から Azure Sentinel にログ情報をストリーミングします。
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 82ea47037902ce3a9449f71a9edf62cb80863d4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655988"
---
# <a name="connect-data-from-azure-information-protection"></a>Azure Information Protection からデータを接続する

> [!IMPORTANT]
> Azure Sentinel の Azure Information Protection データ コネクタは、現在パブリック プレビューです。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Information Protection データ コネクタを構成することにより、[Azure Information Protection](https://azure.microsoft.com/services/information-protection/) から Azure Sentinel にログ情報をストリーミングできます。 Azure Information Protection は、クラウドとオンプレミスのどちらに保存されているかにかかわらず、機密データを制御し、セキュリティで保護するのに役立ちます。

このサービスからのログ情報が、Azure Sentinel で現在選択されているのと同じ Log Analytics ワークスペースに格納されるように、[Azure Information Protection の中央レポート](/azure/information-protection/reports-aip)が既に構成されている場合は、このデータ コネクタの構成を省略できます。 Azure Information Protection からのログ情報は、Azure Sentinel で既に利用できます。

ただし、Azure Information Protection からのログ情報を、Azure Sentinel 用に現在選択されているものとは異なる Log Analytics ワークスペースに送る場合は、次のいずれかを行います。

- Azure Sentinel で選択されているワークスペースを変更します。

- Azure Information Protection 用のワークスペースを変更します。これは、このデータ コネクタを構成することによって行うことができます。
    
    ワークスペースを変更すると、Azure Information Protection に対する新しいレポート データは、Azure Sentinel 用に使用しているワークスペースに格納されるようになり、Azure Sentinel では履歴データを使用できなくなります。 さらに、前のワークスペースがカスタムのクエリ、アラート、または REST API 用に構成されている場合、それらを Azure Information Protection 用に引き続き使用するには、Azure Sentinel ワークスペース用に再構成する必要があります。 Azure Information Protection を使用するクライアントとサービスを再構成する必要はありません。

## <a name="prerequisites"></a>前提条件

- テナント用に次のいずれかの Azure AD 管理者ロール: 
    - Azure Information Protection 管理者
    - セキュリティ管理者
    - コンプライアンス管理者
    - コンプライアンス データ管理者
    - 全体管理者
    
    > [!NOTE]
    > お使いのテナントが[統合ラベル付けプラットフォーム](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)上にある場合は、Azure Information Protection 管理者ロールを使用できません。
    
    これらの管理者ロールは、Azure Information Protection コネクタを構成する場合にのみ必要であり、Azure Sentinel が Azure Information Protection に接続されている場合は必要ありません。

- Azure Sentinel および Azure Information Protection 用に使用している Log Analytics ワークスペースの読み取りと書き込みを行うためのアクセス許可。

- Azure Information Protection が Azure portal に追加されていること。 このステップに関するヘルプが必要な場合は、「[Azure portal に Azure Information Protection を追加する](/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)」を参照してください。

## <a name="connect-to-azure-information-protection"></a>Azure Information Protection に接続する

Azure Information Protection 用に Log Analytics ワークスペースを構成していない場合、または Azure Information Protection のログ情報を格納するワークスペースを変更する必要がある場合は、次の手順のようにします。

1. Azure Sentinel で **[データ コネクタ]**  >  **[Azure Information Protection (プレビュー)]** を選択します。

2. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

3. **[構成]** で、 **[Azure Information Protection ログを接続する]** を選択します。

4. **[分析の構成 (プレビュー)]** ブレードで、現在 Azure Sentinel 用に使用しているワークスペースを選択します。 別のワークスペースを選択した場合、Azure Sentinel では、Azure Information Protection からのレポート データを使用できません。

5. ワークスペースを選択したら、 **[OK]** を選択します。 コネクタの **[状態]** が、 **[接続済み]** に変わります。

6. Azure Information Protection からのレポート データは、選択したワークスペースの **InformationProtectionLogs_CL** テーブルに格納されます。 
    
    このレポート データに関連するスキーマを Azure Monitor で使用するには、**InformationProtectionEvents** を検索します。 これらのイベント関数については、Azure Information Protection のドキュメントの「[イベント関数のフレンドリ スキーマ リファレンス](/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions)」セクションを参照してください。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Information Protection を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。