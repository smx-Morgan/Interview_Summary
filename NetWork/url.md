**从输入url到显示网页到关闭网页会经历哪些步骤**

[浏览器通过 **DNS解析** 获取服务器的 IP 地址]

[浏览器和服务器通过 **TCP三次握手** 建立连接]

[浏览器向服务器发送 **HTTP请求**，包含请求方法、URL、头部和正文

服务器接收请求，根据请求内容进行处理，并返回 **HTTP响应**，包含状态码、头部和正文]

浏览器接收响应，根据响应内容进行解析，并显示页面

[浏览器和服务器通过 **TCP四次挥手** 关闭连接