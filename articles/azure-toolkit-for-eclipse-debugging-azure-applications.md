---
title: "Eclipse での Azure アプリケーションのデバッグ"
description: "Azure Toolkit for Eclipse を使用する Azure アプリケーションのデバッグについて説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 3d1b34bf-399e-421e-bdcc-da2d422dbb4f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 04e6a43e478484d90478e4aa90d489ea4eb0cc68


---
# <a name="debugging-azure-applications-in-eclipse"></a>Eclipse での Azure アプリケーションのデバッグ
Azure Toolkit for Eclipse を使用すると、Azure で実行しているアプリケーションでも、オペレーティング システムとして Windows を使用している場合はコンピューティング エミュレーター内のアプリケーションでも、デバッグできます。 次の図は、リモート デバッグを有効にするために使用する **[デバッグ]** プロパティ ダイアログを示しています。

![][ic719504]

このチュートリアルでは、アプリケーションが正常に作成されていて、コンピューティング エミュレーターおよび Azure へのデプロイを理解しているものとします。

このトピックでは、「[JSP で Azure サービス ランタイム ライブラリを使用する][Using the Azure Service Runtime Library in JSP]」チュートリアルで作成したアプリケーションから始めます。 アプリケーションをまだ作成していない場合は、先に進む前に作成してください。

## <a name="to-debug-your-application-while-running-in-azure"></a>Azure で実行しながらアプリケーションをデバッグするには
> [!WARNING]
> Toolkit でのリモート Java デバッグの現在のサポートは、主に、Azure コンピューティング エミュレーターで実行中のデプロイメントに対するものです。 デバッグ接続はセキュリティで保護されていないため、運用環境のデプロイメントではリモート デバッグを有効にしないでください。 Azure クラウドで実行しているアプリケーションをデバッグする必要がある場合は、ステージング デプロイメントを使用します。ただし、ステージング デプロイメントであっても、クラウド デプロイメントの IP アドレスがわかると、デバッグ セッションに不正アクセスできることを理解しておいてください。
> 
> 

1. エミュレーターでのテスト用のプロジェクトをビルドします。Eclipse の [Project Explorer (プロジェクト エクスプローラー)] で、**[MyAzureProject]** を右クリックし、**[Properties (プロパティ)]**、**[Azure]** の順にクリックして、**[Build for (ビルド対象)]** を **[Deployment to cloud (クラウドへのデプロイ)]** に設定します。
2. プロジェクトをビルドし直します。Eclipse メニューから、**[Project (プロジェクト)]**、**[Build All (すべてビルド)]** の順にクリックします。
3. アプリケーションを Azure の *ステージング* にデプロイします。
    >[AZURE.IMPORTANT] 前述のように、ほとんどの場合はコンピューティング エミュレーターでデバッグし、追加のデバッグが必要な場合にのみステージング環境でデバッグすることを、強くお勧めします。 運用環境ではデバッグしないことをお勧めします。
4. Azure でデプロイメントの準備ができたら、[Microsoft Azure 管理ポータル][Azure Management Portal]からデプロイメントの DNS 名を取得します。 ステージング デプロイメントの DNS 名の形式は http://*&lt;guid&gt;*.cloudapp.net であり、*&lt;guid&gt;* は Azure によって割り当てられる GUID の値です。
5. Eclipse の [Project Explorer (プロジェクト エクスプローラー)] で、**[WorkerRole1]** を右クリックし、**[Azure]** をクリックしてから **[Debugging (デバッグ)]** をクリックします。
6. **[WorkerRole1 デバッグのプロパティ]** ダイアログで次のようにします。
   1. **[このロールのリモート デバッグを有効にする]**
   2. **[Input endpoint to use (使用する入力エンドポイント)]** では、**[Debugging (public:8090, private:8090) (デバッグ (パブリック:&8090;、プライベート:&8090;))]** を使用します。
   3. **[JVM を一時停止モードで開始し、デバッガーの接続を待機する]** がオフになっていることを確認します。
       >[AZURE.IMPORTANT] **[JVM を一時停止モードで開始し、デバッガーの接続を待機する]** オプションは、コンピューティング エミュレーターのみでの高度なデバッグ シナリオ用です (クラウド デプロイメント用ではありません)。 **[JVM を一時停止モードで開始し、デバッガーの接続を待機する]** オプションを使用する場合は、Eclipse デバッガーが JVM に接続されるまで、サーバーのスタートアップ プロセスは中断されます。 コンピューティング エミュレーターを使用するデバッグ セッションにこのオプションを使用している間は、クラウド デプロイメントでのデバッグ セッションに使用しないでください。 Azure のスタートアップ タスクでサーバーの初期化が行われ、スタートアップ タスクが完了するまで、Azure クラウドはパブリック エンドポイントを利用できるようにしません。 したがって、クラウド デプロイメントでこのオプションを有効にした場合、外部 Eclipse クライアントからの接続を受けられないため、スタートアップ プロセスが正常に完了しません。
   4. **[デバッグ構成の作成]**をクリックします。
