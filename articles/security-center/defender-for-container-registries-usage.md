---
title: コンテナー レジストリ用の Azure Defender を使用する方法
description: コンテナー レジストリ用の Azure Defender を使用して、Linux でホストされたレジストリ内の Linux イメージをスキャンする方法について説明します
author: memildin
ms.author: memildin
ms.date: 10/21/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ee4992e41e792b570d8937edfe31efb4c651d742
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100731"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>コンテナー レジストリ用の Azure Defender を使用してイメージの脆弱性をスキャンする

このページでは、組み込みの脆弱性スキャナーを使用して、Azure Resource Manager ベースの Azure Container Registry に格納されているコンテナー イメージをスキャンする方法について説明します。

**コンテナー レジストリ用の Azure Defender** が有効になっている場合、ご利用のレジストリにプッシュしたイメージは直ちにスキャンされます。 さらに、過去 30 日以内にプルされたイメージもスキャンされます。 

スキャナーから Security Center に脆弱性が報告されると、その結果と関連情報が推奨事項として Security Center によって表示されます。 さらに、結果には関連情報 (修復の手順、関連する CVE、CVES スコアなど) も含まれています。 1 つまたは複数のサブスクリプション、あるいは特定のレジストリで識別された脆弱性を表示することができます。


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Azure コンテナー レジストリでイメージの脆弱性を識別する 

Azure Resource Manager ベースの Azure Container Registry に格納されているイメージの脆弱性のスキャンを有効にするには、次のようにします。

1. ご利用のサブスクリプションで **コンテナー レジストリ用の Azure Defender** を有効にします。 これで、Security Center でレジストリ内のイメージをスキャンする準備が整いました。

    >[!NOTE]
    > この機能では、イメージごとに課金されます。

1. イメージ スキャンは、プッシュまたはインポートが行われるたびに、およびイメージが過去 30 日以内にプルされている場合にトリガーされます。 

    スキャンが完了すると (通常は約 2 分後ですが、最大で 15 分かかることもあります)、結果が Security Center の推奨事項として表示されます。

1. [以下で説明するように結果を表示して修復します](#view-and-remediate-findings)。

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>他のコンテナー レジストリでイメージの脆弱性を識別する 

1. ACR ツールを使用すれば、Docker Hub または Microsoft Container Registry からイメージをご利用のレジストリに取り込むことができます。  インポートが完了すると、インポートされたイメージが Azure Defender によってスキャンされます。 

    詳細については、「[コンテナー レジストリにコンテナー イメージをインポートする](../container-registry/container-registry-import-images.md)」を参照してください。

    スキャンが完了すると (通常は約 2 分後ですが、最大で 15 分かかることもあります)、結果が Security Center の推奨事項として表示されます。

1. [以下で説明するように結果を表示して修復します](#view-and-remediate-findings)。


## <a name="view-and-remediate-findings"></a>結果の表示と修復

1. 結果を表示するために、 **[推奨事項]** ページへ移動します。 問題が見つかった場合は、**Azure Container Registry イメージの脆弱性を修復する必要があります** という推奨事項が表示されます。

    ![問題を修復するための推奨事項 ](media/monitor-container-security/acr-finding.png)

1. 推奨事項を選択します。 

    追加情報を示した推奨事項の詳細ページが開きます。 この情報には、脆弱性のあるイメージ ("影響を受けるリソース") を含むレジストリの一覧とその修復手順が含まれます。 

1. 特定のレジストリを選択して、その中の脆弱なリポジトリを含むリポジトリを確認します。

    ![レジストリの選択](media/monitor-container-security/acr-finding-select-registry.png)

    影響を受けるリポジトリの一覧を示したレジストリの詳細ページが開きます。

1. 特定のリポジトリを選択して、その中の脆弱性のあるイメージを含むリポジトリを確認します。

    ![リポジトリの選択](media/monitor-container-security/acr-finding-select-repository.png)

    リポジトリの詳細ページが開きます。 脆弱性のあるイメージが、結果の重大度の評価と共に一覧表示されます。

1. 脆弱性を表示するには、特定のイメージを選択します。

    ![イメージの選択](media/monitor-container-security/acr-finding-select-image.png)

    選択されたイメージに対応する結果の一覧が開きます。

    ![結果の一覧](media/monitor-container-security/acr-findings.png)

1. 結果の詳細を確認するには、その結果を選択します。 

    結果の詳細ペインが開きます。

    [![結果の詳細ペイン](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    このペインには、問題の詳細な説明と、脅威を軽減するための外部リソースへのリンクが含まれます。

1. このペインの修復セクションにある手順に従います。

1. セキュリティの問題を修復するために必要な手順を実行したら、レジストリ内のイメージを置き換えます。

    1. 更新されたイメージをプッシュします。 これにより、スキャンがトリガーされます。 
    
    1. 推奨事項のページ上で、推奨事項 "Azure Container Registry イメージの脆弱性を修復する必要があります" を確認します。 
    
        推奨事項が引き続き表示され、対処済みのイメージが脆弱性のあるイメージの一覧にまだ表示されている場合は、修復手順をもう一度確認します。

    1. 更新されたイメージがプッシュされ、スキャンされ、推奨事項にもう表示されないことが確認できたら、脆弱性のある "古い" イメージをレジストリから削除します。


## <a name="disable-specific-findings-preview"></a>特定の検出結果を無効にする (プレビュー)

> [!NOTE]
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]

組織のニーズとして、検出結果を修復するのではなく無視する場合は、必要に応じて検出結果を無効にすることができます。 無効化された検出結果は、セキュリティ スコアに影響を与えたり、不要なノイズを生成したりすることはありません。

検出結果が、無効化ルールで定義した条件に一致する場合、検出結果の一覧には表示されません。 一般的なシナリオは次のとおりです。

- 重大度が中以下の検出結果を無効にする
- パッチを適用できない検出結果を無効にする
- CVSS スコアが 6.5 未満の検出結果を無効にする
- セキュリティ チェックまたはカテゴリに特定のテキスト("RedHat"、"CentOS Security Update for sudo" など) が含まれている検出結果を無効にする

> [!IMPORTANT]
> ルールを作成するには、Azure Policy でポリシーを編集するためのアクセス許可が必要です。
>
> 詳細については、「[Azure Policy における RBAC アクセス許可](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)」を参照してください。

次のいずれかの条件を使用できます。 

- ID の検索 
- カテゴリ
- セキュリティ チェック 
- CVSS v3 スコア
- 重大度 
- パッチ適用可能な状態 

ルールを作成するには:

1. **Azure Container Registry イメージの脆弱性を修復する必要があります** という推奨事項の詳細ページで、 **[ルールを無効にする]** を選択します。
1. 関連するスコープを選択します。
1. 条件を定義します。
1. **[ルールの適用]** を選択します。

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="レジストリで VA の結果の無効化ルールを作成する":::

1. ルールを表示、無効化、または削除するには、次の手順に従います。 
    1. **[ルールを無効にする]** を選択します。
    1. アクティブなルールが適用されているサブスクリプションは、スコープの一覧に **[Rule applied]\(ルール適用済み\)** と表示されます。
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="既存のルールを変更または削除する":::
    1. ルールを表示または削除するには、省略記号メニュー ("...") を選択します。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Defender に関する詳細情報](azure-defender.md)