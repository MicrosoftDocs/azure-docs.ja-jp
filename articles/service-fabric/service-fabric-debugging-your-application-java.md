---
title: "Eclipse での Azure Service Fabric アプリケーションのデバッグ| Microsoft Docs"
description: "Eclipse とローカル開発クラスターを使用してサービスを開発、デバッグし、サービスの信頼性とパフォーマンスを改善します。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: vturecek;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 3d122720c834de672d5cb512c7bad2dc5ed3ea58
ms.openlocfilehash: aa608d6ab76792234e6607160bb17cff520b5665


---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Eclipse を使用して Java Service Fabric アプリケーションをデバッグする
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. 「 [Service Fabric 開発環境の設定](service-fabric-get-started-linux.md)」にある手順に従って、ローカル開発クラスターを開始してください。

2. デバッグするサービスの entryPoint.sh を更新して、リモートのデバッグ パラメーターで java プロセスを開始するようにします。 このファイルは次の場所にあります: ``ApplicationName\ServiceNamePkg\Code\entrypoint.sh``。 この例では、デバッグ用にポート 8001 が設定されています。

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. アプリケーション マニフェストを更新して、デバッグするサービスのインスタンス数またはレプリカ数を 1 に設定します。 この設定により、デバッグに使用されるポートの競合を回避できます。 たとえば、ステートレス サービスについては、``InstanceCount="1"`` と設定し、ステートフル サービスについては、`` TargetReplicaSetSize="1" MinReplicaSetSize="1"`` のように設定して、レプリカ セット サイズのターゲット値と最小値を 1 にします。

4. アプリケーションをデプロイします。

5. Eclipse IDE で、**[実行] -> [デバッグ構成] -> [リモート Java アプリケーション]** を選択し、接続プロパティを次のように設定します。

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  必要なポイントにブレークポイントを設定し、アプリケーションをデバッグします。

アプリケーションがクラッシュする場合は、コアダンプを有効にすることもできます。 シェル内で ``ulimit -c`` を実行し、ゼロが返された場合は、コアダンプが無効になっています。 無制限コアダンプを有効にするには、次のコマンドを実行します: ``ulimit -c unlimited``。 また、コマンド ``ulimit -a`` を使用して状態を確認することもできます。  コアダンプの生成パスを更新する場合は、``echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`` を実行します。 

### <a name="next-steps"></a>次のステップ

* [Linux Azure 診断でログを収集する方法](service-fabric-diagnostics-how-to-setup-lad.md)。
* [ローカルでのサービスの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)。



<!--HONumber=Jan17_HO1-->


