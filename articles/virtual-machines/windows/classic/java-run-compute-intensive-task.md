---
title: "VM で多くの計算処理を要する Java アプリケーションを実行する | Microsoft Docs"
description: "Azure の仮想マシンを作成し、多くのコンピューティング処理を要する Java アプリケーションを実行して、別の Java アプリケーションで監視する方法について説明します。"
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: ae6f2737-94c7-4569-9913-d871450c2827
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 290a9dc5ca67495da18d88b61dce1d8b6becea61
ms.lasthandoff: 03/25/2017


---
# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>仮想マシンで多くのコンピューティング処理を要する Java タスクを実行する方法
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

Azure で仮想マシンを使用することで、多くのコンピューティング処理を要するタスクを処理できます。 たとえば、仮想マシンでタスクを処理し、結果をクライアント マシンやモバイル アプリケーションに配信できます。 このガイドを記事を読むことで、多くのコンピューティング処理を要する Java アプリケーションを実行し、それを別の Java アプリケーションから監視できる仮想マシンの作成方法を理解できます。

このチュートリアルは、Java コンソール アプリケーションを作成する方法を理解しており、Java アプリケーションへのライブラリのインポートおよび Java アーカイブ (JAR) の生成を実行できることを前提としています。 Microsoft Azure に関する知識は不要です。

学習内容:

* Java Development Kit (JDK) インストール済みの仮想マシンを作成する方法
* 仮想マシンにリモート ログインする方法
* Service Bus 名前空間の作成方法
* 多くのコンピューティング処理を要するタスクを実行する Java アプリケーションの作成方法
* 多くのコンピューティング処理を要するタスクの進捗状況を監視する Java アプリケーションの作成方法
* Java アプリケーションの実行方法
* Java アプリケーションの停止方法

このチュートリアルでは、多くのコンピューティング処理を要するタスクとして、巡回セールスマン問題を使用します。 次に示しているのは、多くのコンピューティング処理を要するタスクを実行する Java アプリケーションの例です。

![巡回セールスマン問題を解くプログラム][solver_output]

以下は、多くのコンピューティング処理を要するタスクを監視する Java アプリケーションの例です。