7. **[Azure デバッグ構成]** ダイアログで次のようにします。
   1. **[Java project to debug (デバッグする Java プロジェクト)]** で、**MyHelloWorld** プロジェクトを選択します。
   2. **[Configure debugging for (デバッグ構成の対象)]** で、**[Azure cloud (staging) (Azure クラウド (ステージング))]** をオンにします。
   3. **[Azure コンピューティング エミュレーター]** がオフになっていることを確認します。
   4. **[Host (ホスト)]** には、ステージングされたデプロイの DNS 名を入力します。ただし、先頭の **http://** は省略します。 例 (ここで示す GUID の代わりに実際の GUID を使用してください): **4e616d65-6f6e-6d65-6973-526f62657274.cloudapp.net**
8. **[OK]** をクリックして **[Azure Debug Configuration (Azure デバッグ構成)]** ダイアログを閉じます。
9. **[OK]** をクリックして **[Properties for WorkerRole1 Debugging (WorkerRole1 デバッグのプロパティ)]** ダイアログを閉じます。
10. index.jsp でブレークポイントをまだ設定していない場合は、設定します。
    1. Eclipse の [Project Explorer (プロジェクト エクスプローラー)] で、**[MyHelloWorld]**、**[WebContent]** の順に展開し、**index.jsp** をダブルクリックします。
    2. 次の図のように、index.jsp で Java コードの左側にある青いバーを右クリックし、**[Toggle Breakpoints (ブレークポイントの切り替え)]** をクリックします。 ![][ic551537]
11. Eclipse のメニューで、**[Run (実行)]**、**[Debug Configurations (デバッグ構成)]** の順にクリックします。
12. **[デバッグ構成]** ダイアログで、左側のウィンドウの **[Remote Java Application (リモート Java アプリケーション)]** を展開し、**[Azure Cloud (WorkerRole1) (Azure クラウド (WorkerRole1))]** を選択して、**[Debug (デバッグ)]** をクリックします。
13. ブラウザーで、ステージングしたアプリケーション **http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld** を実行します。*&lt;guid&gt;* は実際の DNS 名の GUID に置き換えてください。 **[Confirm Perspective Switch (パースペクティブ スイッチの確認)]** ダイアログ ボックスにプロンプトが表示されたら、**[Yes (はい)]** をクリックします。 ブレークポイントを設定したコード行まで、デバッグ セッションが実行されます。

> [!NOTE]
> 複数のロール インスタンスが実行されているデプロイメントへのリモート デバッグ接続を開始する場合、現在は、デバッガーが最初に接続されるインスタンスを制御することはできません。Azure のロード バランサーがインスタンスをランダムに選択します。 そのインスタンスに接続されても、同じインスタンスのデバッグを続けます。 また、非アクティブな期間が 4 分を超えると (たとえば、ブレークポイントでの停止時間が長すぎた場合)、Azure が接続を閉じる場合があることにも注意してください。
> 
> 

## <a name="debugging-a-specific-role-instance-in-a-multi-instance-deployment"></a>複数インスタンス デプロイメントでの特定のロール インスタンスのデバッグ
デプロイメントをクラウドで実行する場合、ほとんどは複数のコンピューティング インスタンス (つまり、ロール インスタンス) で実行します。 これにより、Azure の 99.95% 可用性保証を利用でき、アプリケーションをスケールアウトできます。

