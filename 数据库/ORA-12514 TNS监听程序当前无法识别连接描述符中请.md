[解决方法](https://www.bbsmax.com/A/nAJvK34w5r/)

若Oracle出现“监听程序当前无法识别连接描述符中请求的服务”这个错误可以按照以下方法解决：

可以通过这个路径找到一个文本文件： oracle\product\10.2.0\db_1\NETWORK\ADMIN\listener.ora 还一个在同路径下的tnsnames.ora文件，这两个文本中的一些信息要是这样的： listener.ora # listener.ora Network Configuration File: F:\oracle\product\10.2.0\db_1\network\admin\listener.ora # Generated by Oracle configuration tools.
SID_LIST_LISTENER = (SID_LIST =   (SID_DESC =    (SID_NAME = PLSExtProc)    (ORACLE_HOME = F:\oracle\product\10.2.0\db_1)    (PROGRAM = extproc)   )    (SID_DESC =    (GLOBAL_DBNAME = orcl)    (ORACLE_HOME = F:\oracle\product\10.2.0\db_1)    (SID_NAME = orcl)   )
)
LISTENER = (DESCRIPTION_LIST =   (DESCRIPTION =    (ADDRESS = (PROTOCOL = TCP)(HOST = 127.0.0.1)(PORT = 1521))   ) )
出现“监听程序当前无法识别连接描述符中请求的服务”一般是因为缺少了上面中间位置的

(SID_DESC =    (GLOBAL_DBNAME = orcl)    (ORACLE_HOME = F:\oracle\product\10.2.0\db_1)    (SID_NAME = orcl)   ) 只要把它加在上面就ＯＫ了！！！！！！！！！！！！！
tnsnames.ora # tnsnames.ora Network Configuration File: F:\oracle\product\10.2.0\db_1\network\admin\tnsnames.ora # Generated by Oracle configuration tools.
ORCL = (DESCRIPTION =   (ADDRESS = (PROTOCOL = TCP)(HOST = 127.0.0.1)(PORT = 1521))   (CONNECT_DATA =    (SERVER = DEDICATED)    (SERVICE_NAME = orcl)   ) )
EXTPROC_CONNECTION_DATA = (DESCRIPTION =   (ADDRESS_LIST =    (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1))   )   (CONNECT_DATA =    (SID = PLSExtProc)    (PRESENTATION = RO)   ) )