![巡回セールスマン問題のクライアント][client_output]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>仮想マシンを作成するには
1. [Azure クラシック ポータル](https://manage.windowsazure.com)にログインします。
2. **[新規]**、**[Compute]**、**[仮想マシン]**、**[ギャラリーから]** をクリックします。
3. **[仮想マシン イメージの選択]** ダイアログ ボックスで、**[JDK 7 Windows Server 2012]** を選択します。
   **[JDK 6 Windows Server 2012]** は、JDK 7 を実行する準備ができていないレガシ アプリケーションがある場合に表示されることに注意してください。
4. **[次へ]**をクリックします。
5. **[仮想マシンの構成]** ダイアログ ボックスで次の作業を行います。
   1. 仮想マシンの名前を指定します。
   2. 仮想マシンに使用するサイズを指定します。
   3. **[ユーザー名]** フィールドに、管理者の名前を入力します。 この名前と次に入力するパスワードは忘れないでください。仮想マシンにリモート ログインするときに使用します。
   4. **[新しいパスワード]** フィールドにパスワードを入力し、**[確認]** フィールドに再びパスワードを入力します。 これは、Administrator アカウントのパスワードです。
   5. **[次へ]**をクリックします。
6. 次の **[仮想マシンの構成]** ダイアログ ボックスで次の作業を行います。
   1. **[クラウド サービス]** には、既定の **[新しいクラウド サービスの作成]** を使用します。
   2. **[クラウド サービス DNS 名]** の値は cloudapp.net 全体で一意であることが必要です。 一意であることを示す表示になるように、必要に応じてこの値を修正してください。
   3. リージョン、アフィニティ グループ、または仮想ネットワークを指定します。 このチュートリアルでは、 **[米国西部]**などのリージョンを指定します。
   4. **[ストレージ アカウント]** で、**[自動的に生成されたストレージ アカウントを使用]** を選択します。
   5. **[可用性セット]** は、**[(なし)]** を選択します。
   6. **[次へ]**をクリックします。
7. 最後の **[仮想マシンの構成]** ダイアログ ボックスで次の作業を行います。
   1. 既定のエンドポイント エントリをそのまま使用します。
   2. **[完了]**をクリックします。

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>仮想マシンにリモート ログインするには
1. [Azure クラシック ポータル](https://manage.windowsazure.com)にログオンします。
2. **[仮想マシン]**をクリックします。
3. ログインする仮想マシンの名前をクリックします。
4. **[接続]**をクリックします。
5. 表示される画面で必要に応じて入力して、仮想マシンに接続します。 管理者名とパスワードの入力画面が表示されたら、仮想マシンの作成時に指定した値を使用します。

Azure の Service Bus 機能により、JRE の **cacerts** ストアの一部として Baltimore CyberTrust Root 証明書をインストールすることが求められます。 このチュートリアルで使用する Java ランタイム環境 (JRE) には、この証明書が自動的に含められます。 JRE **cacerts** ストアにこの証明書がない場合は、その追加方法 (および cacerts ストアの証明書を表示する方法) を「[証明書を Java CA 証明書ストアに追加する方法][add_ca_cert]」で確認してください。

## <a name="how-to-create-a-service-bus-namespace"></a>Service Bus 名前空間の作成方法
Azure の Service Bus キューを使用するには、最初にサービス名前空間を作成する必要があります。 サービス名前空間は、アプリケーション内で Service Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。

サービス名前空間を作成するには:

1. [Azure クラシック ポータル](https://manage.windowsazure.com)にログオンします。
2. Azure クラシック ポータルの左下のナビゲーション ウィンドウで、**[サービス バス、アクセス制御、キャッシュ]** をクリックします。
3. Azure クラシック ポータルの左上のナビゲーション ウィンドウで、**[Service Bus]** ノードをクリックしてから **[新規]** をクリックします。  
   ![[Service Bus] ノードのスクリーンショット][svc_bus_node]
4. **[サービス名前空間の新規作成]** ダイアログ ボックスで **[名前空間]** に名前空間の名前を入力し、固有の名前であることを確認するために **[有効か確認]** をクリックします。  
   ![[名前空間の新規作成] のスクリーンショット][create_namespace]
5. 名前空間の名前が有効であることを確認できたら、名前空間をホストする国またはリージョンを選択して、 **[名前空間の作成]** ボタンをクリックします。  
   
   作成した名前空間が Azure クラシック ポータルに表示され、アクティブになります。これには少し時間がかかります。 状態が **[有効]** になるのを待ってから、次の手順に進みます。

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>名前空間の既定の管理資格情報の取得
新規作成した名前空間に対してキューの作成などの管理操作を実行するには、名前空間の管理資格情報を取得する必要があります。

1. 左側のナビゲーション ウィンドウで **[Service Bus]** ノードをクリックして、利用可能な名前空間の一覧を表示します。
   ![使用可能な名前空間のスクリーン ショット][avail_namespaces]
2. 表示された一覧から先ほど作成した名前空間を選択します。
   ![名前空間の一覧のスクリーン ショット][namespace_list]
3. 右側の **[プロパティ]** ウィンドウに、新しい名前空間のプロパティが一覧表示されます。
   ![[プロパティ] ウィンドウのスクリーン ショット][properties_pane]
4. **[既定のキー]** は表示されません。 **[表示]** をクリックしてセキュリティ資格情報を表示します。
   ![既定のキーのスクリーン ショット][default_key]
5. **[既定の発行者]** と **[既定のキー]** をメモしておきます。この情報は、後で名前空間に対して操作を実行するときに使用します。

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>多くのコンピューティング処理を要するタスクを実行する Java アプリケーションの作成方法
1. 開発用コンピューター上で (これは作成した仮想マシンと同じでなくてもかまいません)、 [Azure SDK for Java](https://azure.microsoft.com/develop/java/)をダウンロードします。
2. このセクションの末尾にあるコード例を使用して、Java コンソール アプリケーションを作成します。 このチュートリアルでは、Java ファイル名として **TSPSolver.java** を使用します。 **your\_service\_bus\_namespace**、**your\_service\_bus\_owner**、**your\_service\_bus\_key** の各プレースホルダーを変更して、それぞれ自分の Service Bus の **[名前空間]**、**[既定の発行者]**、**[既定のキー]** の値を設定します。
3. コーディング後、実行可能な Java アーカイブ (JAR) にアプリケーションをエクスポートして、生成される JAR に、必要なライブラリをパッケージ化します。 このチュートリアルでは、生成される JAR 名として **TSPSolver.jar** を使用します。

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>多くのコンピューティング処理を要するタスクの進捗状況を監視する Java アプリケーションの作成方法
1. 開発用コンピューターで、このセクションの末尾にあるコード例を使用して、Java コンソール アプリケーションを作成します。 このチュートリアルでは、Java ファイル名として **TSPClient.java** を使用します。 先ほどと同じように、**your\_service\_bus\_namespace**、**your\_service\_bus\_owner**、**your\_service\_bus\_key** の各プレースホルダーを変更して、それぞれ自分の Service Bus の **[名前空間]**、**[既定の発行者]**、**[既定のキー]** の値を設定します。
2. 実行可能な JAR にアプリケーションをエクスポートして、生成される JAR に、必要なライブラリをパッケージ化します。 このチュートリアルでは、生成される JAR 名として **TSPClient.jar** を使用します。

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Java アプリケーションの実行方法
多くのコンピューティング処理を要するアプリケーションを、最初はキューを作成するために実行し、次に巡回セールスマン問題を解くために実行します。これにより、Service Bus キューに現在の最適な経路が追加されます。 多くのコンピューティング処理を要するアプリケーションの実行中に (または実行後に)、クライアントを実行して Service Bus キューから結果を表示します。

### <a name="to-run-the-compute-intensive-application"></a>多くのコンピューティング処理を要するアプリケーションの実行するには
1. 仮想マシンにログオンします。
2. アプリケーションを実行するフォルダーを作成します。 たとえば、**c:\TSP** です。
3. **TSPSolver.jar** を **c:\TSP** にコピーします。
4. **c:\TSP\cities.txt** という名前のファイルを作成し、内容を次のようにします。
   
        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33
5. コマンド プロンプトで、ディレクトリを c:\TSP に変更します。
6. JRE の bin フォルダーが PATH 環境変数に指定されていることを確認します。
7. 巡回セールスマン問題を解くプログラムを実行する前に、Service Bus キューを作成する必要があります。 次のコマンドを実行して、Service Bus キューを作成します。
   
        java -jar TSPSolver.jar createqueue
8. これでキューが作成されたので、巡回セールスマン問題を解くプログラムを実行できます。 たとえば、次のコマンドを実行すると、8 都市を対象としてプログラムが実行されます。
   
        java -jar TSPSolver.jar 8
   
   数値を指定しなかった場合は、10 都市を対象として実行されます。 現時点で最短の経路が見つかると、それがキューに追加されます。

> [!NOTE]
> 指定した数値が大きいほど、プログラムの実行時間は長くなります。 たとえば、14 都市の場合は数分で実行できても、15 都市になると実行に数時間かかることがありえます。 16 都市以上にすると実行時間が数日になる可能性があります (最終的には数週間、数か月、数年かかります)。 これは都市数が増えるにつれてプログラムが評価する順列の数が急増するためです。
> 
> 

### <a name="how-to-run-the-monitoring-client-application"></a>監視用のクライアント アプリケーションの実行方法
1. クライアント アプリケーションを実行するコンピューターにログオンします。 これは、 **TSPSolver** アプリケーションを実行するコンピューターと同じでなくてもかまいません。
2. アプリケーションを実行するフォルダーを作成します。 たとえば、**c:\TSP** です。
3. **TSPClient.jar** を **c:\TSP** にコピーします。
4. JRE の bin フォルダーが PATH 環境変数に指定されていることを確認します。
5. コマンド プロンプトで、ディレクトリを c:\TSP に変更します。
6. 次のコマンドを実行します。
   
        java -jar TSPClient.jar
   
    必要に応じて、コマンド ライン引数を渡してキューのチェック間隔を分単位で指定します。 キューのチェック間隔の既定値は 3 分です。**TSPClient** にコマンド ライン引数を渡さない場合は、この値が使用されます。 チェック間隔として別の値、たとえば 1 分を使用する場合は、次のコマンドを実行します。
   
        java -jar TSPClient.jar 1
   
    クライアントは、"Complete" というキュー メッセージが見つかるまで実行を続けます。 注意点として、クライアントを実行しないで、問題を解くプログラムを複数実行した場合、キューを完全に空にするにはクライアントの複数回実行が必要になる場合があります。 別の方法として、キューを削除して再び作成することもできます。 キューを削除するには、(**TSPClient** コマンドではなく) 次の **TSPSolver** コマンドを実行します。
   
        java -jar TSPSolver.jar deletequeue
   
    プログラムはすべての経路の調査が完了すると終了します。

## <a name="how-to-stop-the-java-applications"></a>Java アプリケーションの停止方法
問題を解くアプリケーションとクライアント アプリケーションのどちらでも、 **Ctrl + C** キーを押すと、通常の処理が完了する前にアプリケーションが終了します。

[solver_output]:media/java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]:media/java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]:media/java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]:media/java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]:media/java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]:media/java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]:media/java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]:media/java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../../../java-add-certificate-ca-store.md

