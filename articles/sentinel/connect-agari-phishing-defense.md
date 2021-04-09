---
title: Agari のフィッシング対策とブランド保護ソリューションを Azure Sentinel に接続する | Microsoft Docs
description: Agari のフィッシング対策とブランド保護コネクタを使用して、そのログを Azure Sentinel にプルする方法について説明します。 Agari のデータをブックに表示し、アラートを作成し、調査を改善します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a60a0291d6669b2a9115dffa8e0d4d63fae4a440
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724440"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Agari のフィッシング対策とブランド保護ソリューションを Azure Sentinel に接続する

> [!IMPORTANT]
> Agari のフィッシング対策とブランド保護コネクタは現在、**プレビュー** の段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Agari のフィッシング対策とブランド保護コネクタを使用すると、ブランド保護とフィッシング対策ソリューションのログを Azure Sentinel に簡単に接続できます。これにより、データをブックで表示すること、データを照会してカスタム アラートを作成すること、データを組み込んで調査を改善することができます。 Agari のソリューションを、Azure Functions と REST API を使用して Azure Sentinel と統合します。

さらに、ブランド保護とフィッシング対応のお客様は、セキュリティ グラフ API を通じて、脅威インテリジェンスの共有を利用できます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

Agari のフィッシング対策とブランド保護ソリューションを Azure Sentinel に接続するには、次のものが必要です。

- Azure Sentinel ワークスペースに対する読み取りと書き込みのアクセス許可。

- ワークスペースの共有キーに対する読み取りアクセス許可。 ワークスペース キーの詳細については、[こちら](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)を参照してください。

- 関数アプリを作成するための、Azure Functions に対する読み取りと書き込みのアクセス許可。 [Azure Functions](../azure-functions/index.yml) の詳細を確認してください。

- Agari の **クライアント ID** と **秘密鍵** (すべての Agari ソリューションで同一) を所有していることを確認します。 手順については、[Agari の開発者サイト](https://developers.agari.com/agari-platform/docs/quick-start)を参照してください。

## <a name="configure-and-connect-agari-solutions"></a>Agari ソリューションを構成して接続する 

Agari ソリューションは、Azure 関数アプリを使用して Azure Sentinel と統合し、ログを直接エクスポートできます。

> [!NOTE]
> このコネクタは、Azure Functions を使用して Agari のソリューションに接続し、Azure Sentinel にログをプルします。 これにより、追加のデータ インジェスト コストが発生する可能性があります。 詳細については、「[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)」ページをご覧ください。

1. **Agari API 資格情報を収集します。** 

    Agari の **クライアント ID** と **秘密鍵** を持っていることを確認します。 手順については、[Agari の開発者サイト](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials)を参照してください。

1. **(オプション) セキュリティ グラフ API を有効にします。** 

    Agari 関数アプリを使用すると、セキュリティ グラフ API を介して Azure Sentinel と脅威インテリジェンスを共有できます。 この機能を使用するには、[Sentinel 脅威インテリジェンス プラットフォーム コネクタ](connect-threat-intelligence.md)を有効にし、Azure Active Directory で[アプリケーションを登録する](/graph/auth-register-app-v2)必要があります。

    このプロセスでは、関数アプリをデプロイするときに使用する 3 つの情報 (**Graph テナント ID**、**Graph クライアント ID**、**Graph クライアント シークレット**) が提供されます。

1. **コネクタとそれに関連付けられている Azure 関数アプリをデプロイします。** 

    1. Azure Sentinel ポータルで **[Data connectors]\(データ コネクタ\)** を選択します。 **[Agari のフィッシング対策とブランド保護 (プレビュー)]** 、 **[コネクタ ページを開く]** の順に選択します。

    1. **[構成]** で、Azure Sentinel の **[ワークスペース ID]** と **[主キー]** をコピーし、控えておきます。

    1. **[Azure に配置する]** を選択します。 (ボタンを見つけるために、下へスクロールする必要がある場合があります。)

    1. **[カスタム デプロイ]** 画面が表示されます。

        - Agari の **[クライアント ID]** と **[クライアント シークレット]** (秘密鍵) を入力します。

        - Azure Sentinel の **[ワークスペース ID]** と **[ワークスペース キー]** (主キー) に、先ほどコピーした値を入力します。

        - アクティブなサブスクリプションを持っている Agari ソリューションについて **[True]** または **[False]** を選択します。

        - セキュリティ グラフ API を使用して Azure Sentinel と IoC を共有するための Azure アプリケーションを作成した場合は、 **[セキュリティ グラフの共有を有効にする]** に **[True]** を選択し、 **[Graph テナント ID]** 、 **[Graph クライアント ID]** 、および **[Graph クライアント シークレット]** を入力します。

    1. **[Review + create]\(レビュー + 作成\)** を選択します。 検証が完了したら、 **[作成]** をクリックします。

1. **関数アプリに必要なアクセス許可を割り当てます。**

    Agari コネクタでは、環境変数を使用してログ アクセスのタイムスタンプを格納します。 アプリケーションでこの変数に書き込むには、システムに割り当てられている ID にアクセス許可を割り当てる必要があります。

    1. Azure portal で **[関数アプリ]** に移動します。

    1. **[関数アプリ]** ブレードで、一覧から関数アプリを選択し、関数アプリのナビゲーション メニューの **[設定]** で **[ID]** を選択します。

    1. **[システム割り当て済み]** タブで、 **[状態]** を **[オン]** に設定します。 

    1. **[保存]** を選択すると、 **[Azure でのロールの割り当て]** ボタンが表示されます。 このボタンをクリックします。

    1. **[Azure でのロールの割り当て]** 画面で、 **[ロールの割り当ての追加]** を選択します。 **[スコープ]** を **[サブスクリプション]** に設定し、 **[サブスクリプション]** ドロップダウンからサブスクリプションを選択し、 **[ロール]** を **[App Configuration データ所有者]** に設定します。 

    1. **[保存]** を選択します。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、次のテーブルの *[カスタム ログ]* の下にある **[ログ]** にデータが表示されます。 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Agari ソリューションのデータに対してクエリを実行するには、上記のテーブル名のいずれかをクエリ ウィンドウに入力します。

便利なサンプル クエリについては、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。 

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Agari のフィッシング対策とブランド保護ソリューションを Azure Sentinel に接続する方法を学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。