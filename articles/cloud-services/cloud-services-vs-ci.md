---
title: "Visual Studio Online を使用したクラウド サービスの継続的な配信 | Microsoft Docs"
description: "診断ストレージ キーをサービス構成ファイルに保存せずに、Azure クラウド アプリの継続的な配信を設定する方法について説明します"
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 148b2959-c5db-4e4a-a7e9-fccb252e7e8a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 7e70f92d4d1333ca6cbac5876e5ccbc763bd915c

---
# <a name="securely-save-cloud-services-diagnostics-storage-key-and-setup-continuous-integration-and-deployment-to-azure-using-visual-studio-online"></a>Visual Studio Online を使用した Cloud Services の診断ストレージ キーの安全な保存および継続的インテグレーションと Azure へのデプロイのセットアップ
 これは、最近のオープン ソース プロジェクトでは一般的です。 構成ファイルにアプリケーション シークレットを保存するのは、シークレットがパブリックなソース管理から漏洩してセキュリティの脆弱性が露見するため、もはや安全な方法ではありません。 継続的インテグレーション パイプラインでファイルにシークレットをプレーン テキストとして格納するのも、ビルド サーバーがクラウド環境の共有リソースである可能性があるため、安全ではありません。 この記事では、開発および継続的インテグレーション プロセスにおけるセキュリティ上の懸念を Visual Studio と Visual Studio Online で緩和する方法について説明します。

## <a name="remove-diagnostics-storage-key-secret-in-project-configuration-file"></a>プロジェクト構成ファイルの診断ストレージ キー シークレットの削除
Cloud Services の診断拡張機能には、診断結果を保存するために Azure Storage が必要です。 以前は、ストレージ接続文字列は Cloud Services 構成 (.cscfg) ファイルで指定し、ソース管理にチェックインすることができました。 最新の Azure SDK リリースでは、トークンで置き換えられるキーを含む部分的な接続文字列のみを格納するように、動作を変更しました。 次の手順では、新しい Cloud Services ツールの動作について説明します。

### <a name="1-open-the-role-designer"></a>1.ロール デザイナーを開く
* ロール デザイナーを開くには、Cloud Services ロールをダブルクリックまたは右クリックします

![ロール デザイナーを開く][0]

### <a name="2-under-diagnostics-section-a-new-check-box-dont-remove-storage-key-secret-from-project-is-added"></a>2.[診断] セクションに、新しい [ストレージ キー シークレットをプロジェクト構成 (.cscfg) ファイルから削除しない] チェック ボックスが追加されている
* ローカル ストレージ エミュレーターを使用している場合、このチェック ボックスは無効になります。これは、ローカル接続文字列 (UseDevelopmentStorage=true) には管理するシークレットがないためです。

![ローカル ストレージ エミュレーターの接続文字列はシークレットではない][1]

* 新しいプロジェクトを作成している場合は、このチェック ボックスは既定でオフになります。 これにより、選択したストレージ接続文字列のストレージ キー セクションがトークンに置き換えられます。 トークンの値は、現在のユーザーの AppData Roaming フォルダーの下で見つかります (例: C:\Users\contosouser\AppData\Roaming\Microsoft\CloudService)。

> user\AppData フォルダーは、ユーザーのサインインによってアクセス制御されており、開発のシークレットを格納する安全な場所と見なされていることに注意してください。
> 
> 

![ユーザー プロファイル フォルダーの下に保存されているストレージ キー][2]

### <a name="3-select-a-diagnostics-storage-account"></a>3.診断ストレージ アカウントを選択する
* ストレージ アカウントは、[…] をクリックして開いたダイアログ ボックスで 選択します。 生成されたストレージ接続文字列には、ストレージ アカウント キーが含まれないことに注意してください。
* 例: DefaultEndpointsProtocol=https;AccountName=contosostorage;AccountKey=$(*clouddiagstrg.key*)

### <a name="4----debugging-the-project"></a>4.  プロジェクトをデバッグする
* デバッグを開始するには、Visual Studio で F5 キーを押します。 以前とまったく同じように動作します。
  ![ローカルでのデバッグの開始][3]

### <a name="5-publish-project-from-visual-studio"></a>5.Visual Studio からプロジェクトを発行する
* アプリケーションを Azure に発行するには、[発行] ダイアログを開き、サインイン手順を続行します。

### <a name="6-additional-information"></a>6.追加情報
> 注: ロール デザイナーの [設定] パネルは今のところ変わっていません。 診断のシークレット管理機能を使用する場合は、[構成] タブに移動します。
> 
> 

![設定の追加][4]

> 注: 有効な場合、Application Insights キーはプレーン テキストとして格納されます。 キーはコンテンツのアップロードにのみ使用されます。そのため、機密データが侵害される危険はありません。
> 
> 

## <a name="build-and-publish-a-cloud-services-project-using-visual-studio-online-task-templates"></a>Visual Studio Online のタスク テンプレートを使用した Cloud Services プロジェクトのビルドと発行
* 次の手順では、Visual Studio Online のタスクを使用して Cloud Services プロジェクトの継続的インテグレーションをセットアップする方法を示します。
  ### <a name="1----obtain-a-vso-account"></a>1.  VSO アカウントを取得する
* Visual Studio Online アカウントがまだない場合は、[作成][Visual Studio Online アカウントの作成]します
* Visual Studio Online アカウントで[チーム プロジェクトを作成][チーム プロジェクトの作成]します

