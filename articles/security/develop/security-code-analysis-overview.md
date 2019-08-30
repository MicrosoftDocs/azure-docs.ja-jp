---
title: Microsoft Azure Security Code Analysis のドキュメントの概要
description: この記事は、Security Code Analysis 拡張機能の概要です
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
ms.openlocfilehash: 283d63bafc583f2ac9da3294644aaebd17d7c950
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718170"
---
# <a name="about-microsoft-security-code-analysis"></a>Microsoft Security Code Analysis について

**Microsoft Security Code Analysis 拡張機能**により、Microsoft の [Secure Development Lifecycle (SDL)](https://www.microsoft.com/securityengineering/sdl/practices) のエキスパートが推奨するように、チームは Azure DevOps CI/CD パイプラインにセキュリティ コード分析をシームレスに統合できます。 セキュリティは、さまざまなツールを実行する複雑さをなくす一貫した UX によって簡素化されます。 NuGet ベースでのツールの配布により、チームはツールのインストールまたは更新を管理する必要がなくなります。 コマンド ラインや基本的なビルド タスク インターフェイスを使用すると、経験豊富なツールの専門家から一般の開発者まで、すべてのユーザーが必要に応じて好きなようにツールを制御できます。 また、チームは、保持のためのログの発行、開発者向けの実用的なレポートの生成、回帰でのビルド中断の構成など、強力な後処理機能を活用することもできます。

## <a name="why-microsoft-security-code-analysis"></a>Microsoft Security Code Analysis を選ぶ理由

### <a name="security-simplified"></a>簡素化されたセキュリティ

Azure DevOps パイプラインにセキュリティ コード分析ツールを追加するのは、新しいタスクを追加するのと同じくらい簡単です。 それらをカスタマイズしても、既定のままにしてもかまいません。 タスクは、DevOps パイプラインの一環として実行され、あらゆる種類の検出結果が詳述されたログを生成します。

### <a name="clean-builds"></a>クリーン ビルド

ツールによってレポートされた初期の問題に対処した後、新しい問題の発生時にビルドを中断するようこの拡張機能を構成できます。 すべての pull request に対して継続的インテグレーション ビルドを設定することは、これほど簡単ではありませんでした。

### <a name="set-it-and-forget-it"></a>設定したら放っておく

ビルドのタスクとツールは、常に最新の状態に保つよう設定できます (それが既定です)。 ツールの更新バージョンがある場合、それをダウンロードしてインストールする必要はありません。この拡張機能によって自動的に処理されます。 

>>>
### <a name="under-the-hood"></a>しくみ

Microsoft Security Code Analysis 拡張機能のビルド タスクによって、以下を行う際の複雑さが解消されます。
  - セキュリティ スタティック分析ツールを実行する。
  - ログ ファイルの結果を処理して、概要レポートを作成するかビルドを中断する。
>>>

## <a name="security-code-analysis-toolset"></a>Security Code Analysis のツールセット

Microsoft Security Code Analysis 拡張機能により、重要な分析ツールの最新バージョンをすぐに利用できるようになります。 この拡張機能には、Microsoft の内部ツールとオープンソースのツールの両方が含まれています。 対応するビルド タスクを使用してパイプラインを構成して実行すると、ツールは、クラウドでホストされているエージェントに自動的にダウンロードされます。 現在、この拡張機能で利用可能なツールのセットを次に示します。 追加については今後の情報をお待ちください。また、追加可能なツールについてのご提案をお寄せください。

### <a name="anti-malware-scanner"></a>Anti-Malware Scanner

Anti-Malware Scanner ビルド タスクが Microsoft Security Code Analysis 拡張機能に含まれるようになりました。 これは、Windows Defender が既にインストールされているビルド エージェント上で実行する必要があります。 詳細については、[Defender の Web サイト](https://aka.ms/defender)を参照してください。 

### <a name="binskim"></a>BinSkim

BinSkim は、コンパイラやリンカーの設定とその他のセキュリティ関連のバイナリ特性を検証する、移植可能な実行可能ファイル (PE) の軽量スキャナーです。 このビルド タスクでは、BinSkim.exe アプリケーションのコマンド ライン ラッパーが提供されます。 BinSkim はオープンソース ツールです。詳細については、[GitHub の BinSkim](https://github.com/Microsoft/binskim) に関するページを参照してください。

### <a name="credential-scanner"></a>Credential Scanner

ソース コードに格納されているパスワードやその他のシークレットは、現在、重大な問題になっています。 Credential Scanner は、ソース コードやビルド出力内で資格情報、シークレット、証明書などの機密コンテンツを検出する、自社開発のスタティック分析ツールです。

### <a name="microsoft-security-risk-detection"></a>Microsoft Security Risk Detection

Security Risk Detection は、Microsoft 独自のクラウドベースのファジー テスト サービスで、ソフトウェア内の悪用可能なセキュリティ バグを特定します。 このサービスは、個別のオンボード プロセスを必要とします。 詳細については、[docs.microsoft.com の MSRD](https://docs.microsoft.com/security-risk-detection/) に関するページを参照してください

### <a name="roslyn-analyzers"></a>Roslyn アナライザー

マネージド コード (C# および VB) を分析するための、Microsoft のコンパイラに統合されたスタティック分析ツール。 詳細については、[docs.microsoft.com の Roslyn アナライザー](https://docs.microsoft.com/dotnet/standard/analyzers/)に関するページを参照してください。

### <a name="tslint"></a>TSLint

TSLint は拡張可能なスタティック分析ツールで、TypeScript コードに読みやすさ、保守のしやすさ、機能に関するエラーがないかどうかをチェックします。 これは、最新のエディターやビルド システムで広くサポートされており、独自の lint ルール、構成、およびフォーマッタを使用してカスタマイズできます。 TSLint はオープンソース ツールです。詳細については、[GitHub の TSLint](https://github.com/palantir/tslint) に関するページを参照してください。

## <a name="analysis-and-post-processing-of-results"></a>結果の分析と後処理

Microsoft Security Code Analysis 拡張機能には、セキュリティ ツールのタスクによって検出された結果を処理および分析する際に役立つ、3 つの後処理タスクもあります。 これらは、通常、パイプラインで他のすべてのツール タスクの後に追加されます。

### <a name="publish-security-analysis-logs"></a>セキュリティ分析ログの発行
"セキュリティ分析ログの発行" ビルド タスクは、調査およびフォローアップのために、ビルド中のセキュリティ ツールの実行のログ ファイルを保持します。

これらは、Azure サーバーの成果物に (ZIP ファイルとして) 発行するか、プライベート ビルド エージェントからアクセス可能なファイル共有にコピーすることができます。

### <a name="security-report"></a>セキュリティ レポート
"セキュリティ レポート" ビルド タスクは、ビルド中のセキュリティ ツールの実行によって作成されたログ ファイルを解析し、分析ツールで検出されたすべての問題を含む 1 つの概要レポート ファイルを作成します。

このタスクは、特定のツールまたはすべてのツールの結果をレポートするよう構成できます。また、どのレベルの問題 (エラー、またはエラーと警告) をレポートするかを選択することもできます。

### <a name="post-analysis-build-break"></a>分析後 (ビルドの中断)
"分析後" ビルド タスクを使用すると、ユーザーは 1 つ以上の分析ツールによってコードでの検出結果や問題がレポートされた場合にビルドの中断を挿入してビルドを失敗させることができます。

このタスクは、特定のツールまたはすべてのツールで検出された問題に対して、また、検出された問題の重大度 (エラー、またはエラーと警告) に基づいて、ビルドを中断するよう構成できます。

>[!NOTE]
>仕様により、個々のビルド タスクは、ツールが正常に完了する限り成功します。検出結果があるかどうかにかかわらず、ビルドは完了するまで実行でき、すべてのツールの実行が許可されます。

## <a name="next-steps"></a>次の手順

Security Code Analysis のオンボードとインストールの手順については、[オンボードとインストールのガイド](security-code-analysis-onboard.md)を参照してください。

ビルド タスクの構成の詳細については、[構成のガイド](security-code-analysis-customize.md)を参照してください。

この拡張機能と提供されるツールについてご不明な点がある場合は、[FAQ ページを参照](security-code-analysis-faq.md)してください。