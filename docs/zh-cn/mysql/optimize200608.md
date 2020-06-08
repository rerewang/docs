## 记一次sql性能调优

### 背景
需求是按照当前商品销量和历史销量，预估今天的总销量，具体逻辑如下:
- 假设当前时间为14点，今日为周一
- 取截止到当前时间的销售量，定义为 numberNow
- 取过去52周周一的总销量，定义为 number52
- 取过去52周周一0点到14点的销量，定义为 numberNow52
- 预计销量 = numberNow * (number52 / numberNow52)
故系统中的sql(两个sql执行时间加起来大概20s)如下：
```sql
#获取前52个周一总销量
SELECT sum(og.goods_number) as tmpcount
    FROM order_info oi
    INNER JOIN order_goods og ON og.order_id = oi.order_id
    WHERE oi.pay_time >= '{$52WeeksAgo}' 
        AND oi.pay_time <= '{$now}'
        AND date_format(oi.pay_time, '%w') = date_format('{$now}', '%w')
        AND date_format(oi.pay_time, '%H:%i:%s') <= '23:59:59'  
```
```sql
#获取前52个周一0点到14点总销量
SELECT sum(og.goods_number) as tmpcount
    FROM order_info oi
    INNER JOIN order_goods og ON og.order_id = oi.order_id
    WHERE oi.pay_time >= '{$52WeeksAgo}' 
        AND oi.pay_time <= '{$now}'
        AND date_format(oi.pay_time, '%w') = date_format('{$now}', '%w')
        AND date_format(oi.pay_time, '%H:%i:%s') <= date_format('{$now}', '%H:%i:%s')
```

### 优化点分析
首先，我们来看一下order_info和order_goods两张表的建表语句(已去掉不影响该案例的列)：
```sql
CREATE TABLE `order_info`
(
    `order_id`                       int(10) unsigned                 NOT NULL AUTO_INCREMENT,
    `order_sn`                       varchar(64)                      NOT NULL,
    `user_id`                        int(10) unsigned                 NOT NULL DEFAULT '0',
    `order_time`                     datetime                         NOT NULL DEFAULT '0000-00-00 00:00:00',
    `pay_status`                     int(10) unsigned                 NOT NULL DEFAULT '0',
    `pay_time`                       datetime                         NOT NULL DEFAULT '0000-00-00 00:00:00',
    PRIMARY KEY (`order_id`),
    UNIQUE KEY `order_sn` (`order_sn`),
    KEY `user_id` (`user_id`),
    KEY `pay_status` (`pay_status`),
    KEY `pay_time` (`pay_time`)
) ENGINE = InnoDB
  AUTO_INCREMENT = 2898690
  DEFAULT CHARSET = utf8;
```
```sql
CREATE TABLE `order_goods`
(
    `rec_id`                             int(10) unsigned                                    NOT NULL AUTO_INCREMENT,
    `order_id`                           int(10) unsigned                                    NOT NULL DEFAULT '0',
    `goods_id`                           int(10) unsigned                                    NOT NULL DEFAULT '0',
    `goods_number`                       smallint(5) unsigned                                NOT NULL DEFAULT '1',
    PRIMARY KEY (`rec_id`),
    KEY `order_id` (`order_id`),
    KEY `goods_id` (`goods_id`)
) ENGINE = InnoDB
  AUTO_INCREMENT = 5071298
  DEFAULT CHARSET = utf8
```
通过explian语句发现sql语句可能用得上pay_time的索引，但索引行数为1496012，而date_format(oi.pay_time, '%w')由于等号左边对字段做了函数处理，对索引的优化完全帮不上忙；

若只查询一天的数据，我们explain的结果，索引行数仅为2000多。

仔细看sql发现，其实我们只需要52天的数据，但sql却对一年的数据进行了筛选, 显然让mysql引擎做了300天的无用功；所以我们得出思路:能否只查出52天的数据呢？
#### 方案1：遍历52次
需要连接数据库52*2=104次，实测响应时间为18s左右，结果并不理想

#### 方案2：union
通过union一次查出52天的数据，单sql执行时间1s左右，接口响应时间优化为2-3s之间，代码如下:
```php
$weekDates = getWeekDates($weeks, $time);
$sqlDay = "
    select sum(og.goods_number) as tmpcount
    from order_info oi
        inner join order_goods og on oi.order_id = og.order_id
        inner join goods g on g.goods_id = og.goods_id
    where 1 = 1
      {$sql_where }
      {$sql_isolate_country_limit_oi}
      and oi.pay_time >= '{$begin}' 
      and oi.pay_time <= '{$time}'
 ";
$sqlNow = "
    select sum(og.goods_number) as tmpcount
    from order_info oi
      inner join order_goods og on oi.order_id = og.order_id
      inner join goods g on og.goods_id = g.goods_id 
    where 1 = 1
      {$sql_where }
      {$sql_isolate_country_limit_oi}
      and oi.pay_time >= '{$begin}' 
      and oi.pay_time <= '{$time}'
";
$nowHis = date('H:i:s', strtotime($time));
$sqlDayUnion = 'select sum(tmpcount) from (';
$sqlNowUnion = 'select sum(tmpcount) from (';
$keyLast = sizeof($weekDates)-1;
foreach ($weekDates as $key => $day) {
    $sqlWeeksDay = " and oi.pay_time >= '{$day} 00:00:00' and oi.pay_time <= '{$day} 23:59:59'" . PHP_EOL;
    $sqlWeeksNow = " and oi.pay_time >= '{$day} 00:00:00' and oi.pay_time <= '{$day} {$nowHis}'" . PHP_EOL;
    if ($key == $keyLast) {
        $sqlDayUnion .= $sqlDay . $sqlWeeksDay . PHP_EOL;
        $sqlNowUnion .= $sqlNow . $sqlWeeksNow . PHP_EOL;
    } else {
        $sqlDayUnion .= $sqlDay . $sqlWeeksDay . ' union all' . PHP_EOL;
        $sqlNowUnion .= $sqlNow . $sqlWeeksNow . ' union all' . PHP_EOL;
    }
}
$sqlDayUnion .= ' ) a';
$sqlNowUnion .= ' ) a';
$dressDay = $db->getOne($sqlDayUnion);
$dressNow = $db->getOne($sqlNowUnion);

private function getWeekDates($weeks, $time) {
    $i = 0;
    $dates = [];
    do {
        $i ++;
        $weekStr = '-' . $i . 'weeks';
        $dates[] = date('Y-m-d', strtotime($weekStr, $time));
    } while ($i <= $weeks);
    return $dates;
}
```