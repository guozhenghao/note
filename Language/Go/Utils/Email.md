# Go语言发送邮件
以QQ企业邮箱为例
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
发送邮件使用SMTP25端口，本地等运行无误，但是放到线上服务器便使用失败。原因是因为线上服务器大多将25端口进行屏蔽，根据一堆发布的网络规范，均对垃圾邮件进行管理和规范，且大多服务器的该端口无法申请开放。所以使用SSL加密通过465或587端口进行发送。
```go
import "gopkg.in/gomail.v2"

func NewSendMail() error{
	m := gomail.NewMessage()
	m.SetHeader("From", "from邮箱地址")
	m.SetHeader("To", "send邮箱地址")
	m.SetAddressHeader("Cc", "抄送邮箱地址", "抄送邮箱的别名")
	m.SetHeader("Subject", "邮件标题")
	m.SetBody("text/html","邮件正文")

	d := gomail.NewDialer("smtp.exmail.qq.com", 465, "user", "password")
	if err := d.DialAndSend(m); err != nil {
		return err
	}
	return nil
}
```