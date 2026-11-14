```
# 前向计算的过程
predicts,train_acc = model(X,y)

# 计算损失，取一个批次样本损失的平均值
loss = F.cross_entropy(predicts, y)
avg_loss = paddle.mean(loss)

test_acc = evaluate_accuracy(test_loader,model)

# 每训练 10 个批次的数据，输出当前损失的情况
if batch_id % 10 == 0:
    print("epoch: {}, batch: {}, loss is: {},train_acc is: {},test_acc is:{}"
    .format(epoch_id, batch_id, avg_loss.numpy(), train_acc.numpy(),test_acc.numpy()))

# 反向传播，更新参数的过程
avg_loss.backward()
# 最小化损失，更新参数
opt.step()
# 清除梯度
opt.clear_grad()

# 保存模型参数
paddle.save(model.state_dict(),'./mnist.pdparams')
```

在本项目中，通过对计算出来的损失求梯度，再根据梯度值逐层反向传播，所谓反向传播就是将模型损失求得的梯度进行回传，用以更新权重的过程。最后通过更新模型中的参数来实现“深度学习”。这些微分反向传播的过程都已经被 PaddlePaddle 框架封装完毕，使用的时候只需要仿照上面的代码进行编写即可完成这些复杂的计算过程。

# 步骤 5：模型训练

现在可以输入下面的代码，执行设计完成的深度学习模型。

```
model = NN_paddle(36,2)
train_data,test_data = train_test_split(data, 0.7)
train(model,train_data,test_data,'Success',128,1e-3,False)
```

输出结果如下。

```
epoch: 0, batch: 0, loss is: [0.91181725],train_acc is: [0.3828125],test_acc is:[0.16267641]
epoch: 1, batch: 0, loss is: [0.57115096],train_acc is: [0.75],test_acc is:[0.86479336]
epoch: 2, batch: 0, loss is: [0.496221],train_acc is: [0.8203125],test_acc is:[0.83732355]
...
```