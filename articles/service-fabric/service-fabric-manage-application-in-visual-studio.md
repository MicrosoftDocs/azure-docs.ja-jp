---
title: "Visual Studio でアプリケーションを管理する | Microsoft Docs"
description: "Visual Studio を利用し、Service Fabric のアプリケーションとサービスを作成、開発、パッケージ化、デプロイ、デバッグします。"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: seanmck;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 60d440c75d6352d5e65e0158e439df9db2315ecd
ms.openlocfilehash: 70c393f1185844bb26ff1f89cb69cb06b51fc155


---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Visual Studio を利用すれば、Service Fabric アプリケーションの記述と管理が簡単になりまする
Azure Service Fabric アプリケーションとサービスを Visual Studio で管理できます。 [開発環境のセットアップ](service-fabric-get-started.md)が済んだら、Visual Studio を使用して、Service Fabric アプリケーションの作成、サービスの追加、ローカル開発クラスターでのアプリケーションのパッケージ化、登録、およびデプロイができます。

## <a name="deploy-your-service-fabric-application"></a>Service Fabric アプリケーションのデプロイ
アプリケーションのデプロイでは、既定で、次の手順が 1 つの単純な操作にまとめられています。

1. アプリケーション パッケージの作成
2. アプリケーション パッケージのイメージ ストアへのアップロード
3. アプリケーションの種類の登録
4. 実行している任意のアプリケーション インスタンスの削除
5. 新しいアプリケーション インスタンスの作成

Visual Studio では、 **[F5]** キーを押してもアプリケーションはデプロイされ、すべてのアプリケーション インスタンスにデバッガーがアタッチされます。 **[Ctrl + F5]** キーを押してデバッグなしでアプリケーションをデプロイするか、発行プロファイルを使用してローカルまたはリモート クラスターにアプリケーションを発行できます。 詳細については、「 [Visual Studio を使用してリモート クラスターにアプリケーションを発行する](service-fabric-publish-app-remote-cluster.md)」を参照してください。

### <a name="application-debug-mode"></a>アプリケーション デバッグ モード
既定では、デバッグを停止したとき、または (デバッガーをアタッチせずにアプリケーションをデプロイした場合に) アプリケーションを再デプロイしたときに、Visual Studio ではアプリケーションの種類の既存のインスタンスが削除されます。 その場合、アプリケーションのデータはすべて削除されます。 ローカルでデバッグしている間、アプリケーションの新しいバージョンをテストする際に、場合によっては既に作成したデータを保持する必要があります。または、アプリケーションを実行し続けるか、以降のデバッグセッションでアプリケーションをアップグレードする必要があります。 Visual Studio Service Fabric Tools には、**Application Debug Mode (アプリケーション デバッグ モード)** というプロパティが用意されていて、**F5** キーを押したときにアプリケーションをアンインストールするか、デバッグ セッションの終了後もアプリケーションを維持するか、以降のデバッグ セッションで、アプリケーションを削除して再デプロイするのではなく、アップグレードできるようにするかを制御できます。

#### <a name="to-set-the-application-debug-mode-property"></a>Application Debug Mode (アプリケーション デバッグ モード) プロパティを設定するには
1. アプリケーション プロジェクトのショートカット メニューで、**[プロパティ]** を選択します (または **[F4]** キーを押します)。
2. **[プロパティ]** ウィンドウで **[アプリケーション デバッグ モード]** プロパティを設定します。

    ![Set Application Debug Mode Property][debugmodeproperty]

**[アプリケーション デバッグ モード]** では、次のオプションを使用できます。

