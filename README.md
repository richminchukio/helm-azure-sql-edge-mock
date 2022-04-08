# azure-sql-edge-mock

Simply provide a dacpac for your mock database, and the azure-sql-edge-mock chart will create and dacpac deploy your database for you.

## SKAFFOLD/HELM USAGE

Create a new .Net core sqlproj in your application code repo, and add some tables and procs.

```sh
dotnet new --install "MSBuild.Sdk.SqlProj.Templates"
dotnet new sqlproj -n MyDb
dotnet sln add MyDb
cd MyDb
dotnet new table -s dbo -n myTable
dotnet new sproc -s dbo -n myProc
```

create a skaffold yaml to build your dacpac and helm install the chart

```yaml
# skaffold.yaml
apiVersion: skaffold/v2beta25
kind: Config
metadata:
  name: my-app
build:
  artifacts:
    hooks:
      after:
      - command: ["dotnet", "build"] # make dacpac
deploy:
  helm:
    releases:
    - name: my-app
      remoteChart: https://raw.githubusercontent.com/richminchukio/helm-azure-sql-edge-mock/main/
      version: 1.0.0
      setFiles:
        "configmap.data.init\\.dacpac": ./MyDb/bin/Debug/netstandard2.0/MyDb.dacpac
```

Add the connection string to your application.

```cs
// Program.cs
//// for in-cluster communication.
private static string _connString = @"
  server=my-app-azure-sql-edge-mock;
  Initial Catalog=MyDb;
  User Id=sa;
  Password=asdfASDF1234!@#$;
  Encrypt=Yes;
  TrustServerCertificate=Yes;
";
```