このようなシナリオでは、特定のロール インスタンスの Java アプリケーションをリモート デバッグすることが必要になる場合があります。 ただし、標準の入力エンドポイントのみでデバッグを有効にした場合、Azure ロード バランサーにより、ユーザーが特定のロール インスタンスにデバッガーを接続することはほぼ不可能になります。 代わりに、ロード バランサーによってランダムに選択されたロール インスタンスに接続されます。

この種のシナリオでは、インスタンスの入力エンドポイントを利用すると、特定のロール インスタンスのデバッグが容易になります。

たとえば、デプロイメントの最大 5 つのロール インスタンスを実行するとします。 ロール プロパティ ダイアログの **[エンドポイント]** プロパティ ページを使用して、インスタンス入力エンドポイントを作成し、単一のポート番号ではなくパブリック ポートの範囲にそれを割り当てます。 たとえば、**[Public port (パブリック ポート)]** 入力ボックスで「**81-85**」と指定します。

このインスタンス エンドポイントでアプリケーションをデプロイすると、Azure はこの範囲から一意のポート番号を各ロール インスタンスに割り当てます。 その場合、各インスタンスに割り当てられたポート番号を調べるには、Toolkit によってデプロイに自動的に構成される *InstanceEndpointName***_PUBLICPORT** 環境変数 (ここで、*InstanceEndpointName* はインスタンス エンドポイントを作成したときに割り当てた名前) を使用できます (たとえば、Web ページのフッターでその値を返すと、参照時に確認できます)。

インスタンスに割り当てられたパブリック ポート番号がわかったら、サービスのホスト名にそれを付加することによって、Eclipse のデバッグ構成で参照できます。 これにより、Eclipse デバッガーは、他のインスタンスではなく、その特定のインスタンスに接続できます。

## <a name="windows-only-to-debug-your-application-while-running-in-the-compute-emulator"></a>Windows のみ: コンピューティング エミュレーターで実行中のアプリケーションをデバッグするには
> [!NOTE]
> Azure エミュレーターは Windows でのみ使用できます。 Windows 以外のオペレーティング システムを使用する場合は、このセクションをスキップしてください。 
> 
> 

1. エミュレーターでのテスト用のプロジェクトをビルドします。Eclipse の [Project Explorer (プロジェクト エクスプローラー)] で、**[MyAzureProject]** を右クリックし、**[Properties (プロパティ)]**、**[Azure]** の順にクリックして、**[Build for (ビルド対象)]** を **[Testing in emulator (エミュレーターでのテスト)]** に設定します。
2. プロジェクトをビルドし直します。Eclipse メニューから、**[Project (プロジェクト)]**、**[Build All (すべてビルド)]** の順にクリックします。
3. Eclipse の [Project Explorer (プロジェクト エクスプローラー)] で、**[WorkerRole1]** を右クリックし、**[Azure]** をクリックしてから **[Debugging (デバッグ)]** をクリックします。
4. **[WorkerRole1 デバッグのプロパティ]** ダイアログで次のようにします。
   1. **[このロールのリモート デバッグを有効にする]**
   2. **[Input endpoint to use (使用する入力エンドポイント)]** では、Toolkit によって自動的に生成される既定のエンドポイントを使用します (一覧では **[Debugging (public:8090, private:8090) (デバッグ (パブリック:&8090;、プライベート:&8090;))]** と表示されます)。
   3. **[JVM を一時停止モードで開始し、デバッガーの接続を待機する]** オプションがオフになっていることを確認します。
       >[AZURE.IMPORTANT] **[JVM を一時停止モードで開始し、デバッガーの接続を待機する]** オプションは、コンピューティング エミュレーターのみでの高度なデバッグ シナリオ用です (クラウド デプロイメント用ではありません)。 **[JVM を一時停止モードで開始し、デバッガーの接続を待機する]** オプションを使用する場合は、Eclipse デバッガーが JVM に接続されるまで、サーバーのスタートアップ プロセスは中断されます。 コンピューティング エミュレーターを使用するデバッグ セッションにこのオプションを使用している間は、クラウド デプロイメントでのデバッグ セッションに使用しないでください。 Azure のスタートアップ タスクでサーバーの初期化が行われ、スタートアップ タスクが完了するまで、Azure クラウドはパブリック エンドポイントを利用できるようにしません。 したがって、クラウド デプロイメントでこのオプションを有効にした場合、外部 Eclipse クライアントからの接続を受けられないため、スタートアップ プロセスが正常に完了しません。
   4. **[デバッグ構成の作成]**をクリックします。
