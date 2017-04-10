---
title: "Azure SDK for .NET 2.8 リリース ノート"
description: "Azure SDK for .NET 2.8 リリース ノート"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: de7207ff-ba4f-4008-9141-8742fcaa3254
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 81772fb5801ec620d1c1996509a275e4bb29fb12


---
# <a name="azure-sdk-for-net-28-281-and-282"></a>Azure SDK for .NET 2.8、2.8.1、2.8.2
## <a name="overview"></a>概要
この記事には、Azure SDK for .NET 2.8、2.8.1、2.8.2 リリースのリリース ノート (既知の問題と重大な変更を含む) が記載されています。 

このリリースの新機能とこのリリースで行われた更新の一覧については、 [Visual Studio 2013 および Visual Studio 2015 用 Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) の発表に関するブログ記事をご覧ください。 

## <a name="azure-sdk-for-net-28"></a>Azure SDK for .NET 2.8
### <a name="download-azure-sdk-for-net-28"></a>Azure SDK for .NET 2.8 のダウンロード
[Visual Studio 2015 用 Azure SDK for .NET 2.8](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Visual Studio 2013 用 Azure SDK for .NET 2.8](http://go.microsoft.com/fwlink/?LinkId=699287)

### <a name="net-452-support"></a>.NET 4.5.2 のサポート
#### <a name="known-issues"></a>既知の問題
Azure .NET SDK 2.8 では、.NET 4.5.2 クラウド サービス パッケージを作成できます。 ただし、.NET 4.5.2 フレームワークは、2016 年 1 月のゲスト OS のリリースまで既定のゲスト OS イメージにはインストールされません。 それまでは、.NET 4.5.2 フレームワークは別のゲスト OS リリース バージョン (November 2015-02) で利用できます。 イメージのリリース日を追跡した「[Azure ゲスト OS リリースと SDK の互換性対応表](../cloud-services/cloud-services-guestos-update-matrix.md)」ページをご覧ください。  November 2015-02 イメージがリリースされたら、クラウド サービス構成ファイル (.cscfg) を更新することでこのイメージを使用できます。 このサービス構成ファイルで、ServiceConfiguration 要素の osVersion 属性を "WA-GUEST-OS-4.26_201511-02" という文字列に設定します。 このイメージを使用するようにした場合、ゲスト OS に対する自動更新プログラムを取得できなくなります。 自動更新プログラムを取得するには、osVersion を "*" に設定する必要があります。.NET 4.5.2 は 2016 年 1 月の自動更新プログラムによってのみ利用できるようになります。

### <a name="azure-data-factory"></a>Azure Data Factory
#### <a name="known-issues"></a>既知の問題
コンピューターにインストールされている Azure PowerShell のバージョンが 0.9.8 より後の場合、サンプル データを含む **Data Factory テンプレート** プロジェクトの作成時に、Azure PowerShell スクリプトが失敗することがあります。

この種のプロジェクトを正常に作成するには、 [Azure PowerShell バージョン 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi)をインストールする必要があります。

### <a name="azure-resource-manager-tools"></a>Azure リソース マネージャー ツール
#### <a name="breaking-changes"></a>重大な変更
このリリースでは、Azure リソース グループ プロジェクトで提供される PowerShell スクリプトが更新され、新しい Azure PowerShell コマンドレット バージョン 1.0 で動作するようになりました。  2.8 より前のバージョンの SDK を使用している場合、この新しいスクリプトは Visual Studio では動作しません。  

2.8 SDK を使用している場合、以前のバージョンの SDK で作成されたプロジェクトのスクリプトは、Visual Studio 内から実行できません。  すべてのスクリプトは、適切なバージョンの Azure PowerShell コマンドレットを使用して、Visual Studio の外部で引き続き動作します。  

2.8 SDK には、Azure PowerShell コマンドレットのバージョン 1.0 が必要です。  他のすべてのバージョンの SDK には、Azure PowerShell コマンドレットのバージョン 0.9.8 が必要です。  詳細については、 [このブログ](http://go.microsoft.com/fwlink/?LinkID=623011) をご覧ください。

### <a name="web-tools-extensions"></a>Web ツールの拡張機能
#### <a name="known-issues"></a>既知の問題
以下の既知の問題には、次のリリースで対処する予定です。

* App Service に関連するクラウドおよびサーバー エクスプローラーの非運用環境向け (Azure China や Azure Stack の顧客など) のジェスチャは機能しません。 これらの影響を受ける地域の顧客の場合、Azure ポータルから発行プロファイルをダウンロードすると、発行機能が有効になります。 Azure China と Azure Stack の顧客のために、今後のリリースで、"デバッガーのアタッチ" や "ストリーミング ログの表示" などのジェスチャが修正される予定です。 
* デプロイ先の App Insights インスタンスが米国東部以外のリージョンにある場合、App Service の作成時にエラーが発生することがあります。 このようなシナリオでは、ポータルで App Service を作成し、発行プロファイルをダウンロードすると、発行シナリオが有効になります。 

### <a name="azure-hdinsight-tools"></a>Azure HDInsight ツール
#### <a name="new-updates"></a>新しい更新プログラム
* ほぼオーバーヘッドなしで、HiveServer2 を使用してクラスターで Hive クエリを実行し、ジョブのログをリアルタイムで確認できます。
* 新しい Hive タスク実行ビューを使用して、ジョブをさらに詳しく調べ、詳細を確認し、潜在的な問題を特定できます。

詳細については、 [Visual Studio 2013 および Visual Studio 2015 用 Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)に関するブログ記事をご覧ください。 

## <a name="azure-sdk-for-net-281"></a>Azure SDK for .NET 2.8.1
### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 および Visual Studio 2015 の既知の問題
1. スロットへの WebJob 発行がトリガーされると、エラーが発生し、スケジュールは設定されませんが、WebJob が Azure にプッシュされます。 スケジュールされたジョブを必要としているお客様は、Azure ポータルを使用して WebJob のスケジュールを設定できます。 
2. Python のお客様には、デバッガーの問題が発生することがあります。 サービス チームがこの修正プログラムのロールアウトを進めていますが、影響を受けたお客様は、フォーラム、またはお知らせブログかリリース ノートのコメント セクションでマイクロソフトにお知らせください。 
3. 一部のリージョン (インド南部など) のお客様には、App Service のプロビジョニング エラーが発生します。 これはポータルと一致しており、この問題が発生したお客様は、Azure ポータルを使用してこれらの geo リージョンに発行するためのアクセスを要求できます。 これらのリージョンへのアクセスを要求したら、Azure ポータルのプロビジョニングが機能するようになります。 

## <a name="azure-sdk-for-net-282"></a>Azure SDK for .NET 2.8.2
2.8.2 のツールのインストール後、次のような問題が発生する場合があります。         

* Windows 10 を使用していて、Internet Explorer をインストールしていない場合は、"Internet Explorer が見つかりませんでした" というエラーが表示されます。
  この問題を解決するには、[Windows コンポーネントの追加と削除] ダイアログを使用して Internet Explorer をインストールしてください。

この問題が解決されない場合は、"気に入った機能の報告" 機能を使用して問題を報告してください。

詳細については、 [この投稿](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) を参照してください。

## <a name="other-updates"></a>他の更新プログラム
他の更新プログラムについては、「 [Azure SDK 2.8 アナウンスの投稿](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)」を参照してください。

## <a name="also-see"></a>関連トピック
[Azure SDK 2.8 の発表に関するブログ記事](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Azure SDK for .NET および API のサポートと提供終了に関する情報](https://msdn.microsoft.com/library/azure/dn479282.aspx)




<!--HONumber=Dec16_HO2-->


