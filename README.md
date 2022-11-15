# 使用NLog紀錄程式日誌

1 編輯Convience.ManagentApi.csprog，在ItemGroup段落加入NLog主要的套件
```
<ItemGroup>
    <PackageReference Include="NLog" Version="4.7.9" />
    <PackageReference Include="NLog.Web.AspNetCore" Version="4.12.0" />
</ItemGroup>
```

2 在根目錄建立nlog.config，此為NLog的設定檔。
```
<?xml version="1.0" encoding="utf-8" ?>
<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      autoReload="true">

  <!-- enable asp.net core layout renderers -->
  <extensions>
    <add assembly="NLog.Targets.ElasticSearch"/>
    <add assembly="NLog.Web.AspNetCore"/>
  </extensions>

  <!-- the targets to write to -->
  <targets async="true">

    <!-- 本地文件日志target -->
    <target xsi:type="File" name="allfile" archiveAboveSize="10485760"
            fileName="${basedir}/Logs/${machinename}-all-${shortdate}/${shortdate}.log"
            layout="${longdate}|${event-properties:item=EventId_Id}|${uppercase:${level}}|${logger}|${message} ${exception:format=tostring}" />

  </targets>

  <!-- rules to map from logger name to target -->
  <rules>
    <!--All logs, including from Microsoft-->
    <logger name="*" minlevel="Trace" writeTo="allfile" />
    <logger name="*" minlevel="Trace" writeTo="ElasticSearch" />

    <!--Skip non-critical Microsoft logs and so log only own logs-->
    <logger name="Microsoft.*" maxlevel="Info" final="true" />
    <!-- BlackHole without writeTo -->
    <logger name="*" minlevel="Trace" writeTo="ownFile-web" />
  </rules>
</nlog>
```
