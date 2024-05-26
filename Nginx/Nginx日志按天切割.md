```
#!/bin/bash
Date=`date -d '-1 day' '+%Y-%m-%d'`
cd /var/log/nginx  &&   mkdir logs/$Date
for i in access.log error.log
do
gzip -c $i  > logs/$Date/"$i"_"$Date".gz
echo " " >  $i
find logs/ -ctime +30 | xargs rm -rf
done
```