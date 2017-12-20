# Beego文件保存或入库
### 文件保存
````
fs, header, err := this.GetFile(filekey)
if err != nil {
    return
}

ext := filepath.Ext(header.Filename)
fileName := strings.TrimSuffix(header.Filename, ext)
if ext != FILE_EXT {
    return
}

var fileSize float64
if sizeInterface, ok := fs.(Sizer); ok {
    fsSize := sizeInterface.Size()
    fileSize = float64(fsSize) / 1024.0

    if fileSize > FILE_MAX_SIZE {
        return
    }
}

//存储目录不存在，自动创建
ret, err := g.PathExists(TEMP_STOR_DIR)
if !ret || err != nil {
    err = os.MkdirAll(TEMP_STOR_DIR, 0777)
    if err != nil {
        return
    }
}

fileId, err := g.IdWoker.NextId()
if err != nil {
    return
}

//保存路径
dirPath := fmt.Sprintf("%s%v%s", TEMP_STOR_DIR, fileId, ext)
//保存
err = this.SaveToFile(filekey, dirPath)
if err != nil {
    return
}

````

### 文件入库
````
fs, header, err := this.GetFile(filekey)
content, err := ioutil.ReadAll(fs)
````