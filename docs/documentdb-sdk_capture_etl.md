# Capture through diagnositc trace listener
Traces are emitted with DocDBTrace source. Below sample app.config configuration captures the traces to a text listener
<system.diagnostics>
    <switches>
      <add name="SourceSwitch" value="Verbose"/>
    </switches>
    <sources>
      <source name="DocDBTrace" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" >
        <listeners>
          <add name="MyTextListener" type="System.Diagnostics.TextWriterTraceListener" traceOutputOptions="DateTime,ProcessId,ThreadId" initializeData="CosmosDBTrace.txt"></add>
       < /listeners>
      </source>
    </sources>
  </system.diagnostics> 

Please note that tracing everything will impact the application performance. Be selective and is possible collect them only when required. 


# Capture the .net SDK trace in ETL file

The Azure DocumentDB .NET SDK (from version 1.11.0) support capturing trace in ETL trace log file. The trace is emitted and captured using Windows ETW with very little overhead.

## Method 1: (Recommended) Use Perfview to collect and view ETL.
### Steps to collect and viewETL trace file.
1. Download Perfview tool at  https://www.microsoft.com/en-us/download/details.aspx?id=28567
2. In Windows command prompt:

		perfview.exe /onlyproviders=B30ABF1C-6A50-4F2B-85C4-61823ED6CF24:*:0  collect
3. 	When it is done, click  “Stop Collection” button.  “PerviewData.etl.zip” will be generated in the current directory.
4.  To view the trace,  double Click the PerviewData.etl.zip, go to Events, and look for "Providers/B30ABF1C-6A50-4F2B-85C4-61823ED6CF24"

### Monitor the trace in real time.

1. In Windows command prompt:

		perfview.exe UserCommand Listen B30ABF1C-6A50-4F2B-85C4-61823ED6CF24:*:0  

## Method 2: Use logman and svcperf to collect and view ETL.

### Steps to collect ETL trace file.

1.  In Windows command prompt:
 
		logman create trace docdbclienttrace -rt -nb 500 500 -bs 40 -p {B30ABF1C-6A50-4F2B-85C4-61823ED6CF24} -o docdbclient.etl -ft 10
2. When you are ready to collect trace:

		logman start docdbclienttrace

3. When finish collecting:

		logman stop docdbclienttrace

4. You should see docdbclientxxx.etl in current directory.

### Steps to open ETL if you are interested:
 1.  Download the svcperf at [https://svcperf.codeplex.com/](https://svcperf.codeplex.com/)
 2.  Open svcperf.exe.
 3.  In "Manifests|Add", add the ClientEvents.man found in this folder with this 
 4.  Open the etl file you have captured, you might need to hit F5 to refresh it.

## You can send the ETL file to docdb team for analysis. 
