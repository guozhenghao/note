# Go语言发送邮件
```go
package utils

import (
	"net/smtp"
	"strings"
	"github.com/astaxie/beego"
)

//发送邮件
func SendMail(to_mail, subject, body string) error {
	send_mail := "from邮箱地址"
	send_password := "from邮箱密码"
	send_host := "看自己邮箱，如smtp.exmail.qq.com:25"
	hp := strings.Split(send_host, ":")
	auth := smtp.PlainAuth("", send_mail, send_password, hp[0])
	content_type := "Content-Type: text/" + "html" + "; charset=UTF-8"
	msg := []byte("To: " + to_mail + "\r\nFrom: " + send_mail + "<" + send_mail + ">\r\nSubject: " + subject + "\r\n" + content_type + "\r\n\r\n" + body)
	send_to := strings.Split(to_mail, ";")
	err := smtp.SendMail(send_host, auth, send_mail, send_to, msg)
	return err
}
```