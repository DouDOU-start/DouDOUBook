# DIMSE

> 引言：DICOM DIMSE (DICOM Message Service Element) 是DICOM协议中的一个重要部分，它定义了用于在医疗设备和系统之间传输图像和数据的消息格式和通信规范，DIMSE定义了一组不同的服务，每个服务都有特定的目的和功能。

以下是DICOM DIMSE的一些常见服务和详解：

1. C-ECHO (Confirmation-echo) 服务：
   - 功能：用于验证与远程设备的连接是否正常工作。
   - 过程：一个设备发送C-ECHO请求消息，远程设备收到请求后，返回一个C-ECHO响应消息。如果远程设备成功收到并处理了请求，就会发送一个肯定的响应。

2. C-STORE (Confirmation-store) 服务：
   - 功能：用于将图像或数据从一个设备传输到另一个设备
   - 过程：发送设备将C-STORE请求消息发送给接收设备，接收设备返回一个C-STORE响应消息以确认接收和存储。

3. C-FIND（Confirmation-find）服务：
   - 功能：用于根据特定的查询条件从远程设备检索图像或数据。
   - 过程：发送设备发送C-FIND请求消息，接收设备根据查询条件搜索数据，然后返回一个包含查询结果的C-FIND响应消息。

4. C-MOVE（Confirmation-move）服务：
   - 功能：用于从一个设备检索图像或数据，并将其传输到另一个设备。
   - 过程：发送设备发送C-MOVE请求消息，接收设备执行数据移动操作，并返回C-MOVE响应消息以指示操作的状态。

5. C-GET（Confirmation-get）服务：
   - 功能：类似于C-MOVE，用于从一个设备检索图像或数据，但不进行实际的数据移动，而是将数据发送给请求设备。
   - 过程：发送设备发送C-GET请求消息，接收设备执行数据检索操作，并将数据发送回发送设备。

6. C-RETRIEVE（Confirmation-retrieve）服务：
   - 功能：用于从远程设备检索图像或数据，类似于C-FIND。
   - 过程：发送设备发送C-RETRIEVE请求消息，接收设备根据查询条件搜索数据，然后返回一个包含检索结果的C-RETRIEVE响应消息。