---
title: Microsoft Security Code Analysis のドキュメントの概要
description: この記事では、Microsoft Security Code Analysis 拡張機能の概要について説明します。
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
ms.openlocfilehash: a7ab908673f60a651db48ae0485592a362741b6d
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242315"
---
# <a name="about-microsoft-security-code-analysis"></a>Microsoft Security Code Analysis について

Microsoft Security Code Analysis 拡張機能を使用すると、チームは Azure DevOps の継続的インテグレーションと継続的配信 (CI/CD) パイプラインにセキュリティ コード分析を追加できます。 これは Microsoft の [Microsoft セキュリティ開発ライフサイクル (SDL)](https://www.microsoft.com/securityengineering/sdl/practices) の専門家が推奨する分析です。

一貫性のある UX のために、実行しているツールの複雑さは隠されて、セキュリティが単純になります。 ツールの NuGet ベースの配信により、チームはツールのインストールまたは更新を管理する必要がなくなります。 すべてのユーザーが、ビルド タスクのコマンド ラインと基本インターフェイスの両方を使用して必要に応じてツールを適切に制御できます。

チームでは、次のように強力な後処理機能を使用することもできます。

- 保存用にログを発行する。
- 開発者向けの実用的なレポートを生成する。
- 回帰テストでビルドの中断を構成する。

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Microsoft Security Code Analysis を使用する理由

### <a name="security-simplified"></a>簡素化されたセキュリティ

Azure DevOps パイプラインに Microsoft Security Code Analysis ツールを追加するのは、新しいタスクを追加するのと同じくらい簡単です。 タスクをカスタマイズするか、既定の動作を使用します。 タスクは Azure DevOps パイプラインの一部として実行され、さまざまな種類の結果を詳細に示すログが生成されます。

### <a name="clean-builds"></a>クリーン ビルド

ツールによってレポートされた初期の問題に対処した後、新しい問題の発生時にビルドを中断するようこの拡張機能を構成できます。 すべてのプル要求に対して継続的インテグレーション ビルドを設定することは簡単です。

### <a name="set-it-and-forget-it"></a>設定したら放っておく

既定では、ビルド タスクとツールは最新の状態が維持されます。 更新されたバージョンのツールがあっても、ダウンロードしてインストールする必要はありません。 拡張機能によって、自動的に更新が行われます。

### <a name="under-the-hood"></a>コネクタの性能

拡張機能のビルド タスクによって、次のような複雑さが隠されます。
  - セキュリティ スタティック分析ツールを実行する。
  - ログ ファイルの結果を処理して、概要レポートを作成するかビルドを中断する。

## <a name="microsoft-security-code-analysis-tool-set"></a>Microsoft Security Code Analysis ツール セット

Microsoft Security Code Analysis 拡張機能により、重要な分析ツールの最新バージョンをすぐに利用できるようになります。 拡張機能には、Microsoft が管理するツールとオープンソース ツールの両方が含まれています。

対応するビルド タスクを使用してパイプラインを構成して実行すると、これらのツールはクラウドでホストされるエージェントに自動的にダウンロードされます。

このセクションでは、拡張機能で現在使用できる一連のツールについて説明します。 今後もツールが追加されますのでお待ちください。 また、追加を希望するツールについてご意見をお寄せください。

### <a name="anti-malware-scanner"></a>Anti-Malware Scanner

Anti-Malware Scanner ビルド タスクが Microsoft Security Code Analysis 拡張機能に含まれるようになりました。 このタスクは、Windows Defender が既にインストールされているビルド エージェント上で実行する必要があります。 詳細については、[Windows Defender の Web サイト](https://aka.ms/defender)を参照してください。

### <a name="binskim"></a>BinSkim

BinSkim は、コンパイラの設定、リンカーの設定、およびバイナリ ファイルのその他のセキュリティ関連の特性を検証する、ポータブル実行可能 (PE) 形式の軽量なスキャナーです。 このビルド タスクでは、binskim.exe コンソール アプリケーションのコマンド ライン ラッパーが提供されます。 BinSkim はオープンソースのツールです。 詳細については、[GitHub 上の BinSkim](https://github.com/Microsoft/binskim) を参照してください。

### <a name="credential-scanner"></a>Credential Scanner

ソース コードに格納されているパスワードやその他のシークレットは重要な問題です。 Credential Scanner は、この問題を解決するために役立つ独自のスタティック分析ツールです。 このツールによって、ソース コードとビルド出力に含まれる資格情報、シークレット、証明書、およびその他の機密コンテンツが検出されます。

### <a name="microsoft-security-risk-detection"></a>Microsoft Security Risk Detection

Microsoft Security Risk Detection (MSRD) は、ファジー テストのためのクラウドベースのサービスです。 ソフトウェアに含まれる悪用可能なセキュリティ バグが識別されます。 このサービスは、個別のオンボード プロセスを必要とします。 詳細については、[MSRD Developer Center](https://docs.microsoft.com/security-risk-detection/) を参照してください。

### <a name="roslyn-analyzers"></a>Roslyn アナライザー

Roslyn アナライザーは、マネージド C# コードと Visual Basic コードを静的に分析するための Microsoft のコンパイラ統合ツールです。 詳細については、「[Roslyn ベースのアナライザー](https://docs.microsoft.com/dotnet/standard/analyzers/)」を参照してください。

### <a name="tslint"></a>TSLint

TSLint は拡張可能なスタティック分析ツールで、TypeScript コードに読みやすさ、保守のしやすさ、機能のエラーがないかどうかをチェックします。 最新のエディターやビルド システムで広くサポートされています。 独自の lint のルール、構成、およびフォーマッタを使用してカスタマイズできます。 TSLint はオープンソースのツールです。 詳細については、[GitHub 上の TSLint](https://github.com/palantir/tslint) を参照してください。

## <a name="analysis-and-post-processing-of-results"></a>結果の分析と後処理

Microsoft Security Code Analysis 拡張機能には、3 つの後処理タスクもあります。 これらのタスクは、セキュリティツールのタスクによって検出された結果を分析するために役立ちます。 これらのタスクをパイプラインに追加すると、通常、他のすべてのツール タスク後に実行されます。

### <a name="publish-security-analysis-logs"></a>セキュリティ分析ログの発行

セキュリティ分析ログの発行ビルド タスクでは、ビルド中に実行されるセキュリティ ツールのログ ファイルが保持されます。 これらのログを確認して、調査とフォローアップを行うことができます。

ログ ファイルは .zip ファイル形式で Azure Artifacts に発行できます。 プライベート ビルド エージェントからアクセスできるファイル共有にコピーすることもできます。

### <a name="security-report"></a>セキュリティ レポート

セキュリティ レポートのビルド タスクでは、ログ ファイルが解析されます。 これらのファイルは、ビルド時に実行されるセキュリティ ツールによって作成されます。 その後、ビルド タスクによって 1 つの概要レポート ファイルが作成されます。 このファイルには、分析ツールで検出されたすべての問題が示されます。

このタスクは、特定のツールまたはすべてのツールの結果をレポートするように構成することができます。 また、エラーのみ、エラーと警告の両方など、報告する問題のレベルを選択することもできます。

### <a name="post-analysis-build-break"></a>分析後 (ビルドの中断)

分析後のビルド タスクを使用して、意図的にビルドの失敗を引き起こすビルドの中断を挿入できます。 1 つ以上の分析ツールによってコードの問題が報告された場合は、ビルドの中断を挿入します。

このタスクは、特定のツールまたはすべてのツールによって検出された問題のビルドを中断するように構成することができます。 また、エラーや警告など、検出された問題の重大度に基づいて構成することもできます。

>[!NOTE]
>タスクが正常に完了する場合、設計上、各ビルド タスクは成功です。 これは、ツールによって問題が検出されてもされなくても真実であり、すべてのツールの実行を許可すればビルドは最後まで実行できます。

## <a name="next-steps"></a>次の手順

Microsoft Security Code Analysis をオンボードしてインストールする方法については、[オンボードとインストールのガイド](security-code-analysis-onboard.md)を参照してください。

ビルド タスクの構成の詳細については、[構成のガイド](security-code-analysis-customize.md)を参照してください。

この拡張機能と提供されるツールについてご不明な点がある場合は、[FAQ ページ](security-code-analysis-faq.md)を参照してください。
