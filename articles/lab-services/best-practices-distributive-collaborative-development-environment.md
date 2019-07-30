---
title: Azure DevTest Labs リソースの分散型の共同開発 | Microsoft Docs
description: DevTest Lab リソースを開発する分散型の共同開発環境を設定する際のベスト プラクティスについて説明します。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 8ffc8ed3f84284ff69e9515cba0982790b823a37
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543766"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Azure DevTest Labs リソースの分散型の共同開発のためのベスト プラクティス
分散型の共同開発により、さまざまなチームや個人がコード ベースを開発し、保守することができます。 成功させるには、開発プロセスは情報を作成、共有、統合する能力に左右されます。 この重要な開発原則は、Azure DevTest Labs 内で使用できます。 ラボ内には、企業内のさまざまなラボ間で一般的に分散されているさまざまな種類のリソースがあります。 さまざまな種類のリソースは、次の 2 つの領域に集中しています。

- ラボ内に保存されているリソース (ラボベース)
- [ラボに接続されている外部リポジトリ](devtest-lab-add-artifact-repo.md)に格納されたリソース (コード リポジトリベース)。 

このドキュメントでは、すべてのレベルでカスタマイズと品質を確保しながら、複数のチームにわたるコラボレーションと分散を可能にするいくつかのベスト プラクティスについて説明します。

## <a name="lab-based-resources"></a>ラボベースのリソース

### <a name="custom-virtual-machine-images"></a>カスタムの仮想マシン イメージ
毎晩、ラボにデプロイされるカスタム イメージの共通のソースを取得できます。 詳細については、[イメージ ファクトリ](image-factory-create.md)に関するページを参照してください。    

### <a name="formulas"></a>数式
[数式](devtest-lab-manage-formulas.md)はラボ固有であり、分散のメカニズムはありません。 ラボ メンバーは数式の開発をすべて行います。 

## <a name="code-repository-based-resources"></a>コード リポジトリベースのリソース
コード リポジトリに基づく機能は、成果物と環境という 2 種類あります。 この記事では、リポジトリを最も効果的に設定する機能と方法、使用できる成果物および環境を組織レベルまたはチーム レベルでカスタマイズする機能を可能にするワークフローについて説明します。  このワークフローは、標準の[ソース コード管理のブランチ作成戦略](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)に基づいています。 

### <a name="key-concepts"></a>主要な概念
成果物のソース情報には、メタデータ、スクリプトなどがあります。 環境のソース情報には、PowerShell スクリプト、DSC スクリプト、Zip ファイルなどのサポート ファイルを含むメタデータおよび Resource Manager テンプレートが含まれます。  

### <a name="repository-structure"></a>リポジトリの構造  
ソース コード管理 (SCC) の最も一般的な構成は、ラボで使用されるコード ファイル (Resource Manager テンプレート、メタデータ、およびスクリプト) を格納するための多層構造を設定することです。 具体的には、さまざまなレベルのビジネスによって管理されるリソースを格納するためにさまざまなリポジトリを作成します。   

- 会社全体のリソース。
- 事業単位/部門全体のリソース
- チーム固有のリソース。

これらの各レベルは、マスター ブランチが運用環境品質であることが要求される異なるリポジトリにリンクされます。 各リポジトリの[ブランチ](/azure/devops/repos/git/git-branching-guidance?view=azure-devops)は、それらの特定のリソース (成果物またはテンプレート) の開発用です。 複数のリポジトリと複数のブランチを同時に組織のラボに簡単に接続できるため、この構造は DevTest ラボとの連携に適しています。 同じ名前、説明、および発行者が存在する場合に混乱を避けるために、リポジトリ名がユーザー インターフェイス (UI) に含まれています。
     
次の図は、IT 部門によって管理されている会社リポジトリと、研究開発部門によって管理されている部門リポジトリという 2 つのリポジトリを示しています。

![サンプルの分散型の共同開発環境](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
この階層構造により、マスター ブランチでより高いレベルの品質を維持しながら開発できます。また、ラボに複数のリポジトリが接続されているため、柔軟性が向上します。

## <a name="next-steps"></a>次の手順    
次の記事を参照してください。

- [Azure portal](devtest-lab-add-artifact-repo.md) または [Azure Resource Management テンプレート](add-artifact-repository.md)を使用して、ラボにリポジトリを追加します。
- [DevTest ラボの成果物](devtest-lab-artifact-author.md)
- [DevTest ラボの環境](devtest-lab-create-environment-from-arm.md)。
