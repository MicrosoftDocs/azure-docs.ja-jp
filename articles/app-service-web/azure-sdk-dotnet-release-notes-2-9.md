---
title: "Azure SDK for .NET 2.9 リリース ノート"
description: "Azure SDK for .NET 2.9 リリース ノート"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 71c5a4f1b816fdcfe7ef267c20e6ed42e6cc51e6
ms.openlocfilehash: 3c3fb275a7c980f71a3a30e6875b9515321bad99
ms.lasthandoff: 03/01/2017


---
# <a name="azure-sdk-for-net-29-release-notes"></a>Azure SDK for .NET 2.9 リリース ノート

このトピックには、Azure SDK for .NET のバージョン 2.9 および 2.9.6 のリリース ノートが含まれています。

##<a name="azure-sdk-for-net-296-release-summary"></a>Azure SDK for .NET 2.9.6 リリースの概要

リリース日: 2016 年 11 月 16 日
 
今回のリリースでは、Azure SDK 2.9 に対する重大な変更はありません。 また、既存のクラウド サービス プロジェクトでこの SDK を利用するために必要となるアップグレード プロセスもありません。

### <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 Release Candidate

- Visual Studio 2017 RC では、Azure SDK for .NET のこのリリースが Azure ワークロードに組み込まれています。 Azure の開発に必要なすべてのツールは、今後 Visual Studio 2017 RC の一部になります。 Visual Studio 2015 と Visual Studio 2013 の場合、SDK は WebPI から引き続き使用できます。 Visual Studio 2013 用の Azure SDK for .NET のリリースは、Visual Studio 2017 が最終製品としてリリースされた時点で中止される予定です。 Visual Studio 2017 RC をダウンロードするには、https://www.visualstudio.com/vs/visual-studio-2017-rc/ にアクセスしてください。

### <a name="azure-diagnostics"></a>Azure 診断

- Cloud Services 診断ストレージ接続文字列のトークンで置き換えられるキーを含む部分的な接続文字列のみを格納するように、動作を変更しました。 実際のストレージ キーはユーザー プロファイル フォルダーに格納され、アクセスを制御できるようになりました。 Visual Studio は、ローカルのデバッグおよび公開プロセスのために、ユーザー プロファイル フォルダーからストレージ キーを読み取ります。 
- 上記の変更に対応するために、Visual Studio Online チームは、Azure Cloud Services デプロイ タスク テンプレートを拡張しました。これにより、ユーザーは、継続的インテグレーションおよびデプロイで Azure に発行するときに診断拡張機能を設定するためのストレージ キーを指定できます。
- Azure Diagnostics (WAD) のセキュリティで保護された接続文字列とトークンを保存して、環境全体の構成に関する問題を解決できるようになりました。
 
### <a name="windows-server-2016-virtual-machines"></a>Windows Server 2016 の仮想マシン

- Visual Studio で、OS ファミリ 5 (Windows Server 2016) 仮想マシンにクラウド サービスをデプロイできるようになりました。 既存のクラウド サービスの場合は、設定を変更して新しい OS ファミリを対象とすることができます。 新しいクラウド サービスの作成時に .NET 4.6 以降を使用してサービスを作成することを選択すると、サービスで OS ファミリ 5 が既定で使用されるようになります。  詳細については、[ゲスト OS ファミリ サポート表](https://azure.microsoft.com/en-us/documentation/articles/cloud-services-guestos-update-matrix/)を参照してください。

#### <a name="known-issues"></a>既知の問題

- Azure .NET SDK 2.9.6 では、バージョン 5 より前の OS ファミリに対して、サポートされていない .NET framework (.NET 4.6 など) を使用したプロジェクトのデプロイがブロックされるという制限があります。 回避策は[こちら](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9)です。

 
### <a name="azure-in-role-cache"></a>Azure In-Role Cache 

- Azure In-Role Cache のサポートは、2016 年 11 月 30 日に終了します。 詳細については、[ここ](https://azure.microsoft.com/en-us/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)をクリックしてください。

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Azure Stack 用 Azure Resource Manager テンプレート

- Azure Resource Manager テンプレートのデプロイ ターゲットとして Azure Stack を新たに追加しました。


## <a name="azure-sdk-for-net-29-summary"></a>Azure SDK for .NET 2.9 の概要

## <a name="overview"></a>Overview
このドキュメントには、Azure SDK for .NET 2.9 リリースのリリース ノートが含まれます。 

このリリースの詳細については、「 [Azure SDK 2.9 アナウンスの投稿](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)」をご覧ください。

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Azure SDK 2.9 for Visual Studio 2015 Update 2 と Visual Studio "15" プレビュー
バグの修正:

* REST API クライアント生成で、生成されたコード内でコード生成フォルダーや名前空間の名前が "Unknown Type” として表示される問題が修正されました。
* スケジュールされた Web ジョブで、認証情報がスケジューラのプロビジョニング プロセスに渡されないという問題が修正されました。

新機能:

* App Service のプロビジョニング ダイアログの [サービス] タブで、セカンダリ App Services がサポートされるようになりました。 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Azure Data Lake Tools for Visual Studio 2015 Update 2
アップデートの内容:

* **Azure Data Lake Tools** for Visual Studio が Azure SDK for .NET リリースに統合されました。 Azure SDK をインストールすると、Azure Data Lake Tools が自動的にインストールされます。 
  
    このツールは頻繁に更新されます。最新版は[こちら](http://aka.ms/datalaketool)から取得してください。
* **サーバー エクスプローラー** で U-SQL メタデータ エンティティをすべて表示したり、一部を作成したりできるようになりました。 詳細については、 [このブログ](https://azure.microsoft.com/documentation/services/data-lake-analytics/) をご覧ください。

## <a name="hdinsight-tools"></a>HDInsight ツール
**HDInsight Tools** for Visual Studio で HDInsight Version 3.3 がサポートされるようになりました。これには、Tez グラフの表示やその他の言語の修正が含まれています。

## <a name="azure-resource-manager"></a>Azure リソース マネージャー
このリリースでは、Resource Manager テンプレートの [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) サポートが追加されています。

## <a name="see-also"></a>関連項目
[Azure SDK 2.9 の発表に関するブログ記事](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)


