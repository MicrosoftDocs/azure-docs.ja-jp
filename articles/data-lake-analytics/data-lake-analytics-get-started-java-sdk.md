---
title: "Data Lake Analytics Java SDK を使用してアプリケーションを開発する | Microsoft Docs"
description: "Azure Data Lake Analytics Java SDK を使用してアプリケーションを開発する"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 07830b36-2fe3-4809-a846-129cf67b6a9e
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 5d73d1203faf485d715354e68ce2ccde32562611
ms.openlocfilehash: 99e1c8956968264fbea696b85ac084281d75f02a


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-java-sdk"></a>チュートリアル: Java SDK で Azure Data Lake Analytics の使用を開始する
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Data Lake Analytics Java SDK を使用して、Azure Data Lake アカウントを作成し、フォルダーの作成、データ ファイルのアップロードとダウンロード、アカウントの削除、ジョブの操作などの基本操作を行う方法について説明します。 Data Lake の詳細については、「 [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

このチュートリアルでは、テスト データの作成とジョブの送信だけでなく、一般的な管理タスクのサンプルを含む Java コンソール アプリケーションを作成します。  サポートされている他のツールを使用する同じチュートリアルを読み進めるには、このセクションの上部にあるタブをクリックします。

## <a name="prerequisites"></a>前提条件
* Java Development Kit (JDK) 8 (Java バージョン 1.8 を使用)。
* IntelliJ または別の適切な Java 開発環境。 この手順は省略可能ですが、実施することをお勧めします。 以下の手順では、IntelliJ を使用します。
* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure サブスクリプションを有効にします** 。 [手順](data-lake-analytics-get-started-portal.md)を参照してください。
* Azure Active Directory (AAD) アプリケーションを作成し、**そのクライアント ID**、**テナント ID**、および**キー**を取得します。 AAD アプリケーションと、クライアント ID の取得方法の詳細については、 [ポータルを使用した Active Directory のアプリケーションとサービス プリンシパルの作成](../azure-resource-manager/resource-group-create-service-principal-portal.md)に関するページを参照してください。 アプリケーションを作成しキーを生成したら、応答 URI とキーをポータルから使用することもできます。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Azure Active Directory を使用して認証する方法
次のコード スニペットは、アプリケーションが独自の資格情報を提供する、 **非対話型** 認証用のコードを提供します。

このチュートリアルが動作するには、アプリケーションに対して、Azure でリソースを作成するためのアクセス許可を付与する必要があります。 このチュートリアルの目的においては、このアプリケーションの共同作成者に対して、Azure サブスクリプション内の未使用で新しい空のリソース グループへのアクセス許可のみを付与することを **強くお勧めします** 。

## <a name="create-a-java-application"></a>Java アプリケーションの作成
1. IntelliJ を開き、 **コマンド ライン アプリ** テンプレートを使用して新しい Java プロジェクトを作成します。
2. 画面の左側にあるプロジェクトを右クリックし、 **[フレームワーク サポートの追加]**をクリックします。 **[Maven]** を選択し、**[OK]** をクリックします。
3. 新しく作成された **"pom.xml"** ファイルを開き、**\</version>** タグと **\</project>** タグの間に、次のテキストのスニペットを追加します。

    >[!NOTE]
    >この手順は、Azure Data Lake Analytics SDK が Maven で利用できるようになるまでの一時的なものです。 この記事は、Maven で SDK が利用可能になると更新されます。 この SDK に対する今後のすべての更新は、Maven を介して利用できます。
    >

        <repositories>
            <repository>
                <id>adx-snapshots</id>
                <name>Azure ADX Snapshots</name>
                <url>http://adxsnapshots.azurewebsites.net/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
            <repository>
                <id>oss-snapshots</id>
                <name>Open Source Snapshots</name>
                <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <dependencies>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-authentication</artifactId>
                <version>1.0.0-20160513.000802-24</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-runtime</artifactId>
                <version>1.0.0-20160513.000812-28</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.rest</groupId>
                <artifactId>client-runtime</artifactId>
                <version>1.0.0-20160513.000825-29</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-store</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-analytics</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
        </dependencies>
4. **[File (ファイル)]**、**[Settings (設定)]**、**[Build (ビルド)]**、**[Execution (実行)]**、**[Deployment (デプロイ)]** の順に移動します。 **[Build Tools (ビルド ツール)]**、**[Maven]**、**[Importing (インポート)]** の順に選択します。 次に、 **[Maven プロジェクトを自動的にインポートする]**をオンにします。
5. **Main.java** を開き、既存のコード ブロックを次のコードに置き換えます。 また、**localFolderPath**、**_adlaAccountName**、**_adlsAccountName**、**_resourceGroupName** などのコード スニペットで呼び出されたパラメーターの値を入力し、**CLIENT-ID**、**CLIENT-SECRET**、**TENANT-ID**、および **SUBSCRIPTION-ID** のプレースホルダーを置き換えます。

    このコードでは、Data Lake Store と Data Lake Analytics のアカウントの作成、ストアでのファイルの作成、ジョブの状態の取得、ジョブ出力のダウンロード、最後にアカウントの削除のプロセスを行います。

   > [!NOTE]
   > 現時点で、Azure Data Lake Service の既知の問題があります。  サンプル アプリケーションが中断されたり、エラーが発生した場合は、スクリプトによって作成された Data Lake Store と Data Lake Analytics のアカウントを手動で削除することが必要になる場合があります。  ポータルに慣れていない場合は、「 [Azure ポータルを使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md) 」から始めることをお勧めします。
   >
   >

        package com.company;

        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.azure.management.datalake.analytics.*;
        import com.microsoft.azure.management.datalake.analytics.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.nio.file.Files;
        import java.nio.file.Paths;
        import java.util.ArrayList;
        import java.util.UUID;
        import java.util.List;

        public class Main {
            private static String _adlsAccountName;
            private static String _adlaAccountName;
            private static String _resourceGroupName;
            private static String _location;

            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;

            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;

            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";

                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";

                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.

                String localFolderPath = "C:\\local_path\\"; // TODO: Change this to any unused, new, empty folder on your local machine.

                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);

                // Create Data Lake Store and Analytics accounts
                WaitForNewline("Authenticated.", "Creating NEW accounts.");
                CreateAccounts();
                WaitForNewline("Accounts created.", "Displaying accounts.");

                // List Data Lake Store and Analytics accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                System.out.println(String.format("All ADL Analytics accounts that this app can access in subscription %s:", _subId));
                List<DataLakeAnalyticsAccount> adlaListResult = _adlaClient.getAccountOperations().list().getBody();
                for (DataLakeAnalyticsAccount acct : adlaListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Accounts displayed.", "Creating files.");

                // Create a file in Data Lake Store: input1.csv
                // TODO: these change order in the next patch
                byte[] bytesContents = "123,abc".getBytes();
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, "/input1.csv", bytesContents, true);

                WaitForNewline("File created.", "Submitting a job.");

                // Submit a job to Data Lake Analytics
                UUID jobId = SubmitJobByScript("@input =  EXTRACT Data string FROM \"/input1.csv\" USING Extractors.Csv(); OUTPUT @input TO @\"/output1.csv\" USING Outputters.Csv();", "testJob");
                WaitForNewline("Job submitted.", "Getting job status.");

                // Wait for job completion and output job status
                System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
                System.out.println("Waiting for job completion.");
                WaitForJob(jobId);
                System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
                WaitForNewline("Job completed.", "Downloading job output.");

                // Download job output from Data Lake Store
                DownloadFile("/output1.csv", localFolderPath + "output1.csv");
                WaitForNewline("Job output downloaded.", "Deleting file.");

                // Delete file from Data Lake Store
                DeleteFile("/output1.csv");
                WaitForNewline("File deleted.", "Deleting account.");

                // Delete account
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
                _adlaClient.getAccountOperations().delete(_resourceGroupName, _adlaAccountName);
                WaitForNewline("Account deleted.", "DONE.");
            }

            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
                _adlaClient = new DataLakeAnalyticsAccountManagementClientImpl(creds);
                _adlaJobClient = new DataLakeAnalyticsJobManagementClientImpl(creds);
                _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClientImpl(creds);
                _adlsClient.setSubscriptionId(_subId);
                _adlaClient.setSubscriptionId(_subId);
            }

            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";

                System.out.println(reason + "\r\nPress ENTER to continue...");
                try{System.in.read();}
                catch(Exception e){}

                if (!nextAction.isEmpty())
                {
                    System.out.println(nextAction);
                }
            }

            // Create Data Lake Store and Analytics accounts
            public static void CreateAccounts() throws InterruptedException, CloudException, IOException {
                // Create ADLS account
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);

                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);

                // Create ADLA account
                DataLakeStoreAccountInfo adlsInfo = new DataLakeStoreAccountInfo();
                adlsInfo.setName(_adlsAccountName);

                DataLakeStoreAccountInfoProperties adlsInfoProperties = new DataLakeStoreAccountInfoProperties();
                adlsInfo.setProperties(adlsInfoProperties);

                List<DataLakeStoreAccountInfo> adlsInfoList = new ArrayList<DataLakeStoreAccountInfo>();
                adlsInfoList.add(adlsInfo);

                DataLakeAnalyticsAccountProperties adlaProperties = new DataLakeAnalyticsAccountProperties();
                adlaProperties.setDataLakeStoreAccounts(adlsInfoList);
                adlaProperties.setDefaultDataLakeStoreAccount(_adlsAccountName);

                DataLakeAnalyticsAccount adlaParameters = new DataLakeAnalyticsAccount();
                adlaParameters.setLocation(_location);
                adlaParameters.setName(_adlaAccountName);
                adlaParameters.setProperties(adlaProperties);

                    /* If this line generates an error message like "The deep update for property 'DataLakeStoreAccounts' is not supported", please delete the ADLS and ADLA accounts via the portal and re-run your script. */

                _adlaClient.getAccountOperations().create(_resourceGroupName, _adlaAccountName, adlaParameters);
            }

            //todo: this changes in the next version of the API
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();

                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }

            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(filePath, _adlsAccountName);
            }

            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(srcPath, _adlsAccountName).getBody();

                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());

                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();

                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }

                pWriter.println(fileContents);
                pWriter.close();
            }

            // Submit a U-SQL job by providing script contents.
            // Returns the job ID
            public static UUID SubmitJobByScript(String script, String jobName) throws IOException, CloudException {
                UUID jobId = java.util.UUID.randomUUID();
                USqlJobProperties properties = new USqlJobProperties();
                properties.setScript(script);
                JobInformation parameters = new JobInformation();
                parameters.setName(jobName);
                parameters.setJobId(jobId);
                parameters.setType(JobType.USQL);
                parameters.setProperties(properties);

                JobInformation jobInfo = _adlaJobClient.getJobOperations().create(_adlaAccountName, jobId, parameters).getBody();

                return jobId;
            }

            // Wait for job completion
            public static JobResult WaitForJob(UUID jobId) throws IOException, CloudException {
                JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
                while (jobInfo.getState() != JobState.ENDED)
                {
                    jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName,jobId).getBody();
                }
                return jobInfo.getResult();
            }

            // Get job status
            public static String GetJobStatus(UUID jobId) throws IOException, CloudException {
                JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
                return jobInfo.getState().toValue();
            }
        }

1. 指示に従って、アプリケーションを実行して完了します。

## <a name="see-also"></a>関連項目
* 他のツールを使用する同じチュートリアルを表示するには、ページの上部にあるタブ セレクターをクリックします。
* より複雑なクエリを表示する場合は、「 [チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する](data-lake-analytics-analyze-weblogs.md)」をご覧ください。
* U-SQL アプリケーションの開発を開始する場合は、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をご覧ください。
* U-SQL の詳細については、「[チュートリアル: Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」および「[U-SQL Language Reference (U-SQL 言語リファレンス)](http://go.microsoft.com/fwlink/?LinkId=691348)」をご覧ください。
* 管理タスクについては、「 [Azure ポータルを使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
* Data Lake Analytics の概要については、「 [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。



<!--HONumber=Dec16_HO4-->


