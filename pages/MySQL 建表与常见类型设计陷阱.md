- `CHAR(N)`和`VARCHAR(N)`的`N`代表字符的长度，CHAR用于保存固定长度的字符，N的范围是0-255，VARCHAR用于保存变长字符，N的范围为0-65536。
  logseq.order-list-type:: number
- 通常建议把字符集设置为`UTF8MB4`，可以保存emoji表情字符
  logseq.order-list-type:: number
- 不建议使用数据库的枚举类型，建议在工程代码中维护枚举类型，更便于维护扩展
  logseq.order-list-type:: number
- INT类型占用4个字节，范围为-2147483648 ~ 2147483647（2^31），BIGINT占用8字节，-9223372036854775808 ~9223372036854775807（2^63）
  logseq.order-list-type:: number
- ID一般设置为自增，同时一般将主键设置为BIGINT类型，主要是可能INT类型取值范围不能够适应互联网场景的增速
  logseq.order-list-type:: number
- 金融字段推荐使用`DECIMAL`类型，DECIMAL(M,N)，M代有效数字的位数，N代表小数点后的位数
  logseq.order-list-type:: number
- 日期时间类型推荐使用`DATETIME`，而非`TIMESTAMEP`，因为后者保存的是毫秒数，只占用四个字节，其存储的时间上限只能到"`2038-01-19 03:14:07`"
  logseq.order-list-type:: number
- 每个表都要有一个时间字段，开发人员可以知道每次操作记录更新的时间，以便做后续的处理。也可能会有一些其他业务含义。
  logseq.order-list-type:: number