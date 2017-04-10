---
title: "Azure のコマンド ライン ビルド | Microsoft Docs"
description: "Azure のコマンド ライン ビルド"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 5add8703b7b16ba8d9dc49f42f5e71b195c46653
ms.lasthandoff: 03/27/2017


---
# <a name="building-azure-projects-from-the-command-line"></a>コマンド ラインからの Azure プロジェクトのビルド
Microsoft Build Engine (MSBuild) を使用すると、Visual Studio がインストールされていないビルド ラボ環境で製品のビルドを構築できます。 MSBuild は、拡張可能で Microsoft が完全にサポートしている XML 形式をプロジェクト ファイルに使用します。 MSBuild ファイル形式を使用すると、1 つまたは複数のプラットフォームや構成でビルドが必要な項目を記述できます。

MSBuild はコマンド ラインで実行することもできます。このトピックでは、その方法について説明します。 コマンド ラインでプロパティを設定すると、プロジェクトの特定の構成を作成できます。 同様に、MSBuild がビルドするターゲットを定義することもできます。 コマンド ライン パラメーターおよび MSBuild の詳細については、「[MSBuild Command Line Reference (MSBuild コマンド ライン リファレンス)](https://msdn.microsoft.com/library/ms164311.aspx)」を参照してください。

## <a name="msbuild-parameters"></a>MSBuild パラメーター
パッケージを作成する最も簡単な方法は、 `/t:Publish` オプションを指定して MSBuild を実行することです。 既定では、プロジェクトのルート フォルダーに対して ディレクトリが作成されます (たとえば、`<ProjectDirectory>\bin\Configuration\app.publish\`)。 Azure プロジェクトをビルドすると、パッケージ ファイルとそれに対応する構成ファイルの 2 つのファイルが生成されます。

* パッケージ ファイル (`project.cspkg`)
* 構成ファイル (`ServiceConfiguration.TargetProfile.cscfg`)

既定では、各 Azure プロジェクトにはローカル (デバッグ) ビルド用に 1 つ、クラウド (ステージングまたは運用) ビルド用に 1 つのサービス構成ファイルが含まれています。 サービス構成ファイルは必要に応じて追加または削除できます。 Visual Studio 内でパッケージをビルドするときは、パッケージに含めるサービス構成ファイルを指定するよう求められます。 MSBuild を使用してパッケージをビルドする場合、既定でローカル サービス構成ファイルが含められます。 別のサービス構成ファイルを含めるには、MSBuild コマンドの `TargetProfile` プロパティ を設定します (`MSBuild /t:Publish /p:TargetProfile=ProfileName`)。

パッケージと構成ファイルの格納に別のディレクトリを使用する場合は、`/p:PublishDir=Directory\` オプションを使用してパスを設定します。その際、末尾に円記号の区切り記号を含めます。

## <a name="next-steps"></a>次のステップ
パッケージは、ビルド後に Azure にデプロイできます。 このプロセスを自動化する方法を説明したチュートリアルについては、「[Azure でのクラウド サービスの継続的な配信](./cloud-services/cloud-services-dotnet-continuous-delivery.md)」を参照してください。


