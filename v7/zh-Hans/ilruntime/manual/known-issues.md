# 已知问题

热更工程的入口方法如果使用`UniTask`返回值，将无法自动捕获热更工程中的异常。而使用`Task`返回值时，需要生成CLR绑定代码才可捕获异常。