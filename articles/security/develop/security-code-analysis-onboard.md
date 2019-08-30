---
title: Microsoft Azure Security Code Analysis のオンボード ガイド
description: この記事は、Security Code Analysis 拡張機能のインストールに関するものです
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8a8eca73205d4f8f33d4d069fda72638af61d355
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718174"
---
# <a name="how-to-onboarding-and-installing"></a>方法:オンボードとインストール

Microsoft Security Code Analysis の使用を開始するための前提条件
  - 資格のある Microsoft 統合サポート サービス プラン (以下の詳細を参照)
  - Azure DevOps 組織
  - Azure DevOps 組織に拡張機能をインストールするためのアクセス許可
  - クラウドでホストされている Azure DevOps パイプラインに同期できるソース コード


## <a name="onboarding-microsoft-security-code-analysis-extension"></a>Microsoft Security Code Analysis 拡張機能のオンボード

- 以下のいずれかのサポート プランを既にお持ちの場合、拡張機能にアクセスするには、単にテクニカル アカウント マネージャーに連絡して、時間を購入するか、既存の時間を交換します。
   - 統合サポート – 詳細およびパフォーマンス レベル、開発者向け Premier サポート、パートナー向け Premier サポート、または企業向け Premier サポート。
- 以下のいずれかのサポート サービスをご利用の場合、または Microsoft のサポート プランをお持ちでない場合は、資格のあるサポート プランにアップグレードする必要があります。
   - パートナー向け Azure サポート、Azure Basic、Azure Developer、Azure Standard、Azure Professional Direct、または統合サポート – コア レベル
- 資格のあるサポート プランを購入するには、[サポート サービスのホーム ページ](https://www.microsoft.com/enterprise/services/support)にアクセスしてください
- サポート契約が成立したら、作業の開始を支援できるテクニカル アカウント マネージャーに連絡し、必要なすべての詳細情報を収集します。
 
>[!NOTE]
> Microsoft Partner Network への登録済みパートナーのみが、パートナー向け Premier サポートの購入資格を持ちます。それ以外の場合は、前に説明した資格のあるサポート プランのいずれかを購入してください

## <a name="installing-microsoft-security-code-analysis-extension"></a>Microsoft Security Code Analysis 拡張機能のインストール

1. 拡張機能が Azure DevOps 組織と共有されたら、Azure DevOps 組織のページ (たとえば、 http://dev.azure.com/contoso) に移動します
2. 右上隅の自分の名前の横にあるショッピング バッグ アイコンをクリックし、[拡張機能の管理] をクリックします 
3. [Microsoft Security Code Analysis 拡張機能] をクリックし、Azure DevOps UI ウィザードを起動してインストールを開始します。
4. 拡張機能をインストールする Azure DevOps 組織をドロップダウンから選択します
5. [インストール] をクリックします。 完了したら、拡張機能の使用に進むことができます

>[!NOTE]
> アクセス権がない場合でも、インストール手順を続行すると、プロセス中に Azure DevOps 組織の管理者にアクセスを要求できるようになります。
>
拡張機能がインストールされると、安全な開発ビルド タスクが表示され、Azure Pipelines に追加できるようになります。

## <a name="adding-specific-build-tasks-to-your-devops-pipeline"></a>DevOps パイプラインへの特定のビルド タスクの追加

1. Azure DevOps 組織のチーム プロジェクトを開きます。
2. **[パイプライン]** の下の **[ビルド]** タブに移動します 
3. 拡張機能のビルド タスクを追加するパイプラインを選択します。 
   - 新規 - **[新規作成]** をクリックし、詳細な手順に従って新しいパイプラインを作成します。
   - 編集 - パイプラインを選択し、 **[編集]** をクリックして、既存のパイプラインの編集を開始します。
4. [+] をクリックして、 **[タスクの追加]** ウィンドウに移動します。
5. 一覧で、または検索ボックスを使用して、追加するビルド タスクを見つけ、 **[追加]** をクリックします。 
6. これで、タスクに必要なパラメーターを指定できるようになりました。
>[!NOTE]
>ファイルまたはディレクトリのパスは、ソース リポジトリのルートからの相対指定であり、出力のフォルダーやファイルを指定するパラメーターは、ビルド エージェントで定義した共通の場所に置き換えられます。

7. 新しいビルドがキューに追加されます。
> [!TIP]
>  - ビルド後に分析を実行するには、ビルドの [ビルド成果物の発行] 手順の後に、Microsoft Security Code Analysis のビルド タスクを配置します。 そうすることで、スタティック分析ツールを実行する前に、ビルドを完了し、結果をポストすることができます。
>  - 安全な開発ビルド タスクの **[エラー時に続行]** オプションは、常にオンにします。 いずれかのツールが失敗した場合でも、他のツールは実行できます。 相互依存関係はありません。
>  - Microsoft Security Code Analysis ビルド タスクは、ツールが正常に実行できなかった場合にのみ失敗します。ただし、ツールがコード内の問題を特定した場合には失敗しません。 **分析後**ビルド タスクを使用して、ツールによってコード内の問題が特定された場合にビルドを中断するように構成できます。
>  - 一部の Azure DevOps ビルド タスクは、"リリース" パイプラインを介して実行される場合、サポートされません。 これは、Azure DevOps の制限です。 リリース パイプライン内から成果物を発行するタスクは、サポートされていません。
>  - パラメーターとして指定できる、Azure DevOps チーム ビルドの定義済み変数の一覧については、[Azure DevOps のビルド変数](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)に関するページを参照してください

## <a name="next-steps"></a>次の手順

ビルド タスクの構成の詳細については、[構成のガイド](security-code-analysis-customize.md)を参照してください。

この拡張機能と提供されるツールについてご不明な点がある場合は、[FAQ ページを参照](security-code-analysis-faq.md)してください。


