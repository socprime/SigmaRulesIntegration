#  Sigma Rules Integration


Sigma is a new revolutionary way to make better and faster threat detection content for SIEM! Sigma Rules Integration Pack is a free package developed by SOC Prime for integrating Sigma rules into ArcSight ESM, Command Center and Logger for threat hunting purposes. Content includes SOC-ready dashboard, SOC channel and Flex connector to enrich and pickup Sigma search results.

An online Sigma UI tool including spell-check and exporter to most common SIEM platforms is free at https://tdm.socprime.com/sigma/

Sigma is a generic and open signature format that allows you to describe relevant log events in a straight forward manner. The rule format is very flexible, easy to write and applicable to any type of log file. The main purpose of this project is to provide a structured form in which researchers or analysts can describe their once developed detection methods and make them shareable with others. Sigma is for log files what Snort is for network traffic and YARA is for files. Sigma is a private project mainly developed by Florian Roth and Thomas Patzke with feedback from many fellow analysts and friends. “Rules are our own or have been derived from blog posts, tweets or other public sources that are referenced in the rules.”

Sigma Use Cases

- Describe your once discovered detection method in Sigma to make it shareable
- Share the signature in the appendix of your analysis along with file hashes and C2 servers
- Share the signature in threat intel communities - e.g. via MISP
- Provide Sigma signatures for malicious behavior in your own application (Error messages, access violations, manipulations)
- Integrate a new log into your SIEM and check the Sigma repository for available rules
- Write a rule converter for your custom log analysis tool and process new Sigma rules automatically
- Provide a free or commercial feed for Sigma signatures

Official Sigma repository: https://github.com/Neo23x0/sigma

Sigma Rules Integration Pack comes with parser and map file for ArcSight FlexConnector Multiple Folder File, that parse and enrich data with information from Sigma rules.

You can download ArcSight package and detailed instructions how to use it from https://tdm.socprime.com/use-case-library/info/514/

## How it Works

In TDM you can generate ArcSight Searches from existing sigma rules or from your custom. Then you check, save and schedule Saved Search in ArcSight Command Center or in Logger web console with saving output to local directory. After this Flex connector reads csv result files, parse it, enrich with more detailed data from sigma rule and send to the ESM.

