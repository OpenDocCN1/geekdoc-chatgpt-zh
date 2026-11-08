Vulkan 双队列并行从实例到同步的落地思路

在开始任何绘制或计算之前，先弄清楚物理设备到底暴露了哪些队列族。因为后续要分开提交图形和计算，必须先知道队列族的数量，再用 vkGetPhysicalDeviceQueueFamilyProperties 把属性拉取到本地的 vector 里存好，为后面的索引查找做准备。

[PRE0]
接下来搭一个最小可用的 Vulkan 实例。构造 VkApplicationInfo 时把应用名写成 ParallelGraphicsCompute，应用版本设为 1.0.0，引擎名写成 NoEngine，引擎版本同样是 1.0.0，API 版本锁定在 VK_API_VERSION_1_1。实例创建信息使用默认的 VK_STRUCTURE_TYPE_INSTANCE_CREATE_INFO，并把上面的应用信息挂进去。调用 vkCreateInstance 后立刻用 assert 校验返回是否为 VK_SUCCESS，失败就直接中断。

[PRE1]
实例就位后要找到可以跑的 GPU。先用空指针两次查询物理设备数量，第一次拿到 deviceCount，确认大于 0 否则报错，第二次用分配好的 vector 接收真正的 VkPhysicalDevice 数组。为了演示方便，直接取列表里的第一块设备作为本次使用的 physicalDevice。

[PRE2]
有了设备，就要分别定位能跑图形和能跑计算的队列族。调用 findQueueFamilyIndex 传入 VK_QUEUE_GRAPHICS_BIT 得到图形队列族索引，再用 VK_QUEUE_COMPUTE_BIT 得到计算队列族索引，后面的创建逻辑会根据两者是否相同来决定建一个还是两个队列。

[PRE3]
进入逻辑设备的创建阶段。先准备一个优先级 1.0f 的队列优先级。按需组装 VkDeviceQueueCreateInfo，先放图形队列的描述，familyIndex、queueCount、优先级全部填好并 push 进列表。如果计算队列族和图形队列族不是同一个，再单独构造一个计算队列的创建信息也推入列表。用这些信息填充 VkDeviceCreateInfo，sType 设为 VK_STRUCTURE_TYPE_DEVICE_CREATE_INFO，queueCreateInfoCount 和指针指向列表。vkCreateDevice 成功后用 assert 保证。随后分别用 vkGetDeviceQueue 取出 graphicsQueue 和 computeQueue 的句柄，供后续提交使用。

[PRE4]
演示计算队列操作需要一块缓冲区。元素个数取 32，缓冲区大小就是 32 倍的 int。创建信息里 sType 为 VK_STRUCTURE_TYPE_BUFFER_CREATE_INFO，size、usage 同时包含 VK_BUFFER_USAGE_STORAGE_BUFFER_BIT、VK_BUFFER_USAGE_TRANSFER_SRC_BIT、VK_BUFFER_USAGE_TRANSFER_DST_BIT，sharingMode 用 EXCLUSIVE。创建成功后查询内存需求，分配信息里 sType 为 VK_STRUCTURE_TYPE_MEMORY_ALLOCATE_INFO，allocationSize 用查询到的大小，memoryTypeIndex 这里做了简化直接写 0。分配完内存后把缓冲区绑定到该内存地址 0 处。

[PRE5]
绑定完成后把初始数据写进去。先 vkMapMemory 映射整个 bufferSize，得到 void* 指针并强转为 int*，循环 0 到 31 把每个元素写成对应的序号。写完立刻 vkUnmapMemory 释放映射，表示主机侧已完成初始化。

[PRE6]
命令录制需要命令池，先为图形队列创建一个池，poolCreateInfo 的 sType 为 VK_STRUCTURE_TYPE_COMMAND_POOL_CREATE_INFO，queueFamilyIndex 指定为图形族索引，创建得到 graphicsCmdPool。紧接着复用同样的结构，把 queueFamilyIndex 换成计算族索引，再建一个 computeCmdPool。

[PRE7]
有了池子就分配主命令缓冲区。allocInfo 的 sType 为 VK_STRUCTURE_TYPE_COMMAND_BUFFER_ALLOCATE_INFO，commandBufferCount 为 1，level 为 VK_COMMAND_BUFFER_LEVEL_PRIMARY。先让 commandPool 指向 graphicsCmdPool 分配得到 graphicsCmdBuffer，随后把 commandPool 换成 computeCmdPool 再次分配得到 computeCmdBuffer。

[PRE8]
图形命令的录制在这里做占位演示。beginInfo 的 sType 为 VK_STRUCTURE_TYPE_COMMAND_BUFFER_BEGIN_INFO，vkBeginCommandBuffer 启动 graphicsCmdBuffer。实际项目里这里会接 vkCmdBeginRenderPass、vkCmdBindPipeline 等一系列绘制调用，此处仅模拟一次绘制的流程，结束时调用 vkEndCommandBuffer。

[PRE9]
计算侧的命令缓冲区也开始录制。同样用 beginInfo 开始 computeCmdBuffer。没有真实的计算着色器，这里用一个内存屏障来演示计算写入后主机可读的依赖关系。memBarrier 的 sType 为 VK_STRUCTURE_TYPE_MEMORY_BARRIER，srcAccessMask 设 VK_ACCESS_SHADER_WRITE_BIT，dstAccessMask 设 VK_ACCESS_HOST_READ_BIT。通过 vkCmdPipelineBarrier 把阶段从 VK_PIPELINE_STAGE_COMPUTE_SHADER_BIT 推进到 VK_PIPELINE_STAGE_HOST_BIT，完成一次屏障插入后结束命令缓冲。

[PRE10]
最后补上同步对象。图形命令完成后需要通知计算开始，因此创建一个 VK_STRUCTURE_TYPE_SEMAPHORE_CREATE_INFO 的信号量得到 graphicsFinishSemaphore。计算完成后要通知主机等待，所以创建一个 VK_STRUCTURE_TYPE_FENCE_CREATE_INFO 的栅栏，flags 保持 0 表示初始未发出信号，得到 computeFinishFence。两者配合就能实现图形到计算再到主机的有序执行。