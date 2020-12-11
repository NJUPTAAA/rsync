# rsync
Sync tools for NOJ test cases.

# Usage
For every instance of judgeserver, NOJ requires one rsync service.

After successfully deployed NOJ Main Service and NOJ Judge Server. We need to run rsync master in NOJ Main Service Server, the `docker-compose.yml` should looks like the following:

```yml
version: "3"
services:
  noj-rsync-master:
    image: njuptaaa/rsync
    container_name: noj-rsync
    volumes:
      - /path-to-noj/storage/test_case:/test_case
      - /path-to-noj/storage/logs:/log
    environment:
      - RSYNC_MODE=master
      - RSYNC_USER=ojrsync
      - RSYNC_PASSWORD=CHANGE_THIS_PASSWORD
    ports:
      - "0.0.0.0:873:873"
```

Then type `docker-compose -d up` and make master rsync service running.

Remember to let port `873` open and change the field `RSYNC_PASSWORD`.

Then we go to JudgeServer and the `docker-compose.yml` should looks like the following:

```yml
version: "3"
services:
  noj-rsync-slave:
    image: njuptaaa/rsync
    container_name: noj-rsync
    volumes:
      - /path-to-noj-judgeserver/data/backend/test_case:/test_case:ro
      - /path-to-noj-judgeserver/data/rsync_log:/log
    environment:
      - RSYNC_MODE=slave
      - RSYNC_USER=ojrsync
      - RSYNC_PASSWORD=CHANGE_THIS_PASSWORD
      - RSYNC_MASTER_ADDR=NOJ_MAIN_SERVICE_ADDR
```
Then type `docker-compose -d up` and make salve rsync service running.

Remember to change the field `RSYNC_PASSWORD` and fill your main service address to `RSYNC_MASTER_ADDR`, it should look like `192.168.0.2` or `acm.njupt.edu.cn`.

