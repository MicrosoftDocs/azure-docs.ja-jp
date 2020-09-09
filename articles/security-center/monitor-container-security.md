---
title: Azure Security Center でコンテナーのセキュリティを監視する
description: Azure Security Center からコンテナーのセキュリティの状態を確認する方法について説明します
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 5f558aaa367d156a8a4fa8633f113c6480236f5b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038616"
---
# <a name="monitor-the-security-of-your-containers"></a>コンテナーのセキュリティを監視する

このページでは、概要のセクションにある[コンテナーのセキュリティに関する記事](container-security.md)で説明されているコンテナー セキュリティ機能の使用方法について説明します。


## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>ARM ベース コンテナー レジストリの脆弱性をスキャンする 

1. お使いの Azure コンテナー レジストリのイメージの脆弱性スキャンを有効にするには、以下を行います。

    1. Azure Security Center の Standard 価格レベルを利用していることを確認します。

    1. **[Pricing & settings]\(価格と設定\)** ページから、お使いのサブスクリプションに対応するオプションの [コンテナー レジストリ] バンドルを有効にします。![[コンテナー レジストリ] バンドルを有効にする](media/monitor-container-security/enabling-container-registries-bundle.png)

        これで、Security Center では、レジストリにプッシュされるイメージをスキャンする準備ができました。 

        >[!NOTE]
        >この機能では、イメージごとに課金されます。


1. イメージのスキャンをトリガーするには、イメージをレジストリにプッシュします。 

    スキャンが完了すると (通常は約 2 分後ですが、最大で 15 分かかることもあります)、結果が次のような Security Center の推奨事項で表示されます。

1. 結果を表示するために、 **[推奨事項]** ページへ移動します。 問題が見つかった場合は、次の推奨事項が表示されます。

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


## <a name="hardening-your-containers-docker-hosts"></a>コンテナーの Docker ホストを強化する

Security Center では、Docker ホストの構成を常に監視し、業界標準を反映したセキュリティの推奨事項を生成します。

コンテナーの Docker ホストに対する Azure Security Center のセキュリティの推奨事項を表示するには、次の手順を実行します。

1. Security Center ナビゲーション バーから、 **[Compute & apps]\(コンピューターとアプリ\)** を開き、 **[コンテナー]** タブを選択します。

1. 必要に応じて、コンテナー リソースの一覧を [コンテナー ホスト] ホストにフィルター処理します。

    ![コンテナー リソースのフィルター処理](media/monitor-container-security/container-resources-filter.png)

1. コンテナー ホストのコンピューターの一覧から、1 つを選択して詳しく調べます。

    ![コンテナー ホストの推奨事項](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    ホストの詳細と推奨事項の一覧が示された **[Container host information]\(コンテナー ホスト情報\) ページ** が開きます。

1. 推奨事項の一覧から、1 つの推奨事項を選択して詳しく調べます。

    ![コンテナー ホストの推奨事項の一覧](media/monitor-container-security/container-host-rec.png)

1. 必要に応じて、説明、情報、脅威、修復手順を確認します。 

1. ページの下部にある **[アクションの実行]** を選択します。

    [![[アクションの実行] ボタン](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    実行の準備が整ったカスタム操作を示した Log Analytics が開きます。 既定のカスタム クエリには、問題を解決するためのガイドラインと共に評価済みの失敗したルールの全一覧が含まれています。

    [![Log Analytics のアクション](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. 必要に応じて、クエリ パラメーターを調整します。

1. コマンドが適切であり、ホストの準備ができていることを確認したら、 **[実行]** を選択します。



## <a name="next-steps"></a>次のステップ

この記事では、Security Center のコンテナー セキュリティ機能の使用方法について説明しました。 

その他の関連資料については、次のページを参照してください。 

- [コンテナーに関する Security Center の推奨事項](recommendations-reference.md#recs-containers)
- [AKS クラスター レベルのアラート](alerts-reference.md#alerts-akscluster)
- [コンテナー ホスト レベルのアラート](alerts-reference.md#alerts-containerhost)
