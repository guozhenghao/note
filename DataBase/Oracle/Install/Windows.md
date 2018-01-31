# Oracle数据库 Windows安装
- 下载

    `http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html`

    file1和file2都需要下载，解压后放到同一个文件夹下(或将file2中的文件，放入对应的file1文件夹中，版本不同，file2中的文件不一样，视情况而定)，然后在安装

- 安装
    - 如果提示32/64版本问题，可以在database/stage/cvu下的cvu_prereq.xml中，添加如下代码(在<CERTIFIED_SYSTEMS>标签下)
        ````
        <OPERATING_SYSTEM RELEASE="6.2">
            <VERSION VALUE="3"/>
            <ARCHITECTURE VALUE="64-bit"/>
            <NAME VALUE="Windows 10"/>
            <ENV_VAR_LIST>
                <ENV_VAR NAME="PATH" MAX_LENGTH="1023" />
            </ENV_VAR_LIST>
        </OPERATING_SYSTEM>
        ````

    - 如果提示path长度问题
        - 方法一: 删除path中无用的路径
        - 方法二: database/stage/cvu下的cvu_prereq.xml中，将所用的MAX_LENGTH值调高
