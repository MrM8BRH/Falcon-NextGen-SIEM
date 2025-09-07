```yaml
sources:  
  Fslogixlog: 
   type: file 
   include:  
    - \ProgramData\FSLogix\Logs\ODFC\** 
    - \ProgramData\FSLogix\Logs\AdsComputerGroup\** 
    - \ProgramData\FSLogix\Logs\CloudCacheService\** 
    - \ProgramData\FSLogix\Logs\DriverInterface\** 
    - \ProgramData\FSLogix\Logs\Font\** 
    - \ProgramData\FSLogix\Logs\Network\** 
    - \ProgramData\FSLogix\Logs\Printer\** 
    - \ProgramData\FSLogix\Logs\Profile\** 
    - \ProgramData\FSLogix\Logs\RuleCompilation\** 
    - \ProgramData\FSLogix\Logs\Search\** 
    - \ProgramData\FSLogix\Logs\SearchPlugin\** 
    - \ProgramData\FSLogix\Logs\Service\** 
    - \ProgramData\FSLogix\Logs\UsermodeModule\** 
   sink: fx 

sinks: 
  fx: 
    type: hec 
    token: *** 
    url: ***
```