1. **自動アップグレード**: デバッグ セッションが終了しても、アプリケーションは実行されたままになります。 次に **F5** キーを押したときに、そのデプロイは、監視なしの自動モードを使用したアップグレードとして扱われ、アプリケーションは、日付文字列が追加された新しいバージョンにアップグレードされます。 アップグレード プロセスでは、前のデバッグ セッションで入力したすべてのデータが保持されます。
2. **アプリケーションの保持**: デバッグ セッションが終了しても、アプリケーションはクラスターで実行されたままになります。 次に **F5** キーを押すと、アプリケーションは削除され、新しく作成されたアプリケーションがクラスターにデプロイされます。
3. **アプリケーションの削除** : アプリケーションは、デバッグ セッションが終了したときに削除されます。

**[自動アップグレード]** の場合、データは Service Fabric のアプリケーション アップグレード機能を適用して保持されますが、安全性よりもパフォーマンスを最適化するために調整されます。 アプリケーションのアップグレードと実際の環境でアップグレードを実行する方法の詳細については、「 [Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)」を参照してください。

![日付が追加された新しいアプリケーション バージョンの例][preservedata]

> [!NOTE]
> このプロパティは、Service Fabric Tools for Visual Studio バージョン 1.1 より前のバージョンには存在しません。 1.1 より前のバージョンでは、**[開始時にデータを保存]** プロパティを使用してください。同じ動作が実現します。 "アプリケーションの保持" オプションは、Visual Studio の Service Fabric ツールのバージョン 1.2 で導入されました。
>
>

## <a name="add-a-service-to-your-service-fabric-application"></a>Service Fabric アプリケーションにサービスを追加します。
新しいサービスをアプリケーションに追加して、その機能を拡張することができます。  サービスがアプリケーション パッケージに含まれているかどうかを確認するには、 **[新規 Fabric サービス]** メニュー項目からサービスを追加します。

![新しい Fabric サービスをアプリケーションに追加する][newservice]

アプリケーションに追加する Service Fabric プロジェクトの種類を選択し、サービスの名前を指定します。  使用するサービスの種類を決定するのに役立つ、「 [サービスのフレームワークの選択](service-fabric-choose-framework.md) 」を参照してください。

![アプリケーションに追加する Fabric サービス プロジェクトの種類を選択する][addserviceproject]

新しいサービスは、ソリューションおよび既存のアプリケーション パッケージに追加されます。 サービス参照と既定のサービス インスタンスは、アプリケーション マニフェストに追加されます。 サービスが作成され、次回、アプリケーションをデプロイする際に起動します。

![新しいサービスは、アプリケーション マニフェストに追加されます][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Service Fabric アプリケーションのパッケージ化
アプリケーションとそのサービスをクラスターにデプロイするには、アプリケーション パッケージを作成する必要があります。  パッケージは、アプリケーション マニフェスト、サービス マニフェスト、および特定のレイアウトで必要なその他のファイルを整理します。  Visual Studio は、パッケージを 'pkg' ディレクトリのアプリケーション プロジェクト フォルダーに設定し、管理します。  **[アプリケーション]** コンテキスト メニューから **[パッケージ]** をクリックすると、アプリケーション パッケージが作成または更新されます。  カスタム Powershell スクリプトを使用してアプリケーションをデプロイする場合に、これを実行できます。

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Cloud Explorer を使用したアプリケーションとアプリケーションの種類の削除
Visual Studio 内から Cloud Explorer を使用して、基本的なクラスター管理操作を実行できます。Cloud Explorer は、**[表示]** メニューから起動できます。 たとえば、ローカルやリモートのクラスターで、アプリケーションを削除したり、アプリケーションの種類のプロビジョニングを解除できます。

![アプリケーションの削除](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

> [!TIP]
> 高度なクラスター管理機能については、「 [Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)」を参照してください。
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次のステップ
* [Service Fabric のアプリケーション モデル](service-fabric-application-model.md)
* [Service Fabric アプリケーションのデプロイ](service-fabric-deploy-remove-applications.md)
* [複数の環境のアプリケーション パラメーターを管理する](service-fabric-manage-multiple-environment-app-configuration.md)
* [Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)
* [Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]:./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png



<!--HONumber=Jan17_HO1-->


