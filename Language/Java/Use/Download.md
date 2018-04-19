# 下载文件
```java
import javax.servlet.http.HttpServletResponse;
import java.io.*;

public class DownloadFile {
    public static void loadFile(HttpServletResponse res, String path, String filename) throws Exception {

        String fileName =new String(filename.getBytes("utf-8"), "ISO_8859_1");
        res.setHeader("content-type", "application/octet-stream");
        res.setContentType("application/octet-stream");
        res.setHeader("Content-Disposition", "attachment;filename=" + fileName);
        OutputStream os = null;
        BufferedInputStream bis = null;
        try {
            os = res.getOutputStream();
            File file = new File(path + System.getProperty("file.separator") + filename);
            if(!file.isFile()){
                throw new RuntimeException("文件路径不存在");
            }
            InputStream inputStream = new FileInputStream(file);
            byte[] buffer = new byte[inputStream.available()];
            bis = new BufferedInputStream(inputStream);
            int i = bis.read(buffer);
            while (i != -1) {
                os.write(buffer, 0, buffer.length);
                os.flush();
                i = bis.read(buffer);
            }
        }finally {
            if(bis!=null){
                bis.close();
            }
        }
    }
}
```