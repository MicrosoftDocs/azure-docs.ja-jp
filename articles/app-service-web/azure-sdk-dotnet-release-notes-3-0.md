---
title: "Azure SDK for .NET 3.0 リリース ノート | Microsoft Docs"
description: "Azure SDK for .NET 3.0 リリース ノート"
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
ms.date: 03/07/2017
ms.author: juliako;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: dea4a174aaf3727d66e9d69d32d433ff24e0d06d
ms.lasthandoff: 03/22/2017


---
# <a name="azure-sdk-for-net-30-release-notes"></a>Azure SDK for .NET 3.0 リリース ノート

このトピックには、Azure SDK for .NET のバージョン 3.0 のリリース ノートが含まれています。

##<a name="azure-sdk-for-net-30-release-summary"></a>Azure SDK for .NET 3.0 リリースの概要

リリース日: 03/07/2017
 
今回のリリースでは、Azure SDK 3.0 に対する重大な変更はありません。 また、既存のクラウド サービス プロジェクトでこの SDK を利用するために必要となるアップグレード プロセスもありません。 アップグレード プロセスなしで Azure SDK 3.0 を使用できるようにするために、Azure SDK 3.0 は Azure SDK 2.9 と同じディレクトリにインストールされます。 ほとんどのコンポーネントでは、ビルド番号の更新以外、2.9 からの大きなバージョン変更はありませんでした。

## <a name="visual-studio-2017-rtw"></a>Visual Studio 2017 RTW

- Visual Studio 2017 では、Azure SDK for .NET のこのリリースが Azure ワークロードに組み込まれています。 Azure の開発に必要なすべてのツールは、今後 Visual Studio 2017 の一部になります。 Visual Studio 2015 の場合、SDK は WebPI から引き続き使用できます。 Visual Studio 2017 がリリースされたため、Visual Studio 2013 用の Azure SDK .NET リリースは廃止されました。

### <a name="azure-diagnostics"></a>Azure 診断

- Cloud Services 診断ストレージ接続文字列のトークンで置き換えられるキーを含む部分的な接続文字列のみを格納するように、動作を変更しました。 実際のストレージ キーはユーザー プロファイル フォルダーに格納され、アクセスを制御できるようになりました。 Visual Studio は、ローカルのデバッグおよび公開プロセスのために、ユーザー プロファイル フォルダーからストレージ キーを読み取ります。 
- 上記の変更に対応するために、Visual Studio Online チームは、Azure Cloud Services デプロイ タスク テンプレートを拡張しました。これにより、ユーザーは、継続的インテグレーションおよびデプロイで Azure に発行するときに診断拡張機能を設定するためのストレージ キーを指定できます。
- Azure Diagnostics (WAD) のセキュリティで保護された接続文字列とトークンを保存して、環境全体の構成に関する問題を解決できるようになりました。
 
### <a name="windows-server-2016-virtual-machines"></a>Windows Server 2016 の仮想マシン

- Visual Studio で、OS ファミリ 5 (Windows Server 2016) 仮想マシンにクラウド サービスをデプロイできるようになりました。 既存のクラウド サービスの場合は、設定を変更して新しい OS ファミリを対象とすることができます。 新しいクラウド サービスの作成時に .NET 4.6 以降を使用してサービスを作成することを選択すると、サービスで OS ファミリ 5 が既定で使用されるようになります。  詳細については、[ゲスト OS ファミリ サポート表](../cloud-services/cloud-services-guestos-update-matrix.md)を参照してください。

### <a name="known-issues"></a>既知の問題

- Azure .NET SDK 3.0 では、Visual Studio 2015 との並列構成の Visual Studio 2017 を削除したときに問題が発生します。  Azure SDK を Visual Studio 2015 用にインストールした場合、Microsoft Azure ストレージ エミュレーターおよび Microsoft Azure コンピューティング エミュレーターは、Visual Studio 2017 をアンインストールすると削除されます。  このため、Visual Studio 2015 で新しいクラウド サービス プロジェクトを作成およびデバッグするとエラーが生成されます。 この問題を解決するには、Web Platform Installer から Azure SDK を再インストールします。  この問題は、今後の Visual Studio 2017 アップデートで解決されます。  に関するセクションをご覧ください。

 
### <a name="azure-in-role-cache"></a>Azure In-Role Cache 

- Azure In-Role Cache のサポートは、2016 年 11 月 30 日に終了します。 詳細については、[ここ](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)をクリックしてください。





