---
title: Application Insights のリリース注釈 | Microsoft Docs
description: Application Insights で、メトリックス エクスプローラーのグラフにデプロイ マーカーまたはビルド マーカーを追加します。
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 776efd56aaa523d1c2621c51cba0446a42bb7411
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461914"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights のメトリック グラフの注釈

注釈は、新しいビルドのデプロイ先やその他の重要なイベントを示します。 注釈により、変更内容がアプリケーションのパフォーマンスに影響を与えたかどうかを簡単に把握できます。 それらは、[Azure Pipelines](/azure/devops/pipelines/tasks/) ビルド システムで自動的に作成できます。 PowerShell から作成することにより、任意のイベントにフラグを設定する注釈を作成することもできます。

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines ビルドでのリリース注釈

リリース注釈は、Azure DevOps のクラウド ベースの Azure Pipelines サービスの機能です。

### <a name="install-the-annotations-extension-one-time"></a>注釈拡張機能のインストール (1 回限り)

リリース注釈を作成できるようにするには、Visual Studio Marketplace で入手可能な Azure DevOps 拡張機能のいずれかをインストールする必要があります。

1. [Azure DevOps](https://azure.microsoft.com/services/devops/) プロジェクトにサインインします。
   
1. Visual Studio Marketplace の [リリース注釈拡張機能](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)のページで、自分の Azure DevOps 組織を選択し、 **[インストール]** を選択して Azure DevOps 組織に拡張機能を追加します。
   
   ![Azure DevOps 組織を選択してから、[インストール] を選択します。](./media/annotations/1-install.png)
   
Azure DevOps 組織に拡張機能をインストールする必要があるのは一度だけです。 これで、組織内の任意のプロジェクトに対してリリース注釈を構成できるようになります。

### <a name="configure-release-annotations"></a>リリース注釈を構成する

Azure Pipelines のリリース テンプレートごとに個別の API キーを作成します。

1. [Azure portal](https://portal.azure.com) にサインインし、アプリケーションを監視する Application Insights リソースを開きます。 または、まだない場合は、[新しい Application Insights リソースを作成](./app-insights-overview.md)します。
   
1. **[API アクセス]** タブを開き、 **[Application Insights ID]** をコピーします。
   
   ![[API アクセス] で、アプリケーション ID をコピーします。](./media/annotations/2-app-id.png)

1. 別のブラウザー ウィンドウで、Azure Pipelines のデプロイを管理するリリース テンプレートを開くか、作成します。
   
1. **[タスクの追加]** を選択した後、メニューから **[Application Insights Release Annotation]\(Application Insights リリース注釈\)** タスクを選択します。
   
   ![[タスクの追加] をクリックし、[Application Insights Release Annotation]\(Application Insights リリース注釈\) を選択する。](./media/annotations/3-add-task.png)

   > [!NOTE]
   > リリース注釈タスクは、現在、Windows ベースのエージェントのみをサポートしています。Linux、macOS、または他の種類のエージェントでは実行されません。
   
1. **[アプリケーション ID]** に、 **[API アクセス]** タブからコピーした Application Insights ID を貼り付けます。
   
   ![Application Insights ID を貼り付けます。](./media/annotations/4-paste-app-id.png)
   
1. Application Insights の **[API アクセス]** ウィンドウに戻り、 **[API キーの作成]** を選択します。 
   
   ![[API アクセス] タブで、[API キーの作成] を選択する。](./media/annotations/5-create-api-key.png)
   
1. **[API キーの作成]** ウィンドウで、説明を入力し、 **[コメントを書く]** を選択して、 **[キーの生成]** を選択します。 新しいキーをコピーします。
   
   ![[API キーの作成] ウィンドウで、説明を入力し、[コメントを書く] を選択して、[キーの生成] を選択します。](./media/annotations/6-create-api-key.png)
   
1. リリース テンプレート ウィンドウの **[変数]** タブで、 **[追加]** を選択して新しい API キーの変数定義を作成します。

1. **[名前]** に「`ApiKey`」と入力し、 **[値]** に **[API アクセス]** タブからコピーした API キーを貼り付けます。
   
   ![Azure DevOps の [変数] タブで、[追加] を選択し、変数名を ApiKey にして、[値] に API キーを貼り付ける。](./media/annotations/7-paste-api-key.png)
   
1. メイン リリース テンプレート ウィンドウで **[保存]** 選択して、テンプレートを保存します。


   > [!NOTE]
   > API キーに対する制限については、[REST API の割合の制限に関するドキュメント](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits)を参照してください。

## <a name="view-annotations"></a>注釈を表示する


   > [!NOTE]
   > リリース注釈は、Application Insights の [メトリック] ペインでは現在使用できません。

これで、このリリース テンプレートを使用して新しいリリースをデプロイするたびに、注釈が Application Insights に送信されるようになります。 注釈は次の場所に表示できます。

**使用状況** ペイン。ここではリリース注釈を手動で作成することもできます。

![一定時間内のユーザーのアクセス数を表示した棒グラフのスクリーンショット。 リリース注釈は、リリースが発生した時刻を示すグラフの上の緑色のチェックマークとして表示されます。](./media/annotations/usage-pane.png)

視覚化により x 軸に時間を表示したログベースのブック クエリ。

![注釈が表示された時系列ログベース クエリを示すブック ペインのスクリーンショット](./media/annotations/workbooks-annotations.png)

ブックの注釈を有効にするには、 **[詳細設定]** に移動して **[コメントを表示する]** を選択します。

![[詳細設定] メニューのスクリーンショット。[コメントを表示する] というテキストが強調表示され、有効にするために設定の横にチェックマークが付いています。](./media/annotations/workbook-show-annotations.png)

注釈マーカーを選択すると、要求元、ソース管理のブランチ、リリース パイプライン、環境を含む、リリースに関する詳細が表示されます。

## <a name="create-custom-annotations-from-powershell"></a>PowerShell からカスタム注釈を作成する
Azure DevOps を使わずに、GitHub の CreateReleaseAnnotation PowerShell スクリプトを使って、任意のプロセスから注釈を作成できます。

1. CreateReleaseAnnotation.ps1 のローカル コピーを作成します。

    ```powershell
    
    # Copyright (c) Microsoft Corporation. All rights reserved. 
    # Licensed under the MIT License. See License.txt in the project root for license information. 
    
    # Sample usage .\CreateReleaseAnnotation.ps1 -applicationId "<appId>" -apiKey "<apiKey>" -releaseFilePath "<path to .exe with file version>" -releaseProperties @{"ReleaseDescription"="Release with annotation";"TriggerBy"="John Doe"}
    param(
        [parameter(Mandatory = $true)][string]$applicationId,
        [parameter(Mandatory = $true)][string]$apiKey,
        [parameter(Mandatory = $true)][string]$releaseFilePath,
        [parameter(Mandatory = $false)]$releaseProperties
    )
    
    $releaseName = (Get-Item $releaseFilePath).VersionInfo.FileVersion
    Write-Host "Creating release annotation $releaseName in ApplicationInsights" -ForegroundColor Cyan
    
    # background info on how fwlink works: After you submit a web request, many sites redirect through a series of intermediate pages before you finally land on the destination page.
    # So when calling Invoke-WebRequest, the result it returns comes from the final page in any redirect sequence. Hence, I set MaximumRedirection to 0, as this prevents the call to 
    # be redirected. By doing this, we get a response with status code 302, which indicates that there is a redirection link from the response body. We grab this redirection link and 
    # construct the url to make a release annotation.
    # Here's how this logic is going to works
    # 1. Client send http request, such as:  http://go.microsoft.com/fwlink/?LinkId=625115
    # 2. FWLink get the request and find out the destination URL for it, such as:  http://www.bing.com
    # 3. FWLink generate a new http response with status code “302” and with destination URL “http://www.bing.com”. Send it back to Client.
    # 4. Client, such as a powershell script, knows that status code “302” means redirection to new a location, and the target location is “http://www.bing.com”
    function GetRequestUrlFromFwLink($fwLink)
    {
        $request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore
        if ($request.StatusCode -eq "302") {
            return $request.Headers.Location
        }
        
        return $null
    }
    
    function CreateAnnotation($grpEnv)
    {
        $retries = 1
        $success = $false
        while (!$success -and $retries -lt 6) {
            $location = "$grpEnv/applications/$applicationId/Annotations?api-version=2015-11"
                
            Write-Host "Invoke a web request for $location to create a new release annotation. Attempting $retries"
            set-variable -Name createResultStatus -Force -Scope Local -Value $null
            set-variable -Name createResultStatusDescription -Force -Scope Local -Value $null
            set-variable -Name result -Force -Scope Local
    
            try {
                $result = Invoke-WebRequest -Uri $location -Method Put -Body $bodyJson -Headers $headers -ContentType "application/json; charset=utf-8" -UseBasicParsing
            } catch {
                if ($_.Exception){
                    if($_.Exception.Response) {
                        $createResultStatus = $_.Exception.Response.StatusCode.value__
                        $createResultStatusDescription = $_.Exception.Response.StatusDescription
                    }
                    else {
                        $createResultStatus = "Exception"
                        $createResultStatusDescription = $_.Exception.Message
                    }
                }
            }
    
            if ($result -eq $null) {
                if ($createResultStatus -eq $null) {
                    $createResultStatus = "Unknown"
                }
                if ($createResultStatusDescription -eq $null) {
                    $createResultStatusDescription = "Unknown"
                }
            }
            else {
                    $success = $true                     
            }
    
            if ($createResultStatus -eq 409 -or $createResultStatus -eq 404 -or $createResultStatus -eq 401) # no retry when conflict or unauthorized or not found
            {
                break
            }
    
            $retries = $retries + 1
            sleep 1
        }
    
        $createResultStatus
        $createResultStatusDescription
        return
    }
    
    # Need powershell version 3 or greater for script to run
    $minimumPowershellMajorVersion = 3
    if ($PSVersionTable.PSVersion.Major -lt $minimumPowershellMajorVersion) {
       Write-Host "Need powershell version $minimumPowershellMajorVersion or greater to create release annotation"
       return
    }
    
    $currentTime = (Get-Date).ToUniversalTime()
    $annotationDate = $currentTime.ToString("MMddyyyy_HHmmss")
    set-variable -Name requestBody -Force -Scope Script
    $requestBody = @{}
    $requestBody.Id = [GUID]::NewGuid()
    $requestBody.AnnotationName = $releaseName
    $requestBody.EventTime = $currentTime.GetDateTimeFormats("s")[0] # GetDateTimeFormats returns an array
    $requestBody.Category = "Deployment"
    
    if ($releaseProperties -eq $null) {
        $properties = @{}
    } else {
        $properties = $releaseProperties    
    }
    $properties.Add("ReleaseName", $releaseName)
    
    $requestBody.Properties = ConvertTo-Json($properties) -Compress
    
    $bodyJson = [System.Text.Encoding]::UTF8.GetBytes(($requestBody | ConvertTo-Json))
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("X-AIAPIKEY", $apiKey)
    
    set-variable -Name createAnnotationResult1 -Force -Scope Local -Value $null
    set-variable -Name createAnnotationResultDescription -Force -Scope Local -Value ""
    
    # get redirect link from fwlink
    $requestUrl = GetRequestUrlFromFwLink("http://go.microsoft.com/fwlink/?prd=11901&pver=1.0&sbp=Application%20Insights&plcid=0x409&clcid=0x409&ar=Annotations&sar=Create%20Annotation")
    if ($requestUrl -eq $null) {
        $output = "Failed to find the redirect link to create a release annotation"
        throw $output
    }
    
    $createAnnotationResult1, $createAnnotationResultDescription = CreateAnnotation($requestUrl)
    if ($createAnnotationResult1) 
    {
         $output = "Failed to create an annotation with Id: {0}. Error {1}, Description: {2}." -f $requestBody.Id, $createAnnotationResult1, $createAnnotationResultDescription
         throw $output
    }
    
    $str = "Release annotation created. Id: {0}." -f $requestBody.Id
    Write-Host $str -ForegroundColor Green
    
    ```
   
1. 前の手順のステップを使って、Application Insights ID を取得し、Application Insights の **[API アクセス]** タブから API キーを作成します。
   
1. 次のコードで PowerShell スクリプトを呼び出します。角かっこのプレースホルダーは実際の値に置き換えます。 `-releaseProperties` は省略可能です。 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

過去に関する注釈を作成する場合など、スクリプトを変更できます。

## <a name="next-steps"></a>次のステップ

* [作業項目を作成する](./diagnostic-search.md#create-work-item)
* [PowerShell でのオートメーション](./powershell.md)

