shell
```
#!/bin/bash

PORT=0

while [ true ];do
    read -p "please enter the port that you want to minitor:" port
    if [ $port -gt 65536 ] ||  [ $port -lt 1 ];then
        echo 'the port you enter is not correctly'
    else
        PORT=$port
        break
    fi
done

while [ true ]; do
    if [ `netstat -anp 2> /dev/null | grep ${PORT} | wc -l` -gt 0 ] ;then
        echo 'service is online'
    else
        echo 'service is offline'
        cd /home/eko/share/live_admin/script
        php ./ws.php &
    fi
    sleep 3
done
```
利用Swoole定时器实现
```
class Server {
    const PORT = 8811;

    public function port() {
        $shell  =  "netstat -anp 2>/dev/null | grep ". self::PORT . " | grep LISTEN | wc -l";

        $result = shell_exec($shell);
        if($result != 1) {
            // 发送报警服务 邮件 短信
            /// todo
            echo date("Ymd H:i:s")."error".PHP_EOL;
        } else {
            echo date("Ymd H:i:s")."succss".PHP_EOL;
        }
    }
}

// nohup
swoole_timer_tick(2000, function($timer_id) {
    (new Server())->port();
    echo "time-start".PHP_EOL;
});
```