# Go语言二维码相关操作
### 创建二维码
- 方法1
````
package main

import (
	"image/png"
	"os"

	"github.com/boombuler/barcode"
	"github.com/boombuler/barcode/qr"
)

func main() {
	//创建二维码
	qrCode, _ := qr.Encode("http://www.baidu.com", qr.M, qr.Auto)

	//设置尺寸 to 200x200 pixels
	qrCode, _ = barcode.Scale(qrCode, 200, 200)

	// 创建文件
	file, _ := os.Create("qrcode.png")
	defer file.Close()

	// 将二维码写入文件
	png.Encode(file, qrCode)
}
````
- 方法2
````
package main

import (
	qrcode "github.com/skip2/go-qrcode"
	"fmt"
)

func main() {
    // 创建二维码
	png, _ := qrcode.Encode("https://www.baidu.com", qrcode.Medium, 256)
	fmt.Print(png)
    // 创建二维码并将其写入文件
	qrcode.WriteFile("https://www.baidu.com", qrcode.Medium, 256, "qr.png")
}
````

### 识别二维码
````
package main

import (
	"github.com/tuotoo/qrcode"
	"os"
	"fmt"
)

func main() {
    //读取文件
	fi, _ := os.Open("qr.png")
	defer fi.Close()
    //解析
	qrmatrix, _ := qrcode.Decode(fi)
	fmt.Print(qrmatrix.Content)
}
````