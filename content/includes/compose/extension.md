扩展（Extensions）可以使你的 Compose 文件更高效且更易维护。

使用前缀 `x-` 作为顶级元素来模块化你想要重用的配置。
Compose 会忽略任何以 `x-` 开头的字段，这是 Compose 静默忽略未识别字段的唯一例外。