---
title: Azure Front Door Standard または Premium (プレビュー) のルール セットを使用してセキュリティ ヘッダーを構成する
description: この記事では、ルール セットを使用してセキュリティ ヘッダーを構成する方法に関するガイダンスを提供します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c08ba57f43969bb2f0ee9c66b6cb4e92879ed258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101097967"
---
# <a name="configure-security-headers-with-azure-front-door-standardpremium-preview-rule-set"></a>Azure Front Door Standard または Premium (プレビュー) のルール セットを使用してセキュリティ ヘッダーを構成する

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

この記事では、HTTP Strict-Transport-Security (HSTS)、X-XSS-Protection、Content-Security-Policy、X-Frame-Options など、ブラウザーベースの脆弱性を防ぐためにセキュリティ ヘッダーを実装する方法について説明します。 セキュリティベースの属性は、Cookie でも定義できます。

次の例では、ルート内のパスに一致するすべての着信要求に Content-Security-Policy ヘッダーを追加する方法を示します。 ここでは、信頼できるサイト **https://apiphany.portal.azure-api.net** からのスクリプトにのみ、アプリケーション上での実行を許可します。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

* セキュリティ ヘッダーを構成するには、まず、フロント ドアを作成する必要があります。 詳細については、「[クイック スタート: Front Door の作成](create-front-door-portal.md)」を参照してください。
* 以前にルール セット機能を使用していない場合は、[ルール セットを設定](how-to-configure-rule-set.md)する方法を確認してください。

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Azure portal で Content-Security-Policy ヘッダーを追加する

1. Azure Front Door Standard または Premium のプロファイルにアクセスし、 **[設定]** で **[ルール セット]** を選択します。

1. **[追加]** を選択して新しいルール セットを追加します。 ルール セットに **名前** を付け、ルールの **名前** を指定します。 **[アクションの追加]** を選択し、 **[応答ヘッダー]** を選択します。

1. 演算子を **Append** に設定して、このヘッダーを、このルートのすべての着信要求への応答として追加します。

1. ヘッダー名として「**Content-Security-Policy**」を追加し、このヘッダーで受け入れる値を定義します。 このシナリオでは、 *"script-src 'self' https://apiphany.portal.azure-api.net"* を選択します。

1. 必要なすべてのルールを構成に追加したら、必ず、ルール セットをルートに関連付けてください。 ルール セットによってアクションを実行できるようにするためには、この手順が "*必須*" です。 

> [!NOTE]
> このシナリオでは、ルールに[一致条件](concept-rule-set-match-conditions.md)を追加しませんでした。 関連付けられたルートに定義されているパスに一致するすべての着信要求に、このルールが適用されます。 これらの要求の一部にのみ適用する場合、必ず特定の **一致条件** をこのルールに追加してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

### <a name="deleting-a-rule"></a>規則の削除

前の手順では、ルール セットを使用して Content-Security-Policy ヘッダーを構成しました。 ルールが不要になった場合は、ルール セット名を選択して、[ルールの削除] を選択します。 

### <a name="deleting-a-rule-set"></a>ルール セットの削除

ルール セットを削除する場合は、削除の前に、すべてのルートから関連付けを解除してください。 ルール セットの削除に関する詳細なガイダンスについては、[ルール セットの構成](how-to-configure-rule-set.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

お使いのフロント ドア用に Azure Web Application Firewall を構成する方法については、[Web Application Firewall と Front Door](../../web-application-firewall/afds/afds-overview.md) に関する記事を参照してください。