5. **[Azure デバッグ構成]** ダイアログで次のようにします。
   1. **[Java project to debug (デバッグする Java プロジェクト)]** で、**MyHelloWorld** プロジェクトを選択します。
   2. **[Configure debugging for (デバッグ構成の対象)]** で、**[Azure compute emulator (Azure コンピューティング エミュレーター)]** をオンにします。
6. **[OK]** をクリックして **[Azure Debug Configuration (Azure デバッグ構成)]** ダイアログを閉じます。
7. **[OK]** をクリックして **[Properties for WorkerRole1 Debugging (WorkerRole1 デバッグのプロパティ)]** ダイアログを閉じます。
8. index.jsp でブレークポイントを設定します。
   
   1. Eclipse の [Project Explorer (プロジェクト エクスプローラー)] で、**[MyHelloWorld]**、**[WebContent]** の順に展開し、**index.jsp** をダブルクリックします。
   2. 次の図のように、index.jsp で Java コードの左側にある青いバーを右クリックし、**[Toggle Breakpoints (ブレークポイントの切り替え)]** をクリックします。 ![][ic551537]
      
      Java コードの左側にある青いバー内にブレークポイント アイコンが表示される場合は、ブレークポイントが設定されています。
9. Azure ツールバーの **[Azure エミュレーターで実行]** ボタンをクリックして、コンピューティング エミュレーターでアプリケーションを開始します。
10. Eclipse のメニューで、**[Run (実行)]**、**[Debug Configurations (デバッグ構成)]** の順にクリックします。
11. **[Debug Configurations (デバッグ構成)]** ダイアログで、左側のウィンドウの **[Remote Java Application (リモート Java アプリケーション)]** を展開し、**[Azure Emulator (WorkerRole1) (Azure エミュレーター (WorkerRole1))]** を選択して、**[Debug (デバッグ)]** をクリックします。
12. コンピューティング エミュレーターにアプリケーションが実行されていることが示されたら、ブラウザーで **http://localhost:8080/MyHelloWorld** を実行します。
     **[Confirm Perspective Switch (パースペクティブ スイッチの確認)]** ダイアログ ボックスにプロンプトが表示されたら、**[Yes (はい)]** をクリックします。
     ブレークポイントを設定したコード行まで、デバッグ セッションが実行されます。

ここまでは、コンピューティング エミュレーターでのデバッグ方法を示しました。次のセクションでは、Azure にデプロイしたアプリケーションのデバッグ方法を示します。

## <a name="debugging-notes"></a>デバッグに関する注意事項
* デバッグが終了したら、Eclipse のメニューで **[Window (ウィンドウ)]**、**[Open Perspective (パースペクティブを開く)]** の順にクリックして、使用するパースペクティブを選択することにより、パースペクティブを**デバッグ**から **Java** に切り替えることができます。
* GlassFish でリモート デバッグを有効にする場合は、Azure Toolkit for Eclipse のリモート デバッグ構成機能を使用しないでください。 代わりに、GlassFish を手動で構成します。 環境変数で定義済みの Java オプションを GlassFish が処理する方法により、Toolkit のリモート デバッグ構成機能は GlassFish では正しく動作しません。 Toolkit のリモート デバッグ構成機能が有効な場合、GlassFish が起動しない可能性があります。

## <a name="see-also"></a>関連項目
[Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]

[Azure 向け Hello World アプリケーションを Eclipse で作成する][Creating a Hello World Application for Azure in Eclipse]

[Azure Toolkit for Eclipse のインストール][Installing the Azure Toolkit for Eclipse] 

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター][Azure Java Developer Center]を参照してください。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Using the Azure Service Runtime Library in JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->



<!--HONumber=Jan17_HO1-->


