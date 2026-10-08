# BT03 - Cấu hình tường lửa UFW và chuẩn đoán cổng mạng (UFW Firewall and Port Diagnostics)

## 1. Cấu hình chính sách mặc định

```bash
devops@0310263186:~$ sudo ufw default deny incoming
Default incoming policy changed to 'deny'
(be sure to update your rules accordingly)

devops@0310263186:~$ sudo ufw default allow outgoing
Default outgoing policy changed to 'allow'
(be sure to update your rules accordingly)
```

## 2. Mở cổng SSH và cổng ứng dụng Web

```bash
devops@0310263186:~$ sudo ufw allow 22/tcp
Rules updated
Rules updated (v6)

devops@0310263186:~$ sudo ufw allow 8080/tcp
Rules updated
Rules updated (v6)
```

## 3. Kích hoạt tường lửa

```bash
devops@0310263186:~$ sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
```

## 4. Kiểm tra trạng thái tường lửa

```bash
devops@0310263186:~$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
8080/tcp                   ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
8080/tcp (v6)              ALLOW IN    Anywhere (v6)
```

## 5. Kiểm tra cổng đang lắng nghe

Khởi chạy ứng dụng web mẫu lắng nghe tại cổng 8080:

```bash
devops@0310263186:~$ cd /var/www/my-app/public
devops@0310263186:/var/www/my-app/public$ nohup python3 -m http.server 8080 > /dev/null 2>&1 &
[1] 4211
```

```bash
devops@0310263186:~$ sudo ss -tlnp
State  Recv-Q Send-Q Local Address:Port  Peer Address:Port  Process
LISTEN 0      128          0.0.0.0:22         0.0.0.0:*      users:(("sshd",pid=812,fd=3))
LISTEN 0      128          0.0.0.0:8080       0.0.0.0:*      users:(("python3",pid=4211,fd=3))
LISTEN 0      128             [::]:22            [::]:*      users:(("sshd",pid=812,fd=4))
```

## 6. Kiểm tra kết nối tới cổng đã mở

```bash
devops@0310263186:~$ curl -sI http://localhost:8080
HTTP/1.0 200 OK
Server: SimpleHTTP/0.6 Python/3.12.3
Date: Tue, 06 Oct 2026 09:26:14 GMT
Content-type: text/html; charset=utf-8
Content-Length: 322

thinh@local:~$ curl -sI http://160.30.160.103:8080
HTTP/1.0 200 OK
Server: SimpleHTTP/0.6 Python/3.12.3
```

Cổng 8080 đã được mở qua UFW và truy cập được từ bên ngoài.