![alt text](https://raw.githubusercontent.com/socprime/SigmaRulesIntegration/master/Sigma_Rules_Integration_ArcSight_Architecture.jpg)

## Installation 

1. Install on the Logger or ESM server ArcSight FlexConnector Multiple Folder File with the ESM, on which you will install analytical package, as destination.
2. Configure Flex Connector to monitor folder /opt/arcsight/logger/userdata/logger/user/logger/data/savedsearch with the parser sigma_csv.sdkfilereader.properties that is included in the package. Put file sigma_csv.sdkfilereader.properties to ../current/user/agent/flexagent/ folder.
3. Add map.0.properties file to Flex Connector. Put file map.0.properties to ../current/user/agent/map/ folder.
4. Install Connector as a service.
5. Download content package SOC_Prime_Sigma_Rules_Integration_Pack_vx.x.arb for ArcSight ESM from https://tdm.socprime.com/use-case-library/info/514/. Import package to ArcSight Manager with ArcSight Console.

## How to add new Sigma rule

1. Go to SOC Prime TDM https://tdm.socprime.com/sigma/ 
2. Choose needed sigma rule.
3. Generate ArcSight Saved Search and copy paste it to ArcSight Command Center or Logger Search.
4. Save query as a Saved Search and schedule it:
  - [x] Put query to search field and choose needed time frame:
```Query example:```
```
deviceVendor = "ArcSight" and deviceProduct = "ArcSight" and deviceEventClassId = "monitor:146" | cef flexString1 | rex field = flexString1 mode=sed "s//Sigma: Test Rule/g"
```
   - [x] Save query as a Saved Search with the name 'Sigma: RULE NAME'. Where RULE NAME is Sigma Rule Name from TDM. Choose option 'Schedule it'.
   - [x] In Schedule parameters please choose:
     - Specify time when saved search should run, 
     - choose option 'Save to ArcSight Command Center'
     - leave 'Export directory name' empty
     - Uncheck option 'All Fields'
     - Put to 'Fields' list of fields:
```
endTime,name,sourceAddress,destinationAddress,priority,deviceVendor,deviceProduct,categoryBehavior,categoryDeviceGroup,categoryOutcome,categorySignificance,destinationHostName,destinationPort,destinationProcessName,destinationServiceName,destinationUserId,destinationUserName,deviceAction,deviceAddress,deviceHostname,deviceProcessName,deviceCustomNumber1,deviceCustomNumber1Label,deviceCustomNumber2,deviceCustomNumber2Label,deviceCustomString1,deviceCustomString1Label,deviceCustomString2,deviceCustomString2Label,deviceCustomString3,deviceCustomString3Label,deviceCustomString4,deviceCustomString4Label,deviceCustomString5,deviceCustomString5Label,deviceCustomString6,deviceCustomString6Label,fileName,filePath,flexString1,flexString1Label,flexString2,flexString2Label,sourceHostName,sourcePort,sourceProcessName,sourceServiceName,sourceUserId,sourceUserName
```
```Note: Parser is configured to parse files in this field sequence. Don’t change fields.```
```In case you need to add new fields contact SOC Prime.```
   - [x] Choose option 'Include only CEF events'.
   - [x] Enable 'Sigma: RULE NAME job'.

## How to test

Check whether you have configured everything correct by creating test Sigma Rule Job. In case everything is configured well you should see test events in the ESM console.
  1. Go to Logger or ArcSight Command Center web console Event Search page.
  2. Put test query to search field and choose time frame 'Last 10 minutes':
```
deviceVendor = "ArcSight" and deviceProduct = "ArcSight" and deviceEventClassId = "monitor:146" | cef flexString1 | rex field = flexString1 mode=sed "s//Sigma: Test Rule/g"
```
You should see 10 events with changed flexString1 field to 'Sigma: Test Rule'.
	3. Save query as a Saved Search with the name 'Sigma: Test Rule'. Choose option 'Schedule it'.
	4. In Schedule parameters please choose:
   - [x] to run every 15 minutes, 
   - [x] choose option 'Save to ArcSight Command Center'
   - [x] leave 'Export directory name' empty
   - [x] Uncheck option 'All Fields'”
   - [x] Put to 'Fields' list of fields:
```
endTime,name,sourceAddress,destinationAddress,priority,deviceVendor,deviceProduct,categoryBehavior,categoryDeviceGroup,categoryOutcome,categorySignificance,destinationHostName,destinationPort,destinationProcessName,destinationServiceName,destinationUserId,destinationUserName,deviceAction,deviceAddress,deviceHostname,deviceProcessName,deviceCustomNumber1,deviceCustomNumber1Label,deviceCustomNumber2,deviceCustomNumber2Label,deviceCustomString1,deviceCustomString1Label,deviceCustomString2,deviceCustomString2Label,deviceCustomString3,deviceCustomString3Label,deviceCustomString4,deviceCustomString4Label,deviceCustomString5,deviceCustomString5Label,deviceCustomString6,deviceCustomString6Label,fileName,filePath,flexString1,flexString1Label,flexString2,flexString2Label,sourceHostName,sourcePort,sourceProcessName,sourceServiceName,sourceUserId,sourceUserName
```
```Note: Parser is configured to parse files in this field sequence. Don’t change fields.```
```In case you need to add new fields contact SOC Prime.```
   - [x] Choose option 'Include only CEF events'. 
  5. Enable 'Sigma: Test Rule job'. 
  6. In 15 minutes Saved Search should run and save output to csv file into the /opt/arcsight/logger/userdata/logger/user/logger/data/savedsearch directory, which Flex Connector (installed in p.1) will processed that file and send event to the ESM.



