# CIFAR-10 训练实测：图融合让推理提速

在 cuda:0 上跑一遍完整的 CIFAR-10 流程，从数据准备到模型收敛，再对比普通 CUDA 推理和经过节点融合、图优化后的加速推理。整个实验用到的训练集放在 ./data 下，开启自动下载并统一做 transform 预处理；训练 DataLoader 采用 batch 128、shuffle 开启、2 个工作进程，测试 DataLoader 采用 batch 100、shuffle 关闭、2 个工作进程。

模型选用 SimpleCNN，损失函数是 CrossEntropyLoss，优化器用 Adam，学习率设为 0.001。训练阶段先打印提示，随后记录开始时间，调用 train_model 跑 5 个 epoch，结束后输出耗时。紧接着进入评测阶段，调用 test_model 在测试集上做一次常规评估。

随后开始推理对比。首先是融合加速路径，打印提示后计时，用 forward_fused_cuda 对一张 1×3×32×32 的随机张量做前向，记录融合推理时长。再进行普通 CUDA 路径，同样计时，用原始 model 做前向得到基准耗时。最后用基准除以融合耗时得到加速倍数并打印。

准确率的统计使用融合推理方式遍历 testloader，全程 torch.no_grad。每次取出一批图片和标签送入 forward_fused_cuda，取最大值索引为预测，累加总样本数和正确数。统计结束后输出融合模型在测试集上的准确率。

为了直观展示效果，取测试集第一批图片，分别用融合推理和普通推理得到预测，前 10 个样本的真实标签、融合预测结果、普通预测结果分别打印出来。

[PRE0]python
correct += (predicted == labels).sum().item()
print(f"节点融合与图优化后的测试准确率: {100*correct/total:.2f}%")
# 展示部分结果
print("展示部分推理结果:")
test_images, test_labels = next(iter(testloader))
outputs_fused = forward_fused_cuda(model, test_images)
_, predicted_fused = torch.max(outputs_fused.data, 1)
print("真实标签:", test_labels[:10].cpu().numpy())
print("融合后预测:", predicted_fused[:10].cpu().numpy())
# 展示普通 CUDA 推理结果
outputs_normal = model(test_images)
_, predicted_normal = torch.max(outputs_normal.data, 1)
print("普通 CUDA 预测:", predicted_normal[:10].cpu().numpy())
if __name__ == "__main__":
    main()
[PRE1]

使用设备：cuda:0

加载 CIFAR-10 数据集

下载 CIFAR-10 数据集