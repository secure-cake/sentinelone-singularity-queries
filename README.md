# SentinelOne Singularity "Data Lake" Cheatsheet and Queries

## Comparison Operators: 

https://usea1-017.sentinelone.net/docs/en/operators-2545125.html

### And, Or, Not: “implicit AND” when combining query statements
```
  event.type = ‘Process Creation’ src.process.name = ‘svchost.exe’ 	
```
   (no “AND” required) [CASE Sensitive keywords!]
```
  event.type = ‘Process Creation’ and not (src.process.name = ‘svchost.exe’)
```
``` 
  event.type = 'Process Creation' and not (src.process.name = 'svchost.exe' or src.process.name = 'services.exe')
```
``` 
  event.type = 'DNS Resolved' src.process.name != 'svchost.exe'		
``` 
   (‘!=’ synonymous with ‘AND NOT’)

### Contains:
```
 event.type = 'DNS Resolved' and event.dns.response contains 'google'		 
 indicator.name='ServiceCreate' indicator.metadata contains 'BITS'
```
[case insensitive by default]

### In:
```
event.type='IP Connect' dst.ip.address in ('192.168.1.190', '192.168.1.192') event.network.connectionStatus ='FAILURE' event.network.direction = 'OUTGOING'
``` 

### Value Ranges: 
```
event.type='IP Connect' src.ip.address >'192.168.21.1' src.ip.address < '192.168.21.100' event.network.connectionStatus='SUCCESS' dst.port.number=445
```

### Regex: 
https://usea1-017.sentinelone.net/docs/en/regular-expressions.html

### Wildcards: 
```
 * contains ‘.ps1’
```

## Topical Search Examples:

### Fields?: what fields are avaiable?
``` 
indicator.name contains ‘service’
```
```
indicator.name contains ‘task’
```
```
event.type contains ‘connect’
```

## Categorical Search Examples:

### Executable File Creation Events:
```
event.type = 'File Creation' tgt.file.extension in ('dll','exe','ps1','bat','cmd','js')
```
```
event.type = 'File Creation' tgt.file.extension in ('dll','exe','ps1','bat','cmd','js') tgt.file.path contains 'C:\\Windows\\Temp'
```

### Archive File Creation: 
```
event.type = 'File Creation' tgt.file.extension in ('zip','7z','gzip')
```

### Process Creation: 
```
event.type = 'Process Creation' and (src.process.cmdline contains 'anydesk' or tgt.process.cmdline contains 'anydesk')
```
NOTE: psexec, screenconnect, 7z, 

### Network (DNS, IP Connect):
```
event.url.action='GET' url.address contains '.zip'
```
```
event.url.action='POST' src.process.cmdline contains 'ps1'
```
```
event.type='IP Connect' src.ip.address='192.168.21.37' event.network.connectionStatus='SUCCESS' dst.port.number=445
dst.ip.address matches "^([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])(?<!172\.(16|17|18|19|20|21|22|23|24|25|26|27|28|29|30|31))(?<!127)(?<!^10)(?<!^0)\.([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])(?<!192\.168)(?<!172\.(16|17|18|19|20|21|22|23|24|25|26|27|28|29|30|31))\.([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\.([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])$"
dst.ip.address matches "^([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])(?<!172\.(16|17|18|19|20|21|22|23|24|25|26|27|28|29|30|31))(?<!127)(?<!^10)(?<!^0)\.([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])(?<!192\.168)(?<!172\.(16|17|18|19|20|21|22|23|24|25|26|27|28|29|30|31))\.([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\.([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])$" src.process.name != 'EXCEL.EXE' src.process.name != 'services.exe' src.process.name != 'explorer.exe'
```
NOTE: Searches for public IPv4 addresses; make sure and add “columns” – dst.ip.address, src.ip.address, src and dst ports to your view/export
```
event.type = 'DNS Resolved' and event.dns.response contains 'google'
```

### Scheduled Tasks:
```
event.type = 'Task Register' osSrc.process.cmdline contains ('wscript','cmd','powershell')
```
```
event.type = 'Task Register' src.process.parent.cmdline contains 'OneDrive' osSrc.process.cmdline contains 'svchost.exe'
```

### Windows Services:
```
indicator.name = 'ServiceCreate' and task.serviceName contains 'update'
```
```
indicator.name = 'ServiceCreate' src.process.parent.user != 'NT AUTHORITY\\SYSTEM'
```
