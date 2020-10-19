SpringCloud Sidecar
---
1. sidecar是为了方便JVM直接调用Non-JVM应用

# 1. POST不支持
1. 请务必注意被调用服务是不是支持通过POST的方式完成调用，比如flask没有写的话默认是只支持Get