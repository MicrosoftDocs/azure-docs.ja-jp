---
title: Azure portal を使用して Azure Stack Edge の順序の問題をトラブルシューティングする | Microsoft Docs
description: Azure Stack Edge の順序の問題をトラブルシューティングする方法について説明します。
services: databox
author: twooley
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: twooley
ms.openlocfilehash: 226274c52610e24c305400d77dc7737d32c2b722
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783996"
---
# <a name="troubleshoot-your-azure-stack-edge-ordering-issues"></a>Azure Stack Edge の順序の問題をトラブルシューティングする

この記事では、Azure Stack Edge の順序の問題をトラブルシューティングする方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 順序の問題をトラブルシューティングする

## <a name="unsupported-subscription-or-region"></a>サポートされていないサブスクリプションまたはリージョン

**エラーの説明:** Azure portal で、次のエラーが表示された場合:

*選択されたサブスクリプションまたはリージョンはサポートされていません。別のサブスクリプションまたはリージョンを選択してください。*

![サポートされていないサブスクリプションまたはリージョン](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**推奨されている解決方法:** [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/)、[クラウド ソリューション プロバイダー (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)、[Microsoft Azure スポンサープラン](https://azure.microsoft.com/offers/ms-azr-0036p/)など、サポートされているサブスクリプションを使用していることを確認してください。 従量課金制サブスクリプションはサポートされていません。 詳細については、[Azure Stack Edge リソースの前提条件](azure-stack-edge-deploy-prep.md#prerequisites)に関するページを参照してください。

Microsoft では、サブスクリプションの種類のアップグレードがケース バイ ケースで許可される可能性があります。 [Microsoft サポート](https://azure.microsoft.com/support/options/)までご連絡ください。お客様のニーズを理解し、これらの制限を適切に調整できるようにいたします。

## <a name="selected-subscription-type-not-supported"></a>選択されたサブスクリプションの種類はサポートされていません

**エラー:** EA、CSP、またはスポンサー サブスクリプションを所有しており、次のエラーが表示されます。

*選択されたサブスクリプションの種類はサポートされていません。サポートされるサブスクリプションを使用していることを確認してください。[詳細については、こちらを参照してください](azure-stack-edge-deploy-prep.md#prerequisites)。サポートされるサブスクリプションの種類を使用している場合は、`Microsoft.DataBoxEdge` プロバイダーが登録されていることを確認してください。登録する方法については、「[リソース プロバイダーを登録する](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)* 」を参照してください。

**推奨されている解決方法:** 次の手順に従って Azure Stack Edge リソース プロバイダーを登録します。

1. Azure portal で、 **[ホーム]**  >  **[サブスクリプション]** に移動します。

2. デバイスを注文するために使用するサブスクリプションを選択します。

3. **[リソース プロバイダー]** を選択し、**Microsoft.DataBoxEdge** を検索します。

    ![リソース プロバイダーの登録](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

リソース プロバイダーを登録するための所有者または共同作成者のアクセス権がない場合は、次のエラーが表示されます。*次のリソース プロバイダーを登録するためのアクセス許可がサブスクリプション &lt;サブスクリプション名&gt; にありません:Microsoft.DataBoxEdge*

詳細については、「[リソース プロバイダーを登録する](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)」を参照してください。

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>Microsoft.DataBoxEdge がサブスクリプションに登録されいない

**エラー:** Azure portal で、Azure Stack Edge または Data Box Gateway に使用するサブスクリプションを選択すると、次のエラーが表示されます。

*リソース プロバイダー Microsoft.DataBoxEdge はサブスクリプション &lt;サブスクリプション名&gt; に登録されておらず、サブスクリプション &lt;サブスクリプション名&gt; のリソース プロバイダーを登録するためのアクセス許可がありません*。

**推奨されている解決方法:** サブスクリプションのアクセスを昇格させるか、またはリソース プロバイダーを登録するための所有者または共同作成者のアクセス権を持つだれかを見つけます。

## <a name="resource-disallowed-by-policy"></a>リソースがポリシーにより許可されない

**エラー:** Azure portal で、リソース プロバイダーを登録しようとすると、次のエラーが表示されます。

*リソース &lt;リソース名&gt; はポリシーにより許可されませんでした。(コード:RequestDisallowedByPolicy)。イニシアティブ: 一般に不要なリソースの種類を拒否します。ポリシー:許可されていないリソースの種類。*

**推奨されている解決方法:** このエラーは、リソースの作成をブロックする既存の Azure ポリシーのために発生します。 Azure ポリシーは、Azure リソースの使用または作成中のコンプライアンスを確保するために組織のシステム管理者によって設定されます。 このようなポリシーのいずれかが Azure Stack Edge リソースの作成をブロックしている場合は、システム管理者に連絡して Azure ポリシーを編集してください。

## <a name="next-steps"></a>次の手順

* [Azure Stack Edge に関する問題をトラブルシューティングする](azure-stack-edge-troubleshoot.md)方法の詳細について学習します。
