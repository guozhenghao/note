# Beego Session
session可以存在内存或者数据库中，记录内存和redis中的用法
- 无论是那种，都要在basecontroller中进行导包等设置([base写法样例传送门](/Language/Go/Frame/Beego/Base&Json.md))
### 内存
- 配置文件中内容
````
#session设置
SessionOn = true
SessionProvider = memory

#session连接设置
#SessionProviderConfig

#session过期时间 1小时
SessionGCMaxLifetime =3600
````

- 使用

登陆时
````
// 分配sessionId
sessionId, err := 自己写个id的方法
// this是本controller
this.SetSession(sessionId, 信息等)
````

session验证方法
````
//我建了一个basecontroller，所有其他的controller结构体中都包含这个base
//userinfo是user信息的结构体，包名是user，所有大写是自己设置的错误常量
func Authentication(controller *BaseController, userid string, sessionId string) (bool, error) {
	if controller == nil || userid == "" || sessionId == "" {
		return false, errors.New(AUTHORITY_PARAM_ERROR)
	}

	sessionInfo := controller.GetSession(sessionId)
	if sessionInfo == nil {
		logAuthFailedInfo(controller)
		return false, errors.New(SESSION_ID_ERROR)
	}

	userInfo, ok := sessionInfo.(*user.UserInfo)
	if !ok || userInfo == nil || userid != userInfo.Code {
		logAuthFailedInfo(controller)
		return false, errors.New(SESSION_ID_ERROR)
	}

	return true, nil
}
````

其他controller中做session验证
````
// this是本controller
_, err := controllers.Authentication(&this.BaseController, userCode, sessionId)
	if err != nil {
		this.SetError(err.Error())
		g.Log.Error("[das_panel|delete]:%s", err.Error())
		return
	}
````

### redis

配置文件

````
#redis地址,连接池个数,redis密码
SessionProviderConfig=ip:port,4096,password

#hash key
SessionHashKey = dataeyesession

#session过期时间 1小时
SessionGCMaxLifetime =3600
````