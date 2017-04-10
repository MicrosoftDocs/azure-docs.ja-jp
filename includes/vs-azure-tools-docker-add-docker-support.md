1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[追加]、[Docker Support (Docker サポート)]** の順に選択します。
   
    ![Docker サポート コンテキスト メニューの追加](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)
2. ASP.NET Core Web プロジェクトに Docker サポートを追加すると、プロジェクトに追加されているいくつかの Docker 関連ファイル (Docker-Compose ファイル、デプロイの Windows PowerShell スクリプト、および Docker プロパティ ファイルなど) が追加されます。 
   
    ![プロジェクトに追加された Docker ファイル](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)

> [!NOTE]
> [Docker for Windows のベータ版](https://beta.docker.com)を使用している場合は、Properties\Docker.props を開き、既定値を削除して、Visual Studio を再起動すると、値が反映されます。
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=='' "></DockerMachineName>
> ```
> 



<!--HONumber=Nov16_HO3-->


