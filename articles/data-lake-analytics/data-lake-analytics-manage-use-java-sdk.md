---
title: Azure Java SDK を使用して Azure Data Lake Analytics を管理する
description: このアーティクルでは、 Data Lake Analytics のジョブ、データ ソース、およびユーザーを管理するアプリケーションを作成するために Azure Java SDK を使用する方法について説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 08/20/2019
ms.custom: devx-track-java
ms.openlocfilehash: 86e84d825bd1dd3774cfd2774e0b96e540bb3cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218478"
---
# <a name="manage-azure-data-lake-analytics-using-a-java-app"></a>Java アプリケーション を使用して Azure Data Lake Analytics を管理する
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

このアーティクルでは、 Azure Java SDK を使用して作成したアプリを使用して、Azure Data Lake Analytics アカウント、データ ソース、ユーザー、ジョブを管理する方法について説明します。 

## <a name="prerequisites"></a>前提条件
* **Java Development Kit (JDK) 8** (Java バージョン 1.8 を使用)。
* **IntelliJ** または別の適切な Java 開発環境。 このドキュメントの説明では IntelliJ を使います。
* Azure Active Directory (AAD) アプリケーションを作成し、**そのクライアント ID**、**テナント ID**、および **キー** を取得します。 AAD アプリケーションと、クライアント ID の取得方法の詳細については、 [ポータルを使用した Active Directory のアプリケーションとサービス プリンシパルの作成](../active-directory/develop/howto-create-service-principal-portal.md)に関するページを参照してください。 アプリケーションを作成しキーを生成したら、応答 URI とキーをポータルから使うことができます。

## <a name="authenticating-using-azure-active-directory"></a>Azure Active Directory を使用した認証

次のコード スニペットは、アプリケーションが独自の資格情報を提供する、**非対話型** 認証用のコードを提供します。

## <a name="create-a-java-application"></a>Java アプリケーションの作成
1. IntelliJ を開き、**コマンド ライン アプリ** テンプレートを使って Java プロジェクトを作成します。
2. 画面の左側にあるプロジェクトを右クリックし、 **[フレームワーク サポートの追加]** をクリックします。 **[Maven]** を選択し、 **[OK]** をクリックします。
3. 新しく作成された **"pom.xml"** ファイルを開き、 **\</version>** タグと **\</project>** タグの間に、次のテキストのスニペットを追加します。

```xml
<dependencies>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-client-authentication</artifactId>
        <version>1.6.12</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-client-runtime</artifactId>
        <version>1.6.12</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.rest</groupId>
        <artifactId>client-runtime</artifactId>
        <version>1.6.12</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-mgmt-datalake-store</artifactId>
        <version>1.22.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-mgmt-datalake-analytics</artifactId>
        <version>1.22.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.3.6</version>
        <exclusions>
            <exclusion>
                <groupId>com.fasterxml.jackson.core</groupId>
                <artifactId>jackson-core</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
```

**[File]\(ファイル\) > [Settings]\(設定\) > [Build]\(ビルド\) > [Execution]\(実行\) > [Deployment]\(デプロイ\)** に移動します。 **[Build Tools]\(ビルド ツール\) > [Maven] > [Importing]\(インポート\)** を選択します。 次に、 **[Maven プロジェクトを自動的にインポートする]** をオンにします。

`Main.java` を開き、既存のコード ブロックを次のコードに置き換えます。

