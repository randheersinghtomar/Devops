Host Down Alert.

# Host_Down Alert Flow

```text
Host_Down Alert
       |
       v
    Try SSH
       |
   +---+---+
   |       |
Success   Failed
   |       |
Check     Login to iDRAC
server    + Virtual Console
             |
             v
      Does server respond?
         /          \
       Yes           No
        |             |
        v             v
  Check Console    Check Power
  Screen           & iDRAC Health
        |             |
        v             v
  Check OS Health   Check Hardware
  CPU/Memory/       & iDRAC Logs
  Disk/Logs              |
        |                v
        v             Reboot /
  Check SSH &        Power Cycle
  Network                 |
        |                 |
        v                 |
  Fix the Issue           |
  - Restart Service       |
  - Resolve Network       |
  - Troubleshoot OS       |
        |                 |
        v                 |
  Is Reboot Required?     |
      /       \           |
    Yes        No         |
     |          |         |
     v          |         |
Controlled      |         |
Reboot /        |         |
Power Cycle     |         |
     |          |         |
     +----------+---------+
                |
                v
        Server Recovered?
           /          \
         Yes           No
          |             |
          v             v
     Verify Ping,    Escalate to
     SSH, Services   On-Call /
     & Monitoring    Opsgenie
          |
          v
       Close Alert


