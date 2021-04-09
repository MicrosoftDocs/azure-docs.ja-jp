---
title: ARM テンプレートに関してよく寄せられる質問
description: Azure Resource Manager テンプレート (ARM テンプレート) に関してよく寄せられる質問 (FAQ)。
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 24baa0dc7444f632040ddaf9fcc2266f9e4ccae5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036142"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>ARM テンプレートに関してよく寄せられる質問

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) についてよく寄せられる質問に回答します。

## <a name="getting-started"></a>作業の開始

* **ARM テンプレートとは何ですか。なぜ使用する必要があるのでしょうか。**

  ARM テンプレートは、Azure にデプロイする対象を定義する JSON ファイルです。 テンプレートは、Azure のコードとしてのインフラストラクチャ ソリューションを実装するのに役立ちます。 組織は、必要なインフラストラクチャを別々の環境に繰り返し、確実にデプロイできます。

  ARM テンプレートが Azure インフラストラクチャの管理をどのようにサポートするかの詳細については、「[ARM テンプレートとは](overview.md)」を参照してください。

* **テンプレートの使用を開始するにはどうすればよいですか。**

  ARM テンプレートの作成を簡単にするには、適切なツールが必要です。 [Visual Studio Code](https://code.visualstudio.com/) と [Azure Resource Manager ツール拡張機能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)をインストールすることをお勧めします。 これらのツールの概要紹介については、「[クイックスタート:Visual Studio Code を使用して ARM テンプレートを作成する](quickstart-create-templates-use-visual-studio-code.md)」を参照してください。

  ARM テンプレートの作成について学習する準備ができたら、[ARM テンプレートの初心者向けチュートリアル シリーズ](template-tutorial-create-first-template.md)を開始してください。 これらのチュートリアルでは、ARM テンプレートの構築プロセスを段階的に説明します。 テンプレートのさまざまなセクションと、それらがどのように連携するかについて学習します。 このコンテンツは、[Microsoft Learn モジュール](/learn/modules/authoring-arm-templates/)にも用意されています。

* **ARM テンプレートまたは Terraform のどちらを使用して Azure にデプロイする必要がありますか。**

  お好みのオプションをお使いください。 どちらのサービスも、Azure へのデプロイを自動化するのに役立ちます。

  他のコードとしてのインフラストラクチャ サービスと比較して、ARM テンプレートを使用することには利点があると考えられます。 これらの利点の詳細については、「[ARM テンプレートを選択する理由](overview.md#why-choose-arm-templates)」を参照してください。

## <a name="build-2020"></a>Build 2020

* **Microsoft Build 2020 のプレゼンテーションを見逃しました。プレゼンテーションを視聴することはできますか。**

  はい。[いつでも視聴](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions)していただけます。

* **Build で発表された新機能に関する詳細情報はどこで入手できますか。**

  Microsoft が現在開発中の機能に関する情報全般については、[Azure Advisor のデプロイに関する Yammer グループ](https://aka.ms/ARMMeet)に参加してください。

  新しいテンプレート言語について学習するには、[サインアップして通知を受け取る](https://aka.ms/armLangUpdates)ようにしてください。

  テンプレート スペックの詳細については、「[Azure Resource Manager のテンプレート スペック (プレビュー)](template-specs.md)」を参照してください。

## <a name="creating-and-testing-templates"></a>テンプレートの作成とテスト

* **ARM テンプレートのベスト プラクティスについては、どこで学習できますか。**

  テンプレートの実装方法に関するレコメンデーションについては、「[ARM テンプレートのベストプラクティス](template-best-practices.md)」を参照してください。 テンプレートを作成したら、[ARM テスト ツールキット](https://github.com/azure/arm-ttk)を実行します。 テンプレートが推奨プラクティスと一致しているかどうかを確認します。

* **ポータルを使用して環境をセットアップしました。既存のリソース グループからテンプレートを取得する方法はありますか。**

  はい。リソース グループから[テンプレートをエクスポート](export-template-portal.md)できます。 エクスポートされたテンプレートは、テンプレートについて理解するための出発点として適していますが、運用環境で使用する前に変更することをお勧めします。

  テンプレートをエクスポートするときに、テンプレートに含めるリソースを選択できます。

* **ARM テンプレートにリソース グループを作成し、そこにリソースをデプロイすることはできますか。**

  はい。Azure サブスクリプションのレベルでテンプレートをデプロイするときに、テンプレートにリソース グループを作成できます。 リソース グループを作成してリソースをデプロイする例については、「[リソース グループとリソース](deploy-to-subscription.md#resource-groups)」を参照してください。

* **ARM テンプレートにサブスクリプションを作成できますか。**

  はい。詳細については、[最新の API を使用して Azure サブスクリプションをプログラムで作成する](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)方法に関するページをご覧ください。

* **デプロイ前にテンプレートをテストするにはどうすればよいですか。**

  デプロイする前に、[ARM テスト ツールキット](https://github.com/azure/arm-ttk)と [what-if 操作](template-deploy-what-if.md)をテンプレートで実行することをお勧めします。 テスト ツールキットでは、テンプレートでベスト プラクティスが使用されているかどうかが確認されます。 テンプレートの実装方法を改善する可能性がある変更が特定されると、警告が表示されます。

  what-if 操作によって、テンプレートにより環境に対して行われる変更が表示されます。 デプロイされる前に、意図しない変更を確認できます。 また、what-if により、プレフライト検証中に検出できるエラーも返されます。 たとえば、テンプレートに構文エラーが含まれている場合は、そのエラーが返されます。 他にも、デプロイされたリソースの最終的な状態について判断できるエラーも返されます。 たとえば、テンプレートで既に使用されている名前を持つストレージ アカウントがデプロイされる場合は、what-if によってそのエラーが返されます。

* **各リソースの種類で使用可能なプロパティに関する情報はどこで入手できますか。**

  VS Code には、リソースのプロパティを操作するための IntelliSense が用意されています。 また、プロパティと説明については、[テンプレートのリファレンス](/azure/templates/)で確認することもできます。

* **リソースの種類の複数インスタンスを作成する必要があります。テンプレートに反復子を作成するにはどうすればよいですか。**

  複数のインスタンスを指定するには、copy 要素を使用します。 copy は、[リソース](copy-resources.md)、[プロパティ](copy-properties.md)、[変数](copy-variables.md)、および[出力](copy-outputs.md)で使用できます。

## <a name="template-language"></a>テンプレート言語

* **新しいテンプレート言語を開発中であると聞きました。その詳細情報はどこで確認できますか。**

  新しい言語に関する詳細については、「[Bicep とは (プレビュー)](bicep-overview.md)」を参照してください。

* **YAML でのテンプレートの作成をサポートする予定はありますか。**

  現在、YAML をサポートする予定はありません。 新しいテンプレート言語では、AML や JSON よりも使いやすいソリューションが提供される予定です。

* **新しいテンプレート言語がリリースされた後も、JSON でテンプレートを作成できますか。**

  はい、引き続き JSON テンプレートを使用できます。

* **JSON テンプレートを新しいテンプレート言語に変換するためのツールは用意されますか。**

  はい。 「[JSON から Bicep への逆コンパイル](compare-template-syntax.md#decompile-json-to-bicep)」を参照してください。

## <a name="template-specs"></a>Template Specs

* **Template Specs のプレビュー リリースの使用を開始するにはどうすればよいですか。**

  最新バージョンの PowerShell または Azure CLI をインストールします。 Azure PowerShell の場合は、[バージョン 5.0.0 以降](/powershell/azure/install-az-ps)を使用します。 Azure CLI の場合は、[バージョン 2.14.2 以降](/cli/azure/install-azure-cli)を使用します。

* **テンプレート スペックと Azure Blueprints はどのように関連していますか。**

  Azure Blueprints では、`blueprint definition` リソースを `template spec` リソースに置き換えることによって、その実装でテンプレート スペックが使用されます。 Microsoft ではブループリント定義をテンプレート スペックに変換するための移行パスを用意しますが、ブループリント定義 API は引き続きサポートされます。 `blueprint assignment` リソースは変更されません。 Blueprints では、Azure で管理された環境を構築するためのユーザー エクスペリエンスが維持されます。

* **テンプレート スペックで、リンクされたテンプレートは置き換えられますか。**

  いいえ。ただし、テンプレート スペックは、リンクされたテンプレートで適切に機能するように設計されています。 親テンプレートをデプロイする前に、リンクされたテンプレートをパブリックにアクセスできるエンドポイントに移動する必要はありません。 代わりに、テンプレート スペックを作成するときに、親テンプレートとその成果物をまとめてパッケージ化します。

* **テンプレート スペックを複数のサブスクリプションにわたって共有できますか。**

  はい。ユーザーがテンプレート スペックに対する読み取りアクセス権を持っている限り、複数のサブスクリプションにわたって使用できます。テンプレート スペックは、複数のテナントにわたって使用することはできません。

## <a name="scripts-in-templates"></a>テンプレート内のスクリプト

* **テンプレートで実行できないタスクを実行するためのスクリプトをテンプレートに含めることはできますか。**

  はい。[デプロイ スクリプト](deployment-script-template.md)を使用してください。 テンプレートには、Azure PowerShell または Azure CLI のスクリプトを含めることができます。 この機能はプレビュー段階にあります。

* **カスタム スクリプト拡張機能と Desired State Configuration (DSC) は引き続き使用できますか。**

  それらのオプションは変わりなく、引き続き使用できます。 デプロイ スクリプトは、VM ゲストに関連しないアクションを実行するように設計されています。 VM 内のホスト オペレーティング システム上でスクリプトを実行する必要がある場合は、カスタム スクリプト拡張機能や DSC を選択することをお勧めします。 ただし、デプロイ スクリプトには、タイムアウト期間の設定などの利点があります。

* **デプロイ スクリプトは Azure Government でサポートされていますか。**

  はい。US Gov アリゾナと US Gov バージニアでデプロイ スクリプトを使用できます。

## <a name="preview-changes-before-deployment"></a>デプロイ前の変更のプレビュー

* **テンプレートをデプロイする前に、発生する変更をプレビューできますか。**

  はい。[what-if 機能](template-deploy-what-if.md)を使用してください。 環境の現在の状態が評価され、デプロイ後に存在する状態と比較されます。 変更の概要を確認して、テンプレートに予期しない結果が含まれていないことを確認できます。

* **増分モードと完全モードの両方で what-if を使用できますか。**

  はい。両方の[デプロイ モード](deployment-modes.md)がサポートされています。 増分モードの使用例については、「[what-if 操作を実行する](template-deploy-what-if.md#run-what-if-operation)」を参照してください。 完全モードの使用例については、「[削除の確定](template-deploy-what-if.md#confirm-deletion)」を参照してください。

* **what-if はリンクされたテンプレートで動作しますか。**

  はい。what-if では、親テンプレートとそこにリンクされたテンプレートの状態が評価されます。

* **Azure パイプラインで what-if を使用できますか。**

  はい。what-if を使用して、パイプラインを続行するかどうかを確認できます。

* **what-if を使用すると、テンプレートに含まれていないプロパティの変更が表示されます。この "ノイズ" は想定されたものですか。**

  Microsoft ではノイズの削減に取り組んでいます。 こちらの GitHub リポジトリで問題を送信することで、品質向上にご協力ください。 https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>テンプレート ビジュアライザー

* **ARM テンプレートとそのリソースを視覚化する方法はありますか。**

  ARM テンプレートを視覚化するうえで優れた働きをする、[コミュニティによって提供された VS Code 拡張機能](https://aka.ms/ARMVisualizer)を用意しています。 デプロイしているリソースとそれらの間の関係が表示されます。

* **VS Code の外部でテンプレート ビジュアライザーを使用できますか。**

  テンプレート ビジュアライザーのポータルでの使用はプレビュー段階にあります。 詳細については、こちらの [Build からの短いセッション](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions)をご覧ください。

## <a name="deployment-limits"></a>デプロイの制限

* **1 回のデプロイ操作でデプロイできるリソース グループの数を教えてください。**

  以前は、5 個のリソース グループに制限されていました。 最近では、800 個のリソース グループに増加しています。 詳細については、「[サブスクリプション レベルでリソース グループとリソースを作成する](deploy-to-subscription.md)」を参照してください。

* **デプロイ履歴にデプロイは 800 個までに制限されているというエラーが発生しました。どうすればよいですか。**

  Microsoft ではリソース グループのデプロイ履歴の管理方法を変更しています。 以前は、このエラーを回避するには、この履歴から手動でデプロイを削除する必要がありました。 2020 年 6 月から、この制限に近づくと、デプロイは履歴から自動的に削除されます。 詳細については、「[デプロイ履歴からの自動削除](deployment-history-deletions.md)」を参照してください。

  履歴からデプロイを削除しても、デプロイされたリソースには影響しません。

## <a name="templates-and-devops"></a>テンプレートと DevOps

* **ARM テンプレートを Azure Pipelines に統合できますか。**

  はい。 テンプレートとパイプラインの使用方法の詳細については、「[チュートリアル: Azure Pipelines を使用した ARM テンプレートの継続的インテグレーション](deployment-tutorial-pipeline.md)」と「[Azure Pipelines を使用した ARM テンプレートの統合](add-template-to-azure-pipelines.md)」を参照してください。

* **GitHub のアクションを使用してテンプレートをデプロイできますか。**

  はい。「[GitHub Actions を使用した ARM テンプレートのデプロイ](deploy-github-actions.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

ARM テンプレートの概要については、「[ARM テンプレートとは](overview.md)」を参照してください。