```java
import com.microsoft.azure.CloudException;
import com.microsoft.azure.credentials.ApplicationTokenCredentials;
import com.microsoft.azure.datalake.store.*;
import com.microsoft.azure.datalake.store.oauth2.*;
import com.microsoft.azure.management.datalake.analytics.implementation.*;
import com.microsoft.azure.management.datalake.store.*;
import com.microsoft.azure.management.datalake.store.implementation.*;
import com.microsoft.azure.management.datalake.store.models.*;
import com.microsoft.azure.management.datalake.analytics.*;
import com.microsoft.azure.management.datalake.analytics.models.*;
import com.microsoft.rest.credentials.ServiceClientCredentials;

import java.io.*;
import java.nio.charset.Charset;
import java.util.ArrayList;
import java.util.UUID;
import java.util.List;

public class Main {
    private static String _adlsAccountName;
    private static String _adlaAccountName;
    private static String _resourceGroupName;
    private static String _location;

    private static String _tenantId;
    private static String _subscriptionId;
    private static String _clientId;
    private static String _clientSecret;

    private static DataLakeStoreAccountManagementClient _adlsClient;
    private static ADLStoreClient _adlsStoreClient;
    private static DataLakeAnalyticsAccountManagementClient _adlaClient;
    private static DataLakeAnalyticsJobManagementClient _adlaJobClient;

    public static void main(String[] args) throws Exception {
        _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
        _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>";
        _resourceGroupName = "<RESOURCE-GROUP-NAME>";
        _location = "East US 2";

        _tenantId = "<TENANT-ID>";
        _subscriptionId =  "<SUBSCRIPTION-ID>";
        _clientId = "<CLIENT-ID>";

        _clientSecret = "<CLIENT-SECRET>";

        String localFolderPath = "C:\\local_path\\";

        // ----------------------------------------
        // Authenticate
        // ----------------------------------------
        ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
        SetupClients(creds);

        // ----------------------------------------
        // List Data Lake Store and Analytics accounts that this app can access
        // ----------------------------------------
        System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subscriptionId));
        List<DataLakeStoreAccountBasic> adlsListResult = _adlsClient.accounts().list();
        for (DataLakeStoreAccountBasic acct : adlsListResult) {
            System.out.println(acct.name());
        }

        System.out.println(String.format("All ADL Analytics accounts that this app can access in subscription %s:", _subscriptionId));
        List<DataLakeAnalyticsAccountBasic> adlaListResult = _adlaClient.accounts().list();
        for (DataLakeAnalyticsAccountBasic acct : adlaListResult) {
            System.out.println(acct.name());
        }
        WaitForNewline("Accounts displayed.", "Creating files.");

        // ----------------------------------------
        // Create a file in Data Lake Store: input1.csv
        // ----------------------------------------
        CreateFile("/input1.csv", "123,abc", true);
        WaitForNewline("File created.", "Submitting a job.");

        // ----------------------------------------
        // Submit a job to Data Lake Analytics
        // ----------------------------------------
        String script = "@input = EXTRACT Row1 string, Row2 string FROM \"/input1.csv\" USING Extractors.Csv(); OUTPUT @input TO @\"/output1.csv\" USING Outputters.Csv();";
        UUID jobId = SubmitJobByScript(script, "testJob");
        WaitForNewline("Job submitted.", "Getting job status.");

        // ----------------------------------------
        // Wait for job completion and output job status
        // ----------------------------------------
        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
        System.out.println("Waiting for job completion.");
        WaitForJob(jobId);
        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
        WaitForNewline("Job completed.", "Downloading job output.");

        // ----------------------------------------
        // Download job output from Data Lake Store
        // ----------------------------------------
        DownloadFile("/output1.csv", localFolderPath + "output1.csv");
        WaitForNewline("Job output downloaded.", "Deleting file.");

        DeleteFile("/output1.csv");
        WaitForNewline("File deleted.", "Done.");
    }

    public static void SetupClients(ServiceClientCredentials creds) {
        _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
        _adlaClient = new DataLakeAnalyticsAccountManagementClientImpl(creds);
        _adlaJobClient = new DataLakeAnalyticsJobManagementClientImpl(creds);
        _adlsClient.withSubscriptionId(_subscriptionId);
        _adlaClient.withSubscriptionId(_subscriptionId);

        String authEndpoint = "https://login.microsoftonline.com/" + _tenantId + "/oauth2/token";
        AccessTokenProvider provider = new ClientCredsTokenProvider(authEndpoint, _clientId, _clientSecret);
        _adlsStoreClient = ADLStoreClient.createClient(_adlsAccountName + ".azuredatalakestore.net", provider);
    }

    public static void WaitForNewline(String reason, String nextAction) {
        if (nextAction == null)
            nextAction = "";

        System.out.println(reason + "\r\nPress ENTER to continue...");
        try {
            System.in.read();
        } catch (Exception e) {
        }

        if (!nextAction.isEmpty()) {
            System.out.println(nextAction);
        }
    }

    // Create accounts
    public static void CreateAccounts() throws InterruptedException, CloudException, IOException {
        // Create ADLS account
        CreateDataLakeStoreAccountParameters adlsParameters = new CreateDataLakeStoreAccountParameters();
        adlsParameters.withLocation(_location);

        _adlsClient.accounts().create(_resourceGroupName, _adlsAccountName, adlsParameters);

        // Create ADLA account
        AddDataLakeStoreWithAccountParameters adlsInfo = new AddDataLakeStoreWithAccountParameters();
        adlsInfo.withName(_adlsAccountName);

        List<AddDataLakeStoreWithAccountParameters> adlsInfoList = new ArrayList<>();
        adlsInfoList.add(adlsInfo);

        CreateDataLakeAnalyticsAccountParameters adlaParameters = new CreateDataLakeAnalyticsAccountParameters();
        adlaParameters.withLocation(_location);
        adlaParameters.withDefaultDataLakeStoreAccount(_adlsAccountName);
        adlaParameters.withDataLakeStoreAccounts(adlsInfoList);

        _adlaClient.accounts().create(_resourceGroupName, _adlaAccountName, adlaParameters);
    }

    // Create a file
    public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
        byte[] bytesContents = contents.getBytes();

        ADLFileOutputStream stream = _adlsStoreClient.createFile(path, IfExists.OVERWRITE, "777", force);
        stream.write(bytesContents);
        stream.close();
    }

    // Delete a file
    public static void DeleteFile(String filePath) throws IOException, CloudException {
        _adlsStoreClient.delete(filePath);
    }

    // Download a file
    private static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
        ADLFileInputStream stream = _adlsStoreClient.getReadStream(srcPath);

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
            fileContents = writer.toString();
        }

        pWriter.println(fileContents);
        pWriter.close();
    }

    // Submit a U-SQL job
    private static UUID SubmitJobByScript(String script, String jobName) throws IOException, CloudException {
        UUID jobId = java.util.UUID.randomUUID();
        CreateJobProperties properties = new CreateUSqlJobProperties();
        properties.withScript(script);
        CreateJobParameters parameters = new CreateJobParameters();
        parameters.withName(jobName);
        parameters.withType(JobType.USQL);
        parameters.withProperties(properties);

        JobInformation jobInfo = _adlaJobClient.jobs().create(_adlaAccountName, jobId, parameters);

        return jobInfo.jobId();
    }

    // Wait for job completion
    private static JobResult WaitForJob(UUID jobId) throws IOException, CloudException {
        JobInformation jobInfo = _adlaJobClient.jobs().get(_adlaAccountName, jobId);
        while (jobInfo.state() != JobState.ENDED) {
            jobInfo = _adlaJobClient.jobs().get(_adlaAccountName, jobId);
        }
        return jobInfo.result();
    }

    // Retrieve job status
    private static String GetJobStatus(UUID jobId) throws IOException, CloudException {
        JobInformation jobInfo = _adlaJobClient.jobs().get(_adlaAccountName, jobId);
        return jobInfo.state().toString();
    }
}
```

コード スニペットで参照されているパラメーターの値を指定します。
* `localFolderPath`
* `_adlaAccountName`
* `_adlsAccountName`
* `_resourceGroupName`
* `_tenantId`
* `_subId`
* `_clientId`
* `_clientSecret`

## <a name="next-steps"></a>次のステップ

* U-SQL の詳細については、「[チュートリアル: Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」および「[U-SQL Language Reference (U-SQL 言語リファレンス)](/u-sql/)」をご覧ください。
* 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
* Data Lake Analytics の概要については、「 [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。