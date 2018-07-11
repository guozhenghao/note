# python 时间相关
- 获取格式化时间
    ```python
    now = datetime.datetime.now()
    # %A Monday %a Mon
    print n_days.strftime('%Y-%m-%d %H:%M:%S')
    ```
- 时间戳转时间
    ```python
    ts = 1530030336
    dt = time.strftime("%Y-%m-%d %H:%M:%S", time.localtime(ts))
    print(dt)
    ```
- 日期转时间戳
    ```python
    dt = '2018-01-01 10:40:30'
    ts = int(time.mktime(time.strptime(dt, "%Y-%m-%d %H:%M:%S")))
    print (ts)
    ```
- 时间加减
    ```python
    now = datetime.datetime.now()
    delta = datetime.timedelta(days=6)
    n_days = now - delta
    print n_days.strftime('%Y%m%d')
    ```
- 两个时间求差
    ```python
    d1 = datetime.datetime.strptime('2012-03-05 17:41:20', '%Y-%m-%d %H:%M:%S')
    d2 = datetime.datetime.strptime('2012-03-02 17:41:20', '%Y-%m-%d %H:%M:%S')
    delta = d1 - d2
    print delta.days
    ```