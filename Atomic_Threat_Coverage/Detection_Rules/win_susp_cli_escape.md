| Title        | Suspicious Commandline Escape |
|:-------------------|:------------------|
| Description        | Detects suspicious process that use escape characters |
| Tags               |   |
| ATT&amp;CK Tactic | ('Defense Evasion', 'TA0005')  |
| ATT&amp;CK Technique | T1140  |
| Dataneeded         | DN_0002_windows_process_creation_with_commandline_4688, DN_0003_windows_sysmon_process_creation_1 |
| Triggering         | T1140 |
| Severity Level     | low       |
| False Positives    | False positives depend on scripts and administrative tools used in the monitored environment |
| Development Status | experimental      |
| References         | https://twitter.com/vysecurity/status/885545634958385153, https://twitter.com/Hexacorn/status/885553465417756673, https://twitter.com/Hexacorn/status/885570278637678592, https://www.fireeye.com/blog/threat-research/2017/06/obfuscation-in-the-wild.html, http://www.windowsinspired.com/understanding-the-command-line-string-and-arguments-received-by-a-windows-program/ |
| Author             | juju4      |


## Detection Rules

### Sigma rule

```
action: global
title: Suspicious Commandline Escape
description: Detects suspicious process that use escape characters
status: experimental
references:
    - https://twitter.com/vysecurity/status/885545634958385153
    - https://twitter.com/Hexacorn/status/885553465417756673
    - https://twitter.com/Hexacorn/status/885570278637678592
    - https://www.fireeye.com/blog/threat-research/2017/06/obfuscation-in-the-wild.html
    - http://www.windowsinspired.com/understanding-the-command-line-string-and-arguments-received-by-a-windows-program/
author: juju4
tags:
    - attack.defense_evasion
    - attack.t1140
detection:
    selection:
        CommandLine: 
            #- '^'
            #- '@'
# 0x002D -, 0x2013 , 0x2014 , 0x2015 ― ... FIXME! how to match hexa form?
            # - '-'
            # - '―'
            #- 'c:/'
            - '<TAB>'
            - '^h^t^t^p'
            - 'h"t"t"p'
    condition: selection
falsepositives: 
    - False positives depend on scripts and administrative tools used in the monitored environment
level: low
---
# Windows Audit Log
logsource:
    product: windows
    service: security
    description: 'Requirements: Audit Policy : Detailed Tracking > Audit Process creation, Group Policy : Administrative Templates\System\Audit Process Creation'
detection:
    selection:
        EventID: 4688
---
# Sysmon
logsource:
    product: windows
    service: sysmon
detection:
    selection:
        EventID: 1

```











Graylog

```
b'(EventID:"4688" AND CommandLine:("<TAB>" "\\^h\\^t\\^t\\^p" "h\\"t\\"t\\"p"))\n(EventID:"1" AND CommandLine:("<TAB>" "\\^h\\^t\\^t\\^p" "h\\"t\\"t\\"p"))\n'
```
