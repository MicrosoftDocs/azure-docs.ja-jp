---
title: Visual Studio でのアプリケーションの管理
description: Visual Studio を利用し、Azure Service Fabric のアプリケーションとサービスを作成、開発、パッケージ化、デプロイ、デバッグします。
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 1fd9295a0dead180767febcc5339ef0a25cb1e48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574549"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Service Fabric アプリケーションの記述と管理が簡単にするための Visual Studio の利用
Azure Service Fabric アプリケーションとサービスを Visual Studio で管理できます。 [開発環境のセットアップ](service-fabric-get-started.md)が済んだら、Visual Studio を使用して、Service Fabric アプリケーションの作成、サービスの追加、ローカル開発クラスターでのアプリケーションのパッケージ化、登録、およびデプロイができます。

## <a name="deploy-your-service-fabric-application"></a>Service Fabric アプリケーションのデプロイ
アプリケーションのデプロイでは、既定で、次の手順が 1 つの単純な操作にまとめられています。

1. アプリケーション パッケージの作成
2. アプリケーション パッケージのイメージ ストアへのアップロード
3. アプリケーションの種類の登録
4. 実行している任意のアプリケーション インスタンスの削除
5. アプリケーション インスタンスの作成

Visual Studio で、 **[F5]** キーを押すと、アプリケーションがデプロイされ、すべてのアプリケーション インスタンスにデバッガーがアタッチされます。 **[Ctrl + F5]** キーを押してデバッグなしでアプリケーションをデプロイするか、発行プロファイルを使用してローカルまたはリモート クラスターにアプリケーションを発行できます。

### <a name="application-debug-mode"></a>アプリケーション デバッグ モード
Visual Studio には、**アプリケーション デバッグ モード** というプロパティが用意されており、Visual Studio でデバッグ時にアプリケーションのデプロイを処理する方法を制御できます。

#### <a name="to-set-the-application-debug-mode-property"></a>Application Debug Mode (アプリケーション デバッグ モード) プロパティを設定するには
1. Service Fabric アプリケーション プロジェクトの (*.sfproj) ショートカット メニューで、 **[プロパティ]** を選択します (または **[F4]** キーを押します)。
2. **[プロパティ]** ウィンドウで **[アプリケーション デバッグ モード]** プロパティを設定します。

![Set Application Debug Mode Property][debugmodeproperty]

#### <a name="application-debug-modes"></a>アプリケーション デバッグ モード

1. **アプリケーションの更新**: このモードでは、コードをすばやく変更およびデバッグでき、デバッグ中の静的な Web ファイルの編集がサポートされます。 このモードが機能するのは、ローカル開発クラスターが 1 ノード モードの場合のみです。 これが既定のアプリケーション デバッグ モードです。
2. **アプリケーションの削除** : アプリケーションは、デバッグ セッションが終了したときに削除されます。
3. **自動アップグレード**: デバッグ セッションが終了しても、アプリケーションは実行されたままになります。 次回のデバッグ セッションでは、デプロイはアップグレードとして扱われます。 アップグレード プロセスでは、前のデバッグ セッションで入力したすべてのデータが保持されます。
4. **アプリケーションの保持**: デバッグ セッションが終了しても、アプリケーションはクラスターで実行されたままになります。 次回のデバッグ セッションの開始時に、アプリケーションは削除されます。

**[自動アップグレード]** の場合、データは Service Fabric のアプリケーション アップグレード機能を適用して保持されます。 アプリケーションのアップグレードと実際の環境でアップグレードを実行する方法の詳細については、「 [Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)」を参照してください。

## <a name="add-a-service-to-your-service-fabric-application"></a>Service Fabric アプリケーションにサービスを追加します。
新しいサービスをアプリケーションに追加して、その機能を拡張することができます。 サービスがアプリケーション パッケージに含まれているかどうかを確認するには、 **[新規 Fabric サービス]** メニュー項目からサービスを追加します。

![新しい Service Fabric サービスを追加する][newservice]

アプリケーションに追加する Service Fabric プロジェクトの種類を選択し、サービスの名前を指定します。  使用するサービスの種類を決定するのに役立つ、「 [サービスのフレームワークの選択](service-fabric-choose-framework.md) 」を参照してください。

![アプリケーションに追加する Service Fabric サービス プロジェクトの種類を選択する][addserviceproject]

新しいサービスは、ソリューションおよび既存のアプリケーション パッケージに追加されます。 サービス参照と既定のサービス インスタンスは、アプリケーション マニフェストに追加されます。これにより、サービスが作成され、次回、アプリケーションをデプロイする際に起動します。

![新しいサービスがアプリケーション マニフェストに追加されている][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Service Fabric アプリケーションのパッケージ化
アプリケーションとそのサービスをクラスターにデプロイするには、アプリケーション パッケージを作成する必要があります。  パッケージは、アプリケーション マニフェスト、サービス マニフェスト、および特定のレイアウトで必要なその他のファイルを整理します。  Visual Studio は、パッケージを 'pkg' ディレクトリのアプリケーション プロジェクト フォルダーに設定し、管理します。  **[アプリケーション]** コンテキスト メニューから **[パッケージ]** をクリックすると、アプリケーション パッケージが作成または更新されます。

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Cloud Explorer を使用したアプリケーションとアプリケーションの種類の削除
Visual Studio 内から Cloud Explorer を使用して、基本的なクラスター管理操作を実行できます。Cloud Explorer は、 **[表示]** メニューから起動できます。 たとえば、ローカルやリモートのクラスターで、アプリケーションを削除したり、アプリケーションの種類のプロビジョニングを解除できます。

![アプリケーションの削除][removeapplication]

> [!TIP]
> 高度なクラスター管理機能については、「[Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)」を参照してください。
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
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
