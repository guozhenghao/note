# Beego中基础文件
## baseController
baseController是controller中的基础，所有其他的controller结构体在声明时，其中全包含这个base
````
package controllers

import (
	"bytes"
	"encoding/gob"
	"fmt"
	"xs-dataeye-server/g"

	"github.com/astaxie/beego"

	_ "github.com/astaxie/beego/cache"
	_ "github.com/astaxie/beego/session"
    //如果用redis的session的时候添加
	_ "github.com/astaxie/beego/session/redis"

	"xs-dataeye-server/models/user"
)

type BaseController struct {
	beego.Controller
	//go的json包有bug,本身是json的字符串，再次转换，会出问题。
	//因此， 本身是json的字符串不走自动打包的返回方式
	NotAutoJson bool
	bJsonp      bool
	result      g.JsonRet
}

const (
	ErrJson = `{"result":false,"error":"","data":null}`
	OkJson  = `{"result":true,"error":"","data":`
)

var (
	runModel = "" //程序运行模式
)

func init() {
	gob.Register(new(user.UserInfo))
	runModel = beego.AppConfig.String("RunMode")
}

func (this *BaseController) Prepare() {
	callback := this.GetString("callback")
	if callback != "" {
		this.bJsonp = true
	}
}

func (this *BaseController) Finish() {
	w := this.Ctx.ResponseWriter
	w.Header().Add("Access-Control-Allow-Origin", "*")
	if this.NotAutoJson {
		if this.result.Result {
			str := this.result.Data.(string)
			buf := bytes.Buffer{}
			buf.Grow(len(str) + 128)
			buf.WriteString(OkJson)
			buf.WriteString(str)
			buf.WriteString("}")
			this.Ctx.Output.ContentType(".json")
			this.Ctx.WriteString(buf.String())

		} else {
			this.Ctx.Output.ContentType(".json")
			this.Ctx.WriteString(ErrJson)
		}
	} else {
		if this.bJsonp {
			this.Data["jsonp"] = this.result
			this.ServeJSONP()
		} else {
			this.Data["json"] = this.result
			this.ServeJSON()
		}
	}
}

//设置返回错误格式
func (this *BaseController) SetError(err string) {
	this.result.Err = err
	this.result.Result = false
}

//设置带有调试信息的错误
//有些错误信息，如数据库操作错误，不应在正式发布后，返回给前端
func (this *BaseController) SetErrorWithDebug(err string, debugInfo string) {
	errInfo := err
	if runModel == "dev" {
		errInfo = fmt.Sprintf("err:%s  debug:%s", err, debugInfo)
	}

	this.result.Err = errInfo
	this.result.Result = false
}

//设置成功返回数据格式
func (this *BaseController) SetData(data interface{}) {
	this.NotAutoJson = false
	this.result.Data = data
	this.result.Result = true
}

````

## globle
globle中包含一些常用的配置等
````
package g

import (
	"crypto/md5"
	"encoding/hex"
	"fmt"
	"os"
	"path/filepath"

	"xs-dataeye-server/utils"

	"github.com/astaxie/beego"
	"github.com/astaxie/beego/logs"
)

type JsonRet struct {
	Result bool        `json:"result"`
	Err    string      `json:"error"`
	Data   interface{} `json:"data"`
}

const (
	LOG_CONSOLE   int    = 0
	LOG_FILE      int    = 1
	LOG_FILE_NAME string = "log/app.log"
)

var (
	Log     *logs.BeeLogger
	IdWoker *utils.IdWorker
)

func init() {
	Log = logs.NewLogger(5000)

	logtype, err := beego.AppConfig.Int("logtype")
	if err != nil {
		fmt.Println(err.Error())
		logtype = LOG_CONSOLE
	}

	if logtype == LOG_FILE {
		logfile := beego.AppConfig.String("logfile")
		if len(logfile) == 0 {
			logfile = LOG_FILE_NAME
		}

		dirPath := filepath.Dir(logfile)
		if len(dirPath) > 0 {
			os.MkdirAll(dirPath, 0777)
		}

		Log.SetLogger("file", fmt.Sprintf(`{"filename":"%s"}`, logfile))

	} else {
		Log.SetLogger("console", "")
	}

	Log.SetLevel(logs.LevelDebug)
	Log.EnableFuncCallDepth(true)

	//初始化唯一ID生成器
	cluster_node, err := beego.AppConfig.Int64("cluster_node")
	if err != nil {
		cluster_node = 1
	}

	IdWoker, err = utils.NewIdWorker(cluster_node)
	if err != nil {
		fmt.Println(err.Error())
	}
}

//判断路径是否存在
func PathExists(path string) (bool, error) {
	_, err := os.Stat(path)
	if err == nil {
		return true, nil
	}
	if os.IsNotExist(err) {
		return false, nil
	}
	return false, err
}

func GetMD5(content string) string {
	hash := md5.New()
	hash.Write([]byte(content))
	return hex.EncodeToString(hash.Sum(nil))
}

````