### <a name="2----setup-source-control-in-visual-studio"></a>2.  Visual Studio でソース管理をセットアップする
* チーム プロジェクトに接続します

![チーム プロジェクトへの接続][5]

![接続先のチーム プロジェクトの選択][6]

* ソース管理にプロジェクトを追加します

![ソース管理へのプロジェクトの追加][7]

![ソース管理フォルダーへのプロジェクトのマップ][8]

* チーム エクスプローラーからプロジェクトをチェックインします

![ソース管理へのプロジェクトのチェックイン][9]

### <a name="3----configure-build-process"></a>3.  ビルド プロセスを構成する
* チーム プロジェクトを参照し、新しいビルド プロセス テンプレートを追加します

![新しいビルドの追加][10]

* ビルド タスクを選択します

![ビルド タスクの追加][11]

![Visual Studio ビルド タスク テンプレートの選択][12]

* ビルド タスクの入力を編集します。 必要に応じて、ビルド パラメーターをカスタマイズしてください

![ビルド タスクの構成][13]

`/t:Publish /p:TargetProfile=$(targetProfile) /p:DebugType=None /p:SkipInvalidConfigurations=true /p:OutputPath=bin\ /p:PublishDir="$(build.artifactstagingdirectory)\\"`

* ビルド変数を構成します

![ビルド変数の構成][14]

* ビルドの格納場所をアップロードするタスクを追加します

![ビルドの格納場所の発行タスクの選択][15]

![ビルドの格納場所の発行タスクの構成][16]

* ビルドを実行します

![新しいビルドのキューへの配置][17]

![ビルドの概要の表示][18]

* ビルドに成功した場合は、次のような結果が表示されます

![ビルド結果][19]

### <a name="4----configure-release-process"></a>4.  リリース プロセスを構成する
* 新しいリリースを作成します

![新しいリリースの作成][20]

* Azure Cloud Services のデプロイ タスクを選択します

![Azure Cloud Services のデプロイ タスクの選択][21]

* ストレージ アカウント キーはソース管理にチェックインされないため、診断拡張機能を設定するためにシークレット キーを指定する必要があります。 **[新しいサービスを作成するための詳細設定のオプション]** セクションを展開し、**[Diagnostics Storage Account Keys (診断ストレージ アカウント キー)]** パラメーターの入力を編集します。 この入力には、**[RoleName]:$(StorageAccountKey)** という形式のキーと値のペアを複数行指定できます

> 注: 診断ストレージ アカウントのサブスクリプションが Cloud Services アプリケーションを発行するサブスクリプションと同じ場合、デプロイ タスクの入力でキーを入力する必要はありません。デプロイでは、ストレージ情報をサブスクリプションからプログラムで取得します
> 
> 

![Cloud Services のデプロイ タスクの構成][22]

* シークレット ビルド変数を使用して、ストレージ キーを保存します。 変数をシークレットとしてマスクするには、変数の入力の右側にある錠前のアイコンをクリックします

![シークレット ビルド変数へのストレージ キーの保存][23]

* リリースを作成し、プロジェクトを Azure にデプロイします

![新しいリリースの作成][24]

## <a name="next-steps"></a>次のステップ
Azure Cloud Services の診断拡張機能の設定の詳細については、「[PowerShell を使用した Azure Cloud Services での診断の有効化][PowerShell を使用した Azure Cloud Services での診断の有効化]」を参照してください。

[Visual Studio Online アカウントの作成]:https://www.visualstudio.com/team-services/
[チーム プロジェクトの作成]: https://www.visualstudio.com/it-it/docs/setup-admin/team-services/connect-to-visual-studio-team-services
[PowerShell を使用した Azure Cloud Services での診断の有効化]:https://azure.microsoft.com/en-us/documentation/articles/cloud-services-diagnostics-powershell/

[0]: ./media/cloud-services-vs-ci/vs-01.png
[1]: ./media/cloud-services-vs-ci/vs-02.png
[2]: ./media/cloud-services-vs-ci/file-01.png
[3]: ./media/cloud-services-vs-ci/vs-03.png
[4]: ./media/cloud-services-vs-ci/vs-04.png
[5]: ./media/cloud-services-vs-ci/vs-05.png
[6]: ./media/cloud-services-vs-ci/vs-06.png
[7]: ./media/cloud-services-vs-ci/vs-07.png
[8]: ./media/cloud-services-vs-ci/vs-08.png
[9]: ./media/cloud-services-vs-ci/vs-09.png
[10]: ./media/cloud-services-vs-ci/vso-01.png
[11]: ./media/cloud-services-vs-ci/vso-02.png
[12]: ./media/cloud-services-vs-ci/vso-03.png
[13]: ./media/cloud-services-vs-ci/vso-04.png
[14]: ./media/cloud-services-vs-ci/vso-05.png
[15]: ./media/cloud-services-vs-ci/vso-06.png
[16]: ./media/cloud-services-vs-ci/vso-07.png
[17]: ./media/cloud-services-vs-ci/vso-08.png
[18]: ./media/cloud-services-vs-ci/vso-09.png
[19]: ./media/cloud-services-vs-ci/vso-10.png
[20]: ./media/cloud-services-vs-ci/vso-11.png
[21]: ./media/cloud-services-vs-ci/vso-12.png
[22]: ./media/cloud-services-vs-ci/vso-13.png
[23]: ./media/cloud-services-vs-ci/vso-14.png
[24]: ./media/cloud-services-vs-ci/vso-15.png



<!--HONumber=Nov16_HO3-